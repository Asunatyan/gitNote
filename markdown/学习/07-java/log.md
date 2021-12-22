# log4j2

引入log4j包

```t
<dependency>  
    <groupId>org.springframework.boot</groupId>  
    <artifactId>spring-boot-starter-web</artifactId>  
    <exclusions><!-- 去掉springboot默认配置 -->  
        <exclusion>  
            <groupId>org.springframework.boot</groupId>  
            <artifactId>spring-boot-starter-logging</artifactId>  
        </exclusion>  
    </exclusions>  
</dependency> 

<dependency> <!-- 引入log4j2依赖 -->  
    <groupId>org.springframework.boot</groupId>  
    <artifactId>spring-boot-starter-log4j2</artifactId>  
</dependency> 
```

### 

如果自定义了文件名，需要在application.yml中配置

```text
logging:
  config: xxxx.xml
  level:
    cn.jay.repository: trace
```

默认名log4j2-spring.xml，就省下了在application.yml中配置



```xml
<?xml version="1.0" encoding="UTF-8"?>
<!--Configuration后面的status，这个用于设置log4j2自身内部的信息输出，可以不设置，当设置成trace时，你会看到log4j2内部各种详细输出-->
<!--monitorInterval：Log4j能够自动检测修改配置 文件和重新配置本身，设置间隔秒数-->
<configuration monitorInterval="5">
  <!--日志级别以及优先级排序: OFF > FATAL > ERROR > WARN > INFO > DEBUG > TRACE > ALL -->

  <!--变量配置-->
  <Properties>
    <!-- 格式化输出：%date表示日期，%thread表示线程名，%-5level：级别从左显示5个字符宽度 %msg：日志消息，%n是换行符-->
    <!-- %logger{36} 表示 Logger 名字最长36个字符 -->
    <property name="LOG_PATTERN" value="%date{HH:mm:ss.SSS} [%thread] %-5level %logger{36} - %msg%n" />
    <!-- 定义日志存储的路径 -->
    <property name="FILE_PATH" value="更换为你的日志路径" />
    <property name="FILE_NAME" value="更换为你的项目名" />
  </Properties>

  <appenders>

    <console name="Console" target="SYSTEM_OUT">
      <!--输出日志的格式-->
      <PatternLayout pattern="${LOG_PATTERN}"/>
      <!--控制台只输出level及其以上级别的信息（onMatch），其他的直接拒绝（onMismatch）-->
      <ThresholdFilter level="info" onMatch="ACCEPT" onMismatch="DENY"/>
    </console>

    <!--文件会打印出所有信息，这个log每次运行程序会自动清空，由append属性决定，适合临时测试用-->
    <File name="Filelog" fileName="${FILE_PATH}/test.log" append="false">
      <PatternLayout pattern="${LOG_PATTERN}"/>
    </File>

    <!-- 这个会打印出所有的info及以下级别的信息，每次大小超过size，则这size大小的日志会自动存入按年份-月份建立的文件夹下面并进行压缩，作为存档-->
    <RollingFile name="RollingFileInfo" fileName="${FILE_PATH}/info.log" filePattern="${FILE_PATH}/${FILE_NAME}-INFO-%d{yyyy-MM-dd}_%i.log.gz">
      <!--控制台只输出level及以上级别的信息（onMatch），其他的直接拒绝（onMismatch）-->
      <ThresholdFilter level="info" onMatch="ACCEPT" onMismatch="DENY"/>
      <PatternLayout pattern="${LOG_PATTERN}"/>
      <Policies>
        <!--interval属性用来指定多久滚动一次，默认是1 hour-->
        <TimeBasedTriggeringPolicy interval="1"/>
        <SizeBasedTriggeringPolicy size="10MB"/>
      </Policies>
      <!-- DefaultRolloverStrategy属性如不设置，则默认为最多同一文件夹下7个文件开始覆盖-->
      <DefaultRolloverStrategy max="15"/>
    </RollingFile>

    <!-- 这个会打印出所有的warn及以下级别的信息，每次大小超过size，则这size大小的日志会自动存入按年份-月份建立的文件夹下面并进行压缩，作为存档-->
    <RollingFile name="RollingFileWarn" fileName="${FILE_PATH}/warn.log" filePattern="${FILE_PATH}/${FILE_NAME}-WARN-%d{yyyy-MM-dd}_%i.log.gz">
      <!--控制台只输出level及以上级别的信息（onMatch），其他的直接拒绝（onMismatch）-->
      <ThresholdFilter level="warn" onMatch="ACCEPT" onMismatch="DENY"/>
      <PatternLayout pattern="${LOG_PATTERN}"/>
      <Policies>
        <!--interval属性用来指定多久滚动一次，默认是1 hour-->
        <TimeBasedTriggeringPolicy interval="1"/>
        <SizeBasedTriggeringPolicy size="10MB"/>
      </Policies>
      <!-- DefaultRolloverStrategy属性如不设置，则默认为最多同一文件夹下7个文件开始覆盖-->
      <DefaultRolloverStrategy max="15"/>
    </RollingFile>

    <!-- 这个会打印出所有的error及以下级别的信息，每次大小超过size，则这size大小的日志会自动存入按年份-月份建立的文件夹下面并进行压缩，作为存档-->
    <RollingFile name="RollingFileError" fileName="${FILE_PATH}/error.log" filePattern="${FILE_PATH}/${FILE_NAME}-ERROR-%d{yyyy-MM-dd}_%i.log.gz">
      <!--控制台只输出level及以上级别的信息（onMatch），其他的直接拒绝（onMismatch）-->
      <ThresholdFilter level="error" onMatch="ACCEPT" onMismatch="DENY"/>
      <PatternLayout pattern="${LOG_PATTERN}"/>
      <Policies>
        <!--interval属性用来指定多久滚动一次，默认是1 hour-->
        <TimeBasedTriggeringPolicy interval="1"/>
        <SizeBasedTriggeringPolicy size="10MB"/>
      </Policies>
      <!-- DefaultRolloverStrategy属性如不设置，则默认为最多同一文件夹下7个文件开始覆盖-->
      <DefaultRolloverStrategy max="15"/>
    </RollingFile>

  </appenders>

  <!--Logger节点用来单独指定日志的形式，比如要为指定包下的class指定不同的日志级别等。-->
  <!--然后定义loggers，只有定义了logger并引入的appender，appender才会生效-->
  <loggers>

    <!--过滤掉spring和mybatis的一些无用的DEBUG信息-->
    <logger name="org.mybatis" level="info" additivity="false">
      <AppenderRef ref="Console"/>
    </logger>
    <!--监控系统信息-->
    <!--若是additivity设为false，则 子Logger 只会在自己的appender里输出，而不会在 父Logger 的appender里输出。-->
    <Logger name="org.springframework" level="info" additivity="false">
      <AppenderRef ref="Console"/>
    </Logger>

    <root level="info">
      <appender-ref ref="Console"/>
      <appender-ref ref="Filelog"/>
      <appender-ref ref="RollingFileInfo"/>
      <appender-ref ref="RollingFileWarn"/>
      <appender-ref ref="RollingFileError"/>
    </root>
  </loggers>

</configuration>
```

