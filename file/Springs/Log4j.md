### [뒤로가기]( https://yunjae830.github.io/-Clearance/file/Spring )



# Log4j

로그문의 출력을 다양한 대상으로 할 수 있도록 도와주는 도구(오픈소스)

- [**오픈소스 링크**](http://logging.apache.org/log4j/1.2/) 

### Log4j 특징

- log4j는 속도에 최적화
- log4j는 이름있는 로그 계층에 기반
- log4j는 fail-stop이지만 신뢰성은 없음
- log4j는 thread-safe(멀티스레드 환경에서 사용해도 안전하다)
- 설정 파일은 property 파일과 XML 형식으로 실행 중 수정 적용 가능
- log4j는 다음 6단계의 장애레벨을 사용. < TRACE(추가), DEBUG, INFO, WARN, ERROR, FATAL >



### Log4j 구조

| Logger(Category) | 로깅 메세지를 Appender에 전달, 개발자가 직접 로그출력 여부를 런타임에 조정 |
| ---------------- | ------------------------------------------------------------ |
| **Appender**     | **로그의 출력위치를 결정(파일, 콘솔, DB 등), log4J API문서의 XXXAppender로 끝나는 클래스의 이름을 보면, 출력위치를 어느정도 짐작가능** |
| **Layout**       | **Appender가 어디에 출력할 것인지 결정했다면 어떤 형식으로 출력할 것인지 출력 layout을 결정** |



### Log4j 레벨

| FATAL     | 아주 심각한 에러가 발생한 상태. 시스템적으로 심각한 문제가 발생해서 어플리케이션작동이 불가능할 경우가 해당하는데, 일반적으로는 어플리케이션에서는 사용할 일이 없음 |
| --------- | ------------------------------------------------------------ |
| **ERROR** | **요청을 처리하는 중 문제가 발생한 상태를 나타냄**           |
| **WARN**  | **처리 가능한 문제이지만, 향후 시스템 에러의 원인이 될 수 있는 경고성 메시지를 나타냅니다.** |
| **INFO**  | **로그인, 상태변경과 같은 정보성 메시지를 나타냅니다.**      |
| **DEBUG** | **개발시 디버그 용도로 사용한 메시지를 나타냅니다.**         |
| **TRACE** | **log4j1.2.12에서 신규 추가된 레벨로서, DEBUG 레벨이 너무 광범위한 것을 해결하기 위해서 좀 더 상세한 상태를 나타냅니다.** |



### Log4j Pattern Option

| **%p** | debug, info, warn, error, fatal 등의 priority 출력           |
| ------ | ------------------------------------------------------------ |
| **%m** | **로그내용 출력**                                            |
| **%d** | **로깅 이벤트가 발생한 시간을 출력, ex)포맷은 %d{HH:mm:ss} 같은 형태의 SimpleDateFormat** |
| **%t** | **로그이벤트가 발생된 쓰레드의 이름 출력**                   |
| **%F** | **로깅이 발생한 프로그램 파일명 출력**                       |
| **%l** | **로깅이 발생한 caller의 정보 출력**                         |
| **%L** | **로깅이 발생한 caller의 라인수 출력**                       |
| **%M** | **로깅이 발생한 method 이름 출력**                           |
| **%**  | **% 표시 출력**                                              |
| **%n** | **플랫폼 종속적인 개행문자 출력**                            |
| **%c** | **카테고리 출력, ex)카테고리가 a.b.c 처럼 되어있다면 %c{2}는 b.c 출력** |
| **%C** | **클래스명 출력, ex)클래스구조가 org.apache.xyz.SomeClass 처럼 되어있다면 %C{2}는 xyz.SomeClass 출력** |
| **%r** | **어플리케이션 시작 이후 부터 로깅이 발생한 시점의 시간(milliseconds) 출력** |
| **%x** | **로깅이 발생한 thread와 관련된 NDC(nested diagnostic context) 출력** |
| **%X** | **로깅이 발생한 thread와 관련된 MDC(mapped diagnostic context) 출력** |



### Log4j 주요 클래스

