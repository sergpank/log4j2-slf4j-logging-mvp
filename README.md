# log4j2-slf4j-logging-mvp

A bare bone Log4j2 with SLF4j project.

I use it as a cheatsheet when starting a new project, 
because I always forget which dependencies should be configured to make it work.

Especially I am tired from the following error:
```bash
SLF4J(W): No SLF4J providers were found.
SLF4J(W): Defaulting to no-operation (NOP) logger implementation
SLF4J(W): See https://www.slf4j.org/codes.html#noProviders for further details.
```
Which can be easily fixed by importing of `log4j-slf4j2-impl` dependency:
```xml
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-slf4j2-impl</artifactId>
    <version>2.22.1</version>
</dependency>

doc: https://logging.apache.org/log4j/2.x/log4j-slf4j-impl.html
```

Also Log4j2 requires `com.lmax.disruptor` to fix the 
`StatusConsoleListener Null object returned for AsyncRoot in Loggers` very annoying error:
```xml
<dependency>
    <groupId>com.lmax</groupId>
    <artifactId>disruptor</artifactId>
    <version>3.4.4</version>
</dependency>
```

If there is no need to have SLF4j we can use Log4j2 core implementation 
straightforwardly, it looks better on my taste:
```java
import org.apache.logging.log4j.LogManager;
import org.apache.logging.log4j.Logger;

public class App {
    private static final Logger log = LogManager.getLogger();

    public static void main( String[] args ) {
        log.info( "Hello Log!" );
    }
}
```

I don't like that SLF4j implementation requires class 
specification in Logger initilization:
```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class App {
    private static final Logger log = LoggerFactory.getLogger(App.class);

    public static void main( String[] args )
    {log.info( "Hello Log!" );
    }
}
```

And it requires only 3 libraries:
```xml
<!-- LOG4J -->
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-api</artifactId>
    <version>2.22.1</version>
</dependency>
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-core</artifactId>
    <version>2.22.1</version>
</dependency>
<!-- To fix error:
     ERROR StatusConsoleListener Could not create plugin of type class
     org.apache.logging.log4j.core.async.AsyncLoggerConfig$RootLogger
     for element AsyncRoot:
     java.lang.NoClassDefFoundError: com/lmax/disruptor/EventHandler
     ...
     ERROR StatusConsoleListener Null object returned for AsyncRoot in Loggers.
-->
<dependency>
    <groupId>com.lmax</groupId>
    <artifactId>disruptor</artifactId>
    <version>3.4.4</version>
</dependency>
```

Instead of 5:
```xml
<!-- LOG4J -->
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-api</artifactId>
    <version>2.22.1</version>
</dependency>
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-core</artifactId>
    <version>2.22.1</version>
</dependency>
<!-- The Log4j 2 SLF4J Binding allows applications coded to the SLF4J API to use Log4j 2 as the implementation.
     https://logging.apache.org/log4j/2.x/log4j-slf4j-impl.html

     Also, this dependency resolves the following typical error:
     SLF4J(W): No SLF4J providers were found.
     SLF4J(W): Defaulting to no-operation (NOP) logger implementation
     SLF4J(W): See https://www.slf4j.org/codes.html#noProviders for further details.
-->
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-slf4j2-impl</artifactId>
    <version>2.22.1</version>
</dependency>
<!-- To fix error:
     ERROR StatusConsoleListener Could not create plugin of type class
     org.apache.logging.log4j.core.async.AsyncLoggerConfig$RootLogger
     for element AsyncRoot:
     java.lang.NoClassDefFoundError: com/lmax/disruptor/EventHandler
-->
<dependency>
    <groupId>com.lmax</groupId>
    <artifactId>disruptor</artifactId>
    <version>3.4.4</version>
</dependency>

<!-- SLF4J -->
<dependency>
    <groupId>org.slf4j</groupId>
    <artifactId>slf4j-api</artifactId>
    <version>2.0.10</version>
</dependency>
```

Note: OnStartupTriggeringPolicy is configured for log rotation to keep data from each 
app run in a separate file.