## **日志级别**

机制：如果一条日志信息的级别大于等于配置文件的级别，就记录。

- trace：追踪，就是程序推进一下，可以写个trace输出
- debug：调试，一般作为最低级别，trace基本不用。
- info：输出重要的信息，使用较多
- warn：警告，有些信息不是错误信息，但也要给程序员一些提示。
- error：错误信息。用的也很多。
- fatal：致命错误。

## **格式**

- SimpleLayout：以简单的形式显示
- HTMLLayout：以HTML表格显示
- PatternLayout：自定义形式显示

PatternLayout自定义日志布局

```text
%d{yyyy-MM-dd HH:mm:ss, SSS} : 日志生产时间,输出到毫秒的时间
%-5level : 输出日志级别，-5表示左对齐并且固定输出5个字符，如果不足在右边补0
%c : logger的名称(%logger)
%t : 输出当前线程名称
%p : 日志输出格式
%m : 日志内容，即 logger.info("message")
%n : 换行符
%C : Java类名(%F)
%L : 行号
%M : 方法名
%l : 输出语句所在的行数, 包括类名、方法名、文件名、行数
hostName : 本地机器名
hostAddress : 本地ip地址
```

## **log4j2配置详解**

### **根节点Configuration**

有两个属性:

- status
- monitorinterval

