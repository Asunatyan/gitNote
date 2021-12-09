## Java 内存模型

所以，为了**解决可见性和有序性问题**，只需要提供给程序员按需禁用缓存和编译优化的方法即可。(**没有解决原子性那么 "可能" 会有并发的问题)**

Java 内存模型规范了 JVM 如何提供按需禁用缓存和编译优化的方法。

具体来说，这些方法包括 volatile、synchronized 和 final 三个关键字，以及六项 Happens-Before 规则

## Happens-Before 规则

- 程序的顺序性规则

  - 在一个线程中，按照程序顺序，前面的操作 Happens-Before 于后续的任意操作

  - 程序前面对某个变量的修改一定是对后续操作可见的。

  - **这里不会发生指令重重排吗？**

    - 重排序需要遵守happens-before规则

    - 程序顺序规则中所说的每个操作happens-before于该线程中的任意后续操作并不是说前一个操作必须要在后一个操作之前执行，而是指前一个操作的执行结果必须对后一个操作可见，如果不满足这个要求那就不允许这两个操作进行重排序

    - 两个操作之间存在happens-before关系，并不意味着一定要按照happens-before原则制定的顺序来执行。如果重排序之后的执行结果与按照happens-before关系来执行的结果一致，那么这种重排序并不非法。

    - 可以重排，但是要保证符合Happens-Before规则，Happens-Before规则关注的是可见性，
      x=5;
      y=6;
      z=x+y;
      上面的代码重排成这样：
      y=6;
      x=5;
      z=x+y;
      也是可以的。

      所谓顺序，指的是你可以用顺序的方式推演程序的执行，但是程序的执行不一定是完全顺序的。编译器保证结果一定 == 顺序方式推演的结果

      这几条规则，都是告诉你，可以按照这个规则推演程序的执行。但是编译怎么优化，那就百花齐放了。

    - ```
      public double rectangleArea(double length , double width){
      double leng;
      double wid;
      leng=length;//A
      wid=width;//B
      double area=leng*wid;//C
      return area;
      }
      ```

    - 因为A happens-before B，所以A操作产生的结果leng一定要对B操作可见，但是现在B操作并没有用到length，所以这两个操作可以重排序，那A操作是否可以和C操作重排序呢，如果A操作和C操作进行了重排序，因为leng没有被赋值，所以leng=0，area=0*wid也就是area=0；这个结果显然是错误的，所以A操作是不能和C操作进行重排序的（这就是注2中说的前一个操作的执行结果必须对后羿操作可见，如果不满足这个要求就不允许这两个操作进行重排序）

- volatile 变量规则

  - 一个 volatile 变量的写操作， Happens-Before 于后续对这个 volatile 变量的读操作。
    - ????
    - volatile强制所修饰的变量及它前边的变量刷新至内存，并且volatile禁止了指令的重排序。
    - volatile的读写前后会插入内存屏障，保证一些操作是无法没重排序的，其中就有对于volatile的写操作之前的动作不会被重排序到之后
    - 作者回复: 别想禁用指令重排的事，就用顺序性规则+传递行规则+volatile规则来推断就可以了
      边界就是只要给volatile赋值成功，那么这个赋值语句之前所有代码的执行结果都对其他线程可见

- 传递性

  - A Happens-Before B，且 B Happens-Before C，那么 A Happens-Before C。

- 管程中锁的规则

  - 一个锁的解锁 Happens-Before 于后续对这个锁的加锁。
    - 使用管程，由于是访问共享变量，如果是在syn中修改值只能保证当前线程下一次进入syn可以看见最新的值，**其他线程直接访问**还可能不是最新值

- 线程 start() 规则

  - 如果线程 A 调用线程 B 的 start() 方法（即在线程 A 中启动线程 B），那么该 start() 操作 Happens-Before 于线程 B 中的任意操作。

- 线程 join() 规则

  - 如果在线程 A 中，调用线程 B 的 join() 并成功返回，那么线程 B 中的任意操作 Happens-Before 于该 join() 操作的返回

- 线程中断规则

  - 对线程interrupt()方法的调用先行发生于被中断线程的代码检测到中断事件的发生，可以通过Thread.interrupted()方法检测到是否有中断发生。



```
逸出：
在构造方法中将this 复制给别的变量，意味着，构造方法还没结束，对象this 就给别人使用了。对象还没初始化完成，别人就使用，类似双层校验创建单例的例子。
逸出要避免，要避免这种写法。
同时，如果在构造方法中对成员变量赋初始值，比如this.x = x，这样的代码在编译的时候，也会被重排序，将赋值操作重排序到构造方法之外，那么也就是，构造方法结束了，初始化完成了，这时候，别的线程先后可能会读取到俩个不同的值，一个没初始化的，一个初始化后的值。要避免这种情况的发送，就要用fianl，final修饰的变量，可以保证，不会被抽排序到构造方法之外，那么就保证了，只要别的线程拿到该对象的引用，那么改对象的fianl修饰的变量，别的线程一定是能读到在构造方法中初始化的值的，避免了上面同一变量，读取值俩次不一样的问题。
```



```
作者你好，试看了两节，实际运行代码与文中描述不符，具体代码如下：
public class Main {
    int x = 0;
    volatile int v = 0;

    public static void main(String[] args) {
        Main m = new Main();
        new Thread(() -> {
            int i = 0;
            while (true){
                m.writer(i);
                i++;
            }
        }).start();
        new Thread(() -> {
            while (true) {
                m.reader();
            }
        }).start();
    }

    public void writer(int i) {
        x = i;
        v = i;
    }

    public void reader() {
        if (v != x) {
            System.out.println("x: " + x + " v: " + v);
        }
    }
}
以上代码还是会输出x与v不相等，这与文中提到的前三条happen-before原则不符合，请老师百忙之余解答
作者回复: 问题不是出在可见性，而是出在原子性上。volatile不能保证writer()和reader()方法的原子性。例如写线程写完x=i之后，睡了1万年；在这1万年之内，读线程读到的v一定不等于x。同时reader() 也没有原子性，读线程读完v之后，也可能睡了10万年，此时v也一定不等于x。这里的睡，就是线程上下文切换的时间，这个时间的长短是不一定的。

你要想验证可见性，可以把reader()代码改成下面这样：
    public void reader() {
        int vv = v;
        int xx = x;
        // volatile能保证 vv<=xx
        if (vv > xx) {
            System.out.println("x: " + xx + " v: " + vv);
        }
    }
```

