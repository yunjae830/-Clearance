### [뒤로가기]( https://yunjae830.github.io/-Clearance/file/Spring )



# LogBack

 ※ 공식사이트 : https://logback.qos.ch/ 

### LogBack의 특징

- log4j를 토대로 새롭게 만든 Logging 라이브러리다.

- slf4j를 통해 연관 라이브러리들이 다른 logging framework를 쓰더라도 logback으로 통합할 수 있다.

- Logback을 이용하여 로깅을 수행하기 위해서 필요한 주요 설정요소로는 Logger, Appender, Encoder의 3가지가 있으며 각각은 다음과 같은 역할을 수행한다.
  -  Logger : 실제 로깅을 수행하는 구성요소로 Level 속성을 통해서 출력할 로그의 레벨을 조절할 수 있다.
  -  Appender : 로그 메시지가 출력될 대상을 결정하는 요소
  -  Encoder : Appender에 포함되어 사용자가 지정한 형식으로 표현 될 로그메시지를 변환하는 역할을 담당하는 요소



### LogBack의 장점

1. log4j보다 약 10배 정도 빠르게 수행되도록 내부가 변경되었으며, 메모리 효율성도 좋아졌다.
2.  log4j때부터 광범위한 테스트를 진행한 경험을 가지고 있으며, logback은 더욱 높은 레벨의 테스트를 통해 검증되었다.
3.  문서화가 잘 되어 있다.
4.  설정 파일을 변경하였을 경우, 서버 재기동 없이 변경 내용이 자동으로 갱신된다.
5.  서버 중지 없이 I/O Faliure에 대한 복구를 지원한다.
6.  RollingFileAppender를 사용할 경우 자동적으로 오래된 로그를 지워주며 Rolling 백업을 처리한다.



https://goddaehee.tistory.com/45

### Maven 에서 logback 가져오기

```xml
<dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-classic</artifactId>
    <version>1.1.7</version> <!--버전은 상황에 따라 -->
</dependency>
```



### 로그 레벨

 `ERROR`, `WARN`, `INFO`, `DEBUG` or `TRACE` 

-  ERROR : 일반 에러가 일어 났을 때 사용한다. 
-  WARN : 에러는 아니지만 주의할 필요가 있을 때 사용한다.
- INFO : 일반 정보를 나타낼 때 사용한다.
-  DEBUG : 일반 정보를 상세히 나타낼 때 사용한다.
-  TRACE : 경로추적을 위해 사용한다.



### logback 설정파일

일반적으로 `logback.xml` 이라는 이름으로 만들어 `src/main/resources/`아래에 위치하게 된다. Spring-Boot 환경에서는 `logback-spring.xml` 이라는 이름으로 설정해야 하는데 `logback.xml`로 설정하면 스프링부트가 설정하기 전에 로그백 관련한 설정을 하기 때문에 제어할 수가 없게 된다.

 ( 공식사이트 메뉴얼 : https://logback.qos.ch/documentation.html )

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <include resource="org/springframework/boot/logging/logback/defaults.xml" />
    <include resource="org/springframework/boot/logging/logback/console-appender.xml" />    

    <!-- 변수 지정 -->
    <property name="LOG_DIR" value="/logs" />
    <property name="LOG_PATH_NAME" value="${LOG_DIR}/data.log" />

    <!-- FILE Appender -->
    <appender name="FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
        <file>${LOG_PATH_NAME}</file>
        <!-- 일자별로 로그파일 적용하기 -->
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <fileNamePattern>${LOG_PATH_NAME}.%d{yyyyMMdd}</fileNamePattern>
            <maxHistory>60</maxHistory> <!-- 일자별 백업파일의 보관기간 -->
        </rollingPolicy>
        <encoder>
            <pattern>%d{yyyy-MM-dd HH:mm:ss} [%-5p] [%F]%M\(%L\) : %m%n</pattern>
        </encoder>
    </appender>

    <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
      <layout class="ch.qos.logback.classic.PatternLayout">
        <pattern>%d{yyyy-MM-dd HH:mm:ss} [%-5p] [%F]%M\(%L\) : %m%n</pattern>
      </layout>
    </appender>

    <!-- TRACE > DEBUG > INFO > WARN > ERROR, 대소문자 구분 안함 -->
    <!-- profile 을 읽어서 appender 을 설정할수 있다.(phase별 파일을 안만들어도 되는 좋은 기능) -->
    <springProfile name="local">
      <root level="DEBUG">
        <appender-ref ref="FILE" />
        <appender-ref ref="STDOUT" />
      </root>
    </springProfile>
    <springProfile name="real">
      <root level="INFO">
        <appender-ref ref="FILE" />
        <appender-ref ref="STDOUT" />
      </root>
    </springProfile>
</configuration>
```



### java 코딩에서의 로깅

실제 사용은 다음과 같이 `LoggerFactory`를 이용해서 사용하거나 `Lombok`어노테이션을 활용하면 심플하게 사용이 가능하다.

##### LoggerFactory 사용

```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class Foo {
    static final Logger logger = LoggerFactory.getLogger(Foo.class);

    public void test() {
        logger.debug("ID : {}", "foo");
    }
}
```

##### Lombok 어노테이션 사용

```java
import lombok.extern.slf4j.Slf4j;

@Slf4j
public class Foo {

    public void test() {
        log.debug("ID : {}", "foo");
    }
}
```