有两个子节点:

- Appenders
- Loggers(表明可以定义多个Appender和Logger).

status用来指定log4j本身的打印日志的级别.monitorinterval用于指定log4j自动重新配置的监测间隔时间，单位是s,最小是5s.

Appenders节点

- 常见的有三种子节点:Console、RollingFile、File

Console节点用来定义输出到控制台的Appender.

- name:指定Appender的名字.
- target:SYSTEM_OUT 或 SYSTEM_ERR,一般只设置默认:SYSTEM_OUT.
- PatternLayout:输出格式，不设置默认为:%m%n.

File节点用来定义输出到指定位置的文件的Appender.

- name:指定Appender的名字.
- fileName:指定输出日志的目的文件带全路径的文件名.
- PatternLayout:输出格式，不设置默认为:%m%n.

RollingFile节点用来定义超过指定条件自动删除旧的创建新的Appender.

- name:指定Appender的名字.
- fileName:指定输出日志的目的文件带全路径的文件名.
- PatternLayout:输出格式，不设置默认为:%m%n.
- filePattern : 指定当发生Rolling时，文件的转移和重命名规则.
- Policies:指定滚动日志的策略，就是什么时候进行新建日志文件输出日志.
- TimeBasedTriggeringPolicy:Policies子节点，基于时间的滚动策略，interval属性用来指定多久滚动一次，默认是1 hour。modulate=true用来调整时间：比如现在是早上3am，interval是4，那么第一次滚动是在4am，接着是8am，12am...而不是7am.
- SizeBasedTriggeringPolicy:Policies子节点，基于指定文件大小的滚动策略，size属性用来定义每个日志文件的大小.
- DefaultRolloverStrategy:用来指定同一个文件夹下最多有几个日志文件时开始删除最旧的，创建新的(通过max属性)。

### **Loggers节点，常见的有两种:Root和Logger.**

- Root节点用来指定项目的根日志，如果没有单独指定Logger，那么就会默认使用该Root日志输出
- level:日志输出级别，共有8个级别，按照从低到高为：All < Trace < Debug < Info < Warn < Error < AppenderRef：Root的子节点，用来指定该日志输出到哪个Appender.
- Logger节点用来单独指定日志的形式，比如要为指定包下的class指定不同的日志级别等。
- level:日志输出级别，共有8个级别，按照从低到高为：All < Trace < Debug < Info < Warn < Error < Fatal < OFF.
- name:用来指定该Logger所适用的类或者类所在的包全路径,继承自Root节点.
- AppenderRef：Logger的子节点，用来指定该日志输出到哪个Appender,如果没有指定，就会默认继承自Root.如果指定了，那么会在指定的这个Appender和Root的Appender中都会输出，此时我们可以设置Logger的additivity="false"只在自定义的Appender中进行输出。



# Logback

https://www.cnblogs.com/gavincoder/p/10091757.html

默认配置的步骤

(1). 尝试在 classpath 下查找文件 logback-test.xml；

(2). 如果文件不存在，则查找文件 logback.xml；

(3). 如果两个文件都不存在，logback 用 Bas icConfigurator 自动对自己进行配置，这会导致记录输出到控制台。





