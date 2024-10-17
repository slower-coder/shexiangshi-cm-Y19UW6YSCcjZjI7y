**目录**

* [0 引言](#_label0)
* [1 验证实验](#_label1)
+ [实验1：日志框架打印应用程序日志代码的执行情况](#_lab2_1_0)
- [日志配置策略: log4j2\.properties](#_label3_1_0_0)
- [应用程序代码: LogTest](#_label3_1_0_1)
- [实验结果](#_label3_1_0_2)
- [最终结论](#_label3_1_0_3)

* [X 参考文献](#_label2)


[回到顶部(Back to Top)](#_labelTop):[西部世界官网](https://tianchuang88.com)# 0 引言


* 我常以为 配置 INFO 日志级别时， 应用程序代码中**日志器**(logger) debug 级的日志代码，不会被执行（比如，实验1中的`printTestLog`函数）。但今天线上的问题，证实了这个思路是错的。


![](https://img2024.cnblogs.com/blog/1173617/202410/1173617-20241016160637967-1116385172.png)


[回到顶部(Back to Top)](#_labelTop)# 1 验证实验


* 版本信息



> * jdk : 1\.8
> * 日志组件
> 
> 
> 
> > * `slf4j.version` : 1\.7\.25
> > * `log4j.version` : 2\.20\.0



```

<dependency>
	<groupId>org.slf4jgroupId>
	<artifactId>slf4j-apiartifactId>
	<version>${slf4j.version}version>
dependency>
<dependency>
	<groupId>org.apache.logging.log4jgroupId>
	<artifactId>log4j-apiartifactId>
	<version>${log4j.version}version>
dependency>
<dependency>
	<groupId>org.apache.logging.log4jgroupId>
	<artifactId>log4j-coreartifactId>
	<version>${log4j.version}version>
dependency>
<dependency>
	<groupId>org.apache.logging.log4jgroupId>
	<artifactId>log4j-slf4j-implartifactId>
	<version>${log4j.version}version>
dependency>
<dependency>
	<groupId>org.apache.logging.log4jgroupId>
	<artifactId>log4j-julartifactId>
	
	<version>${log4j.version}version>
	<scope>compilescope>
dependency>


```

## 实验1：日志框架打印应用程序日志代码的执行情况


### 日志配置策略: log4j2\.properties


* `log4j2.properties`



```
## 日志的等级(自定义配置项)
##property.log.level=ALL,TRACE,DEBUG,INFO,WARN,ERROR,FATAL,OFF
property.log.level=DEBUG

# ------------------- [1.1] 定义 RootLogger 等 全局性配置(不可随意修改) ------------------- #
## rootLogger, 根记录器，所有记录器的父辈
## 指定根日志的级别 | All < Trace < Debug < Info < Warn < Error < Fatal < OFF
rootLogger.level=${log.level}

... //略

```

### 应用程序代码: LogTest


* `LogTest`



```
package test.java.lang;

import lombok.extern.slf4j.Slf4j;

@Slf4j
public class LogTest {
    public static String printTestLog(){
        return "HelloWorld";//关键代码行
    }

    public static void main(String[] args) {
        log.debug( "log:{}", printTestLog() );
    }
}

```

### 实验结果


* `log.level=INFO`时



```
关键代码行 : 被执行

日志输出结果: 空

```

* `log.level=DEBUG`时



```
关键代码行 : 被执行

日志输出结果: 
[20XX/10/16 16:01:28.585] [TID: N/A] [DEBUG] [main] [LogTest.java:12 main] log:HelloWorld

```

![](https://img2024.cnblogs.com/blog/1173617/202410/1173617-20241016160117664-1034064108.png)


### 最终结论


* 无论 应用程序日志代码 logger 使用何种日志级别打印日志，代码行中的程序均会被执行，只是最终输出时由日志框架根据配置logger所属class的日志级别决定是否输出(appender)
* 解决方法1：应用程序中，如无必要，删除这类日志代码。
* 解决方法2：`log.isDebugEnabled(...)/isInfoEnabled(...)/isWarnEnabled(...)/isErrorEnabled(...)/...`



```
    public static void main(String[] args) {
        if(log.isDebugEnabled()){//将会根据 用户所配置的日志级别(log.level)，决定是否执行 IF 内的代码
            log.debug( "log:{}", printTestLog() );
        }
    }

```

[回到顶部(Back to Top)](#_labelTop)# X 参考文献


* 无


