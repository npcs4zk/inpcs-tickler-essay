# SpringBoot中集成Log4j2日志框架

### 什么是Log4j

Log4j是Apache的一个开源项目，通过使用Log4j，我们可以控制日志信息输送的目的地是控制台、文件、GUI组件，甚至是套接口服务器、NT的事件记录器、UNIX Syslog守护进程等；我们也可以控制每一条日志的输出格式；通过定义每一条日志信息的级别，我们能够更加细致地控制日志的生成过程。最令人感兴趣的就是，这些可以通过一个配置文件来灵活地进行配置，而不需要修改应用的代码。

### 什么是Log4j2

### Log4j2的调用

引入的包

```
import org.apache.logging.log4j.Level;
import org.apache.logging.log4j.LogManager;
import org.apache.logging.log4j.Logger;
```

静态变量

```
private static Logger logger = LogManager.getLogger(Test.class.getName());
```

### 配置文件模板

```
<?xml version="1.0" encoding="UTF-8"?>
<!--设置log4j2的自身log级别为warn-->
<!--日志级别以及优先级排序: OFF > FATAL > ERROR > WARN > INFO > DEBUG > TRACE > ALL -->
<!--Configuration后面的status，这个用于设置log4j2自身内部的信息输出，可以不设置，
    当设置成trace时，你会看到log4j2内部各种详细输出-->
<!--monitorInterval：Log4j能够自动检测修改配置 文件和重新配置本身，设置间隔秒数-->
<configuration status="warn" monitorInterval="30">
    <!--先定义所有的appender-->
    <appenders>
        <!--这个输出控制台的配置-->
        <console name="Console" target="SYSTEM_OUT">
            <!--输出日志的格式-->
            <PatternLayout pattern="[%d{HH:mm:ss:SSS}] [%p] - %l - %m%n"/>
        </console>
        <!--文件会打印出所有信息，这个log每次运行程序会自动清空，由append属性决定，这个也挺有用的，适合临时测试用-->
        <File name="log" fileName="log/test.log" append="false">
            <PatternLayout pattern="%d{HH:mm:ss.SSS} %-5level %class{36} %L %M - %msg%xEx%n"/>
        </File>
        <!-- 这个会打印出所有的info及以下级别的信息，每次大小超过size，
        则这size大小的日志会自动存入按年份-月份建立的文件夹下面并进行压缩，作为存档-->
        <RollingFile name="RollingFileInfo" fileName="${sys:user.home}/devsoft/mymsWork//logs/info.log"
                     filePattern="${sys:user.home}/devsoft/mymsWork/logs/$${date:yyyy-MM}/info-%d{yyyy-MM-dd}-%i.log">
            <Filters>
                <!--控制台只输出level及以上级别的信息（onMatch），其他的直接拒绝（onMismatch）-->
                <ThresholdFilter level="INFO" onMatch="ACCEPT" onMismatch="DENY"/>
                <ThresholdFilter level="WARN" onMatch="DENY" onMismatch="NEUTRAL"/>
            </Filters>
            <PatternLayout pattern="[%d{HH:mm:ss:SSS}] [%p] - %l - %m%n"/>
            <Policies>
                <TimeBasedTriggeringPolicy/>
                <SizeBasedTriggeringPolicy size="100 MB"/>
            </Policies>
        </RollingFile>

        <RollingFile name="RollingFileWarn" fileName="${sys:user.home}/devsoft/mymsWork/logs/warn.log"
                     filePattern="${sys:user.home}/devsoft/mymsWork/logs/$${date:yyyy-MM}/warn-%d{yyyy-MM-dd}-%i.log">
            <Filters>
                <ThresholdFilter level="WARN" onMatch="ACCEPT" onMismatch="DENY"/>
                <ThresholdFilter level="ERROR" onMatch="DENY" onMismatch="NEUTRAL"/>
            </Filters>
            <PatternLayout pattern="[%d{HH:mm:ss:SSS}] [%p] - %l - %m%n"/>
            <Policies>
                <TimeBasedTriggeringPolicy/>
                <SizeBasedTriggeringPolicy size="100 MB"/>
            </Policies>
            <!-- DefaultRolloverStrategy属性如不设置，则默认为最多同一文件夹下7个文件，这里设置了20 -->
            <DefaultRolloverStrategy max="20"/>
        </RollingFile>

        <RollingFile name="RollingFileError" fileName="${sys:user.home}/devsoft/mymsWork/logs/error.log"
                     filePattern="${sys:user.home}/devsoft/mymsWork/logs/$${date:yyyy-MM}/error-%d{yyyy-MM-dd}-%i.log">
            <ThresholdFilter level="ERROR"/>
            <PatternLayout pattern="[%d{HH:mm:ss:SSS}] [%p] - %l - %m%n"/>
            <Policies>
                <TimeBasedTriggeringPolicy/>
                <SizeBasedTriggeringPolicy size="100 MB"/>
            </Policies>
        </RollingFile>

    </appenders>
    <!--然后定义logger，只有定义了logger并引入的appender，appender才会生效-->
    <loggers>
        <!--过滤掉spring和hibernate的一些无用的debug信息-->
        <logger name="org.springframework" level="INFO">
        </logger>
        <logger name="org.mybatis" level="INFO">
        </logger>
        <root level="all">
            <appender-ref ref="Console"/>
            <appender-ref ref="RollingFileInfo"/>
            <appender-ref ref="RollingFileWarn"/>
            <appender-ref ref="RollingFileError"/>
        </root>
    </loggers>

</configuration>
```

### Sprintboot集成

**pom.xml加入log4j2，并同时把spring boot默认的logging去掉**

```
<dependencies>  
    <dependency>  
        <groupId>org.springframework.boot</groupId>  
        <artifactId>spring-boot-starter-web</artifactId>  
        <exclusions><!-- 去掉默认配置 -->  
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
</dependencies>  

```

##### 注意事项：

　　根据maven依赖的特点，应该将该项配置放到第一位。

### 

### 参考资料：

[https://www.cnblogs.com/lzb1096101803/p/5796849.html](https://www.cnblogs.com/lzb1096101803/p/5796849.html)