```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration debug="false">
    <!--定义日志文件的存储地址 勿在 LogBack 的配置中使用相对路径-->
    <property name="LOG_HOME" value="/test/log" />
    <!-- 控制台输出 -->
    <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
        <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
            <!--格式化输出：%d表示日期，%thread表示线程名，%-5level：级别从左显示5个字符宽度%msg：日志消息，%n是换行符-->
            <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{50} - %msg%n</pattern>
        </encoder>
    </appender>
    <!-- 按照每天生成日志文件 -->
    <appender name="FILE"  class="ch.qos.logback.core.rolling.RollingFileAppender">
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <!--日志文件输出的文件名-->
            <FileNamePattern>${LOG_HOME}/TestWeb.log.%d{yyyy-MM-dd}.log</FileNamePattern>
            <!--日志文件保留天数-->
            <MaxHistory>30</MaxHistory>
        </rollingPolicy>
        <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
            <!--格式化输出：%d表示日期，%thread表示线程名，%-5level：级别从左显示5个字符宽度%msg：日志消息，%n是换行符-->
            <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{50} - %msg%n</pattern>
        </encoder>
        <!--日志文件最大的大小-->
        <triggeringPolicy class="ch.qos.logback.core.rolling.SizeBasedTriggeringPolicy">
            <MaxFileSize>10MB</MaxFileSize>
        </triggeringPolicy>
    </appender>
    <!-- show parameters for hibernate sql 专为 Hibernate 定制 -->
    <logger name="org.hibernate.type.descriptor.sql.BasicBinder"  level="TRACE" />
    <logger name="org.hibernate.type.descriptor.sql.BasicExtractor"  level="DEBUG" />
    <logger name="org.hibernate.SQL" level="DEBUG" />
    <logger name="org.hibernate.engine.QueryParameters" level="DEBUG" />
    <logger name="org.hibernate.engine.query.HQLQueryPlan" level="DEBUG" />
    <!--myibatis log configure-->
    <logger name="com.apache.ibatis" level="TRACE"/>
    <logger name="java.sql.Connection" level="DEBUG"/>
    <logger name="java.sql.Statement" level="DEBUG"/>
    <logger name="java.sql.PreparedStatement" level="DEBUG"/>

    <!-- 日志输出级别 -->
    <root level="INFO">
        <appender-ref ref="STDOUT" />
        <appender-ref ref="FILE" />
    </root>
    <!--日志异步到数据库 -->
    <!--<appender name="DB" class="ch.qos.logback.classic.db.DBAppender">-->
    <!--&lt;!&ndash;日志异步到数据库 &ndash;&gt;-->
    <!--<connectionSource class="ch.qos.logback.core.db.DriverManagerConnectionSource">-->
    <!--&lt;!&ndash;连接池 &ndash;&gt;-->
    <!--<dataSource class="com.mchange.v2.c3p0.ComboPooledDataSource">-->
    <!--<driverClass>com.mysql.jdbc.Driver</driverClass>-->
    <!--<url>jdbc:mysql://127.0.0.1:3306/databaseName</url>-->
    <!--<user>root</user>-->
    <!--<password>root</password>-->
    <!--</dataSource>-->
    <!--</connectionSource>-->
    <!--</appender>-->
</configuration>
```
## logback.xml常用配置详解

根节点<configuration>，包含下面三个属性：

- scan: 当此属性设置为true时，配置文件如果发生改变，将会被重新加载，默认值为true。
- scanPeriod: 设置监测配置文件是否有修改的时间间隔，如果没有给出时间单位，默认单位是毫秒。当scan为true时，此属性生效。默认的时间间隔为1分钟。
- debug: 当此属性设置为true时，将打印出logback内部日志信息，实时查看logback运行状态。默认值为false。

示例：

```xml
<configuration scan="true" scanPeriod="60 seconds" debug="false"> 
　　  <!--其他配置省略--> 
</configuration>　
```

### appender

子节点<appender>：负责写日志的组件，它有两个必要属性name和class。name指定appender名称，class指定appender的全限定名。

下文只简述常见的appder，其他appender使用请参考官方文档。

### **ConsoleAppender 把日志输出到控制台，有以下子节点：**

<encoder>：对日志进行格式化。
<target>：字符串System.out(默认)或者System.err
示例：把>=DEBUG级别的日志都输出到控制台