| **ConsoleAppender**          | org.apache.log4j.ConsoleAppender 콘솔에 로그 메시지 출력     |
| ---------------------------- | ------------------------------------------------------------ |
| **FileAppender**             | **org.apache.log4j.FileAppender** 파일에 로그 메시지 기록    |
| **RollingFileAppender**      | **org.apache.log4j.rolling.RollingFileAppender** 파일 크기가 일정 수준 이상이 되면 기존 파일을 백업파일로 바꾸고 처음부터 기록 |
| **DailyRollingFileAppender** | **org.apache.log4j.DailyRollingFileAppender** 일정 기간 단위로 로그 파일을 생성하고 기록 |
| **JDBCAppender**             | **org.apache.log4j.jdbc.JDBCAppender** DB에 로그를 출력. 하위에 Driver, URL, User, Password, Sql과 같은 parameter를 정의할 수 있음 |
| **SMTPAppender**             | 로그 메시지를 이메일로 전송                                  |
| **NTEventAppender**          | 윈도우 시스템 이벤트 로그로 메시지 전송                      |



### 전자정부 확장 Appender

| **EgovDBAppender**   | org.apache.log4j.db.EgovDBAppender : Oracle인 경우 getGeneratedKeys를 실행할 때 문제가 발생하므로 useSupportsGetGeneratedKeysflag 에 따라 JDBC3.0의 getGeneratedKeys를 사용하지 않는 옵션으로 처리 가능한 EgovDBAppender 로 처리함 |
| -------------------- | ------------------------------------------------------------ |
| **EgovJDBCAppender** | **egovframework.rte.fdl.logging.db.EgovJDBCAppender** : Singleton을 구현하고 있으며, spring의 dataSource bean 을 injection 할 수 있도록 Annotation Bean 으로도 설정되어 있어야함. |



### Log4j 설정

1. ##### 라이브러리 추가

   ###### maven 사용 시 pom.xml 수정

   ```xml
   <dependency>
       <groupId>log4j</groupId>
       <artifactId>log4j</artifactId>
       <version>1.2.17</version>
   </dependency>
   ```

2. 사이트에서 다운로드 받은 후 프로젝트의 WebContent > WEB_INF > lib 폴더에 복사

    [**log4j다운로드 사이트**](http://logging.apache.org/log4j/1.2/download.html) 

3. 설정파일 만들기

   설정파일은 xml 또는 properties 로 설정 가능함(xml 권장)

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <!DOCTYPE log4j:configuration SYSTEM "http://logging.apache.org/log4j/1.2/apidocs/org/apache/log4j/xml/doc-files/log4j.dtd">
   <log4j:configuration xmlns:log4j="http://jakarta.apache.org/log4j/">
    
       <!-- 콘솔 로그 -->
       <appender name="infoConsole" class="org.apache.log4j.ConsoleAppender">
           <layout class="org.apache.log4j.PatternLayout">
               <param name="ConversionPattern" value="[%5p] %d{hh\:mm s} (%F\:%L) %c{1}.%M \: %m%n" />
           </layout>  
       </appender>
       
       <!-- root 설정 -->
       <root>
           <level value="INFO" />
           <appender-ref ref="infoConsole" />
       </root>
        
   </log4j:configuration>
   ```

   ```properties
   ####### Global logging configuration
   log4j.rootLogger=INFO, stdout
    
   ####### stdout Appender
   log4j.appender.stdout=org.apache.log4j.ConsoleAppender
   log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
   log4j.appender.stdout.layout.ConversionPattern=[%5p] %d{hh\:mm s} (%F\:%L) %c{1}.%M \: %m%n
   ```

4. web.xml 수정

   Log4j의 설정파일을 Web Context Listener를 통해 등록

   ```xml
   	<context-param>
           <param-name>log4jConfigLocation</param-name>
           <param-value>
               classpath:project/config/log4j.xml
           </param-value>
       </context-param>
    
       <!-- Log4j -->
       <listener>
           <listener-class>
               org.springframework.web.util.Log4jConfigListener
           </listener-class>
       </listener>
   ```