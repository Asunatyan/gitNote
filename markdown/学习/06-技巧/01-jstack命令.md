

# jstack用法

```
/opt/java8/bin/jstack

Usage:
    jstack [-l] <pid>
        (to connect to running process) 连接活动线程
    jstack -F [-m] [-l] <pid>
        (to connect to a hung process) 连接阻塞线程
    jstack [-m] [-l] <executable> <core>
        (to connect to a core file) 连接dump的文件
    jstack [-m] [-l] [server_id@]<remote server IP or hostname>
        (to connect to a remote debug server) 连接远程服务器

Options:
    -F  to force a thread dump. Use when jstack <pid> does not respond (process is hung)
    -m  to print both java and native frames (mixed mode)
    -l  long listing. Prints additional information about locks 长清单。打印关于锁的附加信息

    -h or -help to print this help message
```

# jstack查看输出



## jstack统计线程数

```
/opt/java8/bin/jstack -l 28367 | grep 'java.lang.Thread.State' | wc -l
```