```xml
<configuration> 
　　　<appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender"> 
　　　　　 <encoder> 
　　　　　　　　　<pattern>%-4relative [%thread] %-5level %logger{35} - %msg %n</pattern> 
　　　　　 </encoder> 
　　　</appender> 

　　　<root level="DEBUG"> 
　　　　　　<appender-ref ref="STDOUT" /> 
　　　</root> 
</configuration>
```

**FileAppender：把日志添加到文件，有以下子节点：**
　　　　　<file>：被写入的文件名，可以是相对目录，也可以是绝对目录，如果上级目录不存在会自动创建，没有默认值。
　　　　　<append>：如果是 true，日志被追加到文件结尾，如果是 false，清空现存文件，默认是true。
　　　　　<encoder>：对记录事件进行格式化。（具体参数稍后讲解 ）
　　　　　<prudent>：如果是 true，日志会被安全的写入文件，即使其他的FileAppender也在向此文件做写入操作，效率低，默认是 false。
**示例：**把>=DEBUG级别的日志都输出到testFile.log

```xml
<configuration> 
    <appender name="FILE" class="ch.qos.logback.core.FileAppender"> 
        <file>testFile.log</file> 
        <append>true</append> 
        <encoder> 
            <pattern>%-4relative [%thread] %-5level %logger{35} - %msg%n</pattern> 
        </encoder> 
    </appender> 

    <root level="DEBUG"> 
        <appender-ref ref="FILE" /> 
    </root> 
</configuration>
```

**RollingFileAppender：滚动记录文件，先将日志记录到指定文件，当符合某个条件时，将日志记录到其他文件。有以下子节点：**
　　　　 <file>：被写入的文件名，可以是相对目录，也可以是绝对目录，如果上级目录不存在会自动创建，没有默认值。
　　　　 <append>：如果是 true，日志被追加到文件结尾，如果是 false，清空现存文件，默认是true。
　　　　 <rollingPolicy>:当发生滚动时，决定RollingFileAppender的行为，涉及文件移动和重命名。属性class定义具体的滚动策略类

**<1> class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy"： 最常用的滚动策略，它根据时间来制定滚动策略，既负责滚动也负责出发滚动。有以下子节点：**
　　　　  <fileNamePattern>：必要节点，包含文件名及“%d”转换符，“%d”可以包含一个java.text.SimpleDateFormat指定的时间格式，如：%d{yyyy-MM}。
如果直接使用 %d，默认格式是 yyyy-MM-dd。RollingFileAppender的file字节点可有可无，通过设置file，可以为活动文件和归档文件指定不同位置，当前日志总是记录到file指定的文件（活动文件），活动文件的名字不会改变；
如果没设置file，活动文件的名字会根据fileNamePattern 的值，每隔一段时间改变一次。“/”或者“\”会被当做目录分隔符。
　　　　　　　　<maxHistory>:
可选节点，控制保留的归档文件的最大数量，超出数量就删除旧文件。假设设置每个月滚动，且<maxHistory>是6，则只保存最近6个月的文件，删除之前的旧文件。注意，删除旧文件是，那些为了归档而创建的目录也会被删除。

**<2> class="ch.qos.logback.core.rolling.SizeBasedTriggeringPolicy"： 查看当前活动文件的大小，如果超过指定大小会告知RollingFileAppender 触发当前活动文件滚动。只有一个节点:**
　　　　　<maxFileSize>:这是活动文件的大小，默认值是10MB。
　　　　　<prudent>：当为true时，不支持FixedWindowRollingPolicy。支持TimeBasedRollingPolicy，但是有两个限制，1不支持也不允许文件压缩，2不能设置file属性，必须留空。
　　　　　<triggeringPolicy >: 告知 RollingFileAppender 合适激活滚动。

**<3> class="ch.qos.logback.core.rolling.FixedWindowRollingPolicy" 根据固定窗口算法重命名文件的滚动策略。有以下子节点：**
　　　　　<minIndex>:窗口索引最小值
　　　　　<maxIndex>:窗口索引最大值，当用户指定的窗口过大时，会自动将窗口设置为12。
　　　　　<fileNamePattern>:必须包含“%i”例如，假设最小值和最大值分别为1和2，命名模式为 mylog%i.log,会产生归档文件mylog1.log和mylog2.log。还可以指定文件压缩选项，例如，mylog%i.log.gz 或者 没有log%i.log.zip



 **示例：**每天生成一个日志文件，保存30天的日志文件。

```xml
<configuration> 
    <appender name="FILE" class="ch.qos.logback.core.rolling.RollingFileAppender"> 
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy"> 
            <fileNamePattern>logFile.%d{yyyy-MM-dd}.log</fileNamePattern> 
            <maxHistory>30</maxHistory> 
        </rollingPolicy> 
        <encoder> 
            <pattern>%-4relative [%thread] %-5level %logger{35} - %msg%n</pattern> 
        </encoder> 
    </appender> 

    <root level="DEBUG"> 
        <appender-ref ref="FILE" /> 
    </root> 
</configuration>
```

### 3、子节点<logger>：用来设置某一个包或具体的某一个类的日志打印级别、以及指定<appender>。

### <logger>仅有一个name属性，一个可选的level和一个可选的addtivity属性。

可以包含零个或多个<appender-ref>元素，标识这个appender将会添加到这个logger。
　　　　name: 用来指定受此loger约束的某一个包或者具体的某一个类。
　　　　level: 用来设置打印级别，大小写无关：TRACE, DEBUG, INFO, WARN, ERROR, ALL和OFF，还有一个特殊值INHERITED或者同义词NULL，代表强制执行上级的级别。 如果未设置此属性，那么当前loger将会继承上级的级别。
　　　　addtivity: 是否向上级logger传递打印信息。默认是true。可以包含零个或多个<appender-ref>元素，标识这个appender将会添加到这个logger。

###  4、子节点<root>:它也是<logger>元素，但是它是根loger,是所有<loger>的上级。只有一个level属性，因为name已经被命名为"root",且已经是最上级了。

　　　　level: 用来设置打印级别，大小写无关：TRACE, DEBUG, INFO, WARN, ERROR, ALL和OFF，不能设置为INHERITED或者同义词NULL。 默认是DEBUG。

同<loggert>一样，可以包含零个或多个<appender-ref>元素，标识这个appender将会添加到这个logger。

**示例：**常用logger配置

```xml
<!-- show parameters for hibernate sql 专为 Hibernate 定制 -->
<logger name="org.hibernate.type.descriptor.sql.BasicBinder" level="TRACE" />
<logger name="org.hibernate.type.descriptor.sql.BasicExtractor" level="DEBUG" />
<logger name="org.hibernate.SQL" level="DEBUG" />
<logger name="org.hibernate.engine.QueryParameters" level="DEBUG" />
<logger name="org.hibernate.engine.query.HQLQueryPlan" level="DEBUG" />

<!--myibatis log configure-->
<logger name="com.apache.ibatis" level="TRACE"/>
<logger name="java.sql.Connection" level="DEBUG"/>
<logger name="java.sql.Statement" level="DEBUG"/>
<logger name="java.sql.PreparedStatement" level="DEBUG"/>
```

### **5、子节点<contextName>：用来设置上下文名称，每个logger都关联到logger上下文，默认上下文名称为default。但可以使用<contextName>设置成其他名字，用于区分不同应用程序的记录。一旦设置，不能修改。**

 **示例：**

```
<configuration scan="true" scanPeriod="60 seconds" debug="false"> 
     <contextName>myAppName</contextName> 
　　  <!--其他配置省略-->
</configuration>    
```

###  

### 6、子节点<property> ：用来定义变量值，它有两个属性name和value，通过<property>定义的值会被插入到logger上下文中，可以使“${}”来使用变量。　　　　

　　　　name: 变量的名称
　　　　value: 的值时变量定义的值

 **示例：**

```
<configuration scan="true" scanPeriod="60 seconds" debug="false"> 
　　　<property name="APP_Name" value="myAppName" /> 
　　　<contextName>${APP_Name}</contextName> 
　　　<!--其他配置省略--> 
</configuration>
```

###  

### 7、子节点<timestamp>：获取时间戳字符串，他有两个属性key和datePattern。　　　　

　　　　key: 标识此<timestamp> 的名字；
　　　　datePattern: 设置将当前时间（解析配置文件的时间）转换为字符串的模式，遵循java.txt.SimpleDateFormat的格式。

**示例：**

```
    <configuration scan="true" scanPeriod="60 seconds" debug="false"> 
　　　　　　<timestamp key="bySecond" datePattern="yyyyMMdd'T'HHmmss"/> 
　　　　　　<contextName>${bySecond}</contextName> 
　　　　　　<!-- 其他配置省略--> 
    </configuration>
```

## logback.xml配置示例

```xml

<?xml version="1.0" encoding="UTF-8"?>
<configuration debug="false">

    <!--定义日志文件的存储地址 勿在 LogBack 的配置中使用相对路径-->
    <property name="LOG_HOME" value="/home" />

    <!--控制台日志， 控制台输出 -->
    <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
        <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
            <!--格式化输出：%d表示日期，%thread表示线程名，%-5level：级别从左显示5个字符宽度,%msg：日志消息，%n是换行符-->
            <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{50} - %msg%n</pattern>
        </encoder>
    </appender>

    <!--文件日志， 按照每天生成日志文件 -->
    <appender name="FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <!--日志文件输出的文件名-->
            <FileNamePattern>${LOG_HOME}/TestWeb.log.%d{yyyy-MM-dd}.log</FileNamePattern>
            <!--日志文件保留天数-->
            <MaxHistory>30</MaxHistory>
        </rollingPolicy>
        <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
            <!--格式化输出：%d表示日期，%thread表示线程名，%-5level：级别从左显示5个字符宽度%msg：日志消息，%n是换行符-->
            <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{50} - %msg%n</pattern>
        </encoder>
        <!--日志文件最大的大小-->
        <triggeringPolicy class="ch.qos.logback.core.rolling.SizeBasedTriggeringPolicy">
            <MaxFileSize>10MB</MaxFileSize>
        </triggeringPolicy>
    </appender>

    <!-- show parameters for hibernate sql 专为 Hibernate 定制 -->
    <logger name="org.hibernate.type.descriptor.sql.BasicBinder" level="TRACE" />
    <logger name="org.hibernate.type.descriptor.sql.BasicExtractor" level="DEBUG" />
    <logger name="org.hibernate.SQL" level="DEBUG" />
    <logger name="org.hibernate.engine.QueryParameters" level="DEBUG" />
    <logger name="org.hibernate.engine.query.HQLQueryPlan" level="DEBUG" />

    <!--myibatis log configure-->
    <logger name="com.apache.ibatis" level="TRACE"/>
    <logger name="java.sql.Connection" level="DEBUG"/>
    <logger name="java.sql.Statement" level="DEBUG"/>
    <logger name="java.sql.PreparedStatement" level="DEBUG"/>

    <!-- 日志输出级别 -->
    <root level="DEBUG">
        <appender-ref ref="STDOUT" />
        <appender-ref ref="FILE"/>
    </root>
</configuration>
```

## 总结

### 1、输出源选择

logback的配置，需要配置输出源appender，打日志的logger（子节点）和root（根节点），实际上，它输出日志是从子节点开始，子节点如果有输出源直接输入，如果无，判断配置的addtivity，是否向上级传递，即是否向root传递，传递则采用root的输出源，否则不输出日志。

### 2、日志级别Level

日志记录器（Logger）的行为是分等级的： 分为OFF、FATAL、ERROR、WARN、INFO、DEBUG、ALL或者您定义的级别。
Log4j建议只使用四个级别，**优先级从高到低分别是 ERROR、WARN、INFO、DEBUG，优先级高的将被打印出来。（logback通用）**
通过定义级别，可以作为应用程序中相应级别的日志信息的开关。
**比如在这里定义了INFO级别，则应用程序中所有DEBUG级别的日志信息将不被打印出来。（设置INFO级别，即：>=INFO 生效）**
**项目上生产环境的时候一定得把debug的日志级别重新调为warn或者更高，避免产生大量日志。**

 