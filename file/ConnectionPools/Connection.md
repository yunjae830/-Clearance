### [뒤로가기]( https://yunjae830.github.io/-Clearance/file/Connection )



# Connection Pool Commons DBCP 이해하기

### Connection Pool

- 데이터베이스와 애플리케이션을 효율적으로 연결하기 위한 필수요소입니다. 커넥션 풀 라이브러리를 잘 사용하려면 데이터베이스와 애플리케이션의 일부에서 발생하는 문제를 전체로 전파되지 않게 할 수 있고, 일시적인 문제가 긴 시간 이어지지 않게 할 수 있습니다. 



### JDK 버전과 Commons DBCP 버전

- | Commons DBCP 버전    | JDK 버전          | JDBC 버전    |
  | -------------------- | ----------------- | ------------ |
  | **Commons DBCP 2**   | **JDK 7**         | **JDBC 4.1** |
  | **Commons DBCP 1.4** | **JDK 6**         | **JDBC 4**   |
  | **Commons DBCP 1.3** | **JDK 1.4 ~ 1.5** | **JDBC 3**   |

  

### Commons의 DBCP

- apache commons DBCP 는 커넥션 풀 구현체 오픈소스 중 가장 많이 쓰인다.

- maven 설정

  ```xml
  <dependency> 
      <groupId>org.apache.commons</groupId> 
      <artifactId>commons-dbcp2</artifactId> 
      <version>2.2.0</version> 
  </dependency>
  ```

- 커넥션 관련된 속성들

  - `initialSize` : 최초 시점에 getConnection() 을 통해 커넥션 풀에 채워 넣을 커넥션 개수 (default = 0)
  - `maxTotal` (1.x에서는 maxActive) : 동시에 사용할 수 있는 최대 커넥션 개수 (default = 8)
  - `maxldle` : Connection Pool에 반납할 때 최대로 유지될 수 있는 커넥션 개수 (default = 8)
  - `minldle` : 최소한으로 유지할 커넥션 개수 (default = 0)
  - `maxWaitMillis` (1.x에서는 maxWait) : pool이 고갈되었을 경우 최대 대기 시간 (ms단위, default = -1 = 무한정)

- 커넥션의 검사와 정리에 관련된 속성들

  - `validationQuery` : 풀에 커넥션을 반환하기 전이나, 풀을 획득하기 전에 커넥션이 valid한지를 검사, mysql 기준으로 보통 select 1 설정
  - ` validationQueryTimeout `
  - ` testOnCreate`
  - ` testOnBorrow` :  커넥션 풀에서 커넥션을 얻어올 때 테스트 실행 (default = true)
  - `testOnReturn` :  커넥션 풀로 커넥션을 반환할 때 테스트 실행 (default = false)
  - `testWhileIdle` : Evictor 가 실행될 때 커넥션 풀 안에 있는 유휴 상태의 커넥션을 대상으로 테스트 실행 (default = false)
  - `maxConnLifetimeMillis` :  커넥션의 최대 라이프타임을 지정 (default = -1)
  - `logExpiredConnections` :  로그로 maxConnLifetimeMillis를 초과한 경우에 커넥션이 닫혔음을 남김 (default = true)
  - `connectionInitSqls`
  - `lifo`

- Evictor 스레드와 관련된 속성

  - Evictor 스레드는 Commons DBCP 내부에서 커넥션 자원을 정리하는 구성 요소이며 별도의 스레드로 실행된다.
  - `timeBetweenEvictionRunsMillis` : Evictor 가 동작하는 간격 (default = -1, 비활성화)
  - `numTestsPerEvictionRun` : Evictor 동작 시 한 번에 검사할 커넥션의 개수 (default = 3)
  - `minEvictableIdleTimeMillis` : Evictor 동작 시 커넥션의 유휴 시간을 확인해 설정 값 이상일 경우 커넥션을 제거 (ms단위, default = 30분) -1 로 설정할 경우 사용하지 않음. (권장)
  - `softMiniEvictableIdleTimeMillis` : Evictor 가 커넥션을 제거하기 전에 minIdle 수 만큼의 커넥션은 남기도록 한다. 이때 설정값 시간만큼 존재한다. (default = -1)

- 트랜잭션에 관련된 속성들

  - `defaultAutoCommit` : true 이면 풀에 의해서 생성된 커넥션은 autocommit 된다. 커넥션이 종료되기 전에 commit 처리된다. (default = 드라이버기본값)
  - `defaultReadOnly` : 풀에 의해서 생성된 커넥션의 read-only상태를 설정 (default = 드라이버기본값)
  - `defaultTransactionIsolation`
  - `defaultCatalog` : 풀에 생성된 커넥션의 기본 카탈로그를 설정
  - `cacheState`
  - `defaultQueryTimeout`
  - `enableAutocommitOnReturn`
  - `rollbackOnReturn`

- PreparedStatements Pool 에 관련된 속성들

  - `poolPreparedStatements` : statement 풀링여부 설정 (default = false)
  - `maxOpenPreparedStatements` : 커넥션 당 최대 pooling 할 PreparedStatement 의 갯수를 설정 (default = 무한정, 무한정일 경우 OOM 발생할 수 있음)



### DataSource

- JDBC API 를 이용하여 DB 연동을 처리하려면 DB로부터 커넥션을 얻어야 합니다. 따라서 DB정보를 bean으로 등록하여 스프링 컨테이너가 생성하도록 해야합니다. 이렇게 생성된 bean은 트랜잭션, mybatis, jpa 등등 범용적으로 사용될 수 있습니다.

  - 예제

    ```xml
    <!-- DataSource 설정 --> 
    <bean id="sampleDataSource" class="org.apache.commons.dbcp2.BasicDataSource" destroy-method="close"> 
        <property name="driverClassName" value="${jdbc.sample.driverClassName}"/> <property name="url" value="${jdbc.sample.url}${jdbc.sample.connectionProperties}"/> 
        <property name="username" value="${jdbc.sample.username}"/> 
        <property name="password" value="${jdbc.sample.password}"/> <!-- 4개의 설정은 동일하게 설정하는 것이 예외 케이스를 줄일 수 있음 --> 
        <property name="initialSize" value="20"/> 
        <property name="maxTotal" value="20"/> 
        <property name="maxIdle" value="20"/> 
        <property name="minIdle" value="20"/> 
        <property name="defaultReadOnly" value="true"/> <!-- pool이 고갈되었을 경우 최대 대기 타임 ms --> 
        <property name="maxWaitMillis" value="3000"/> 
        <property name="validationQuery" value="SELECT 1"/> <!-- Evictor 설정 --> 		<property name="testOnBorrow" value="false"/> 
        <property name="testOnReturn" value="false"/> <!-- testWhileIdle 설정. 150초마다 4개의 connection 꺼내 validation query를 날려 확인 --> 
        <property name="testWhileIdle" value="true"/> 
        <property name="timeBetweenEvictionRunsMillis" value="150000"/> 
        <property name="numTestsPerEvictionRun" value="4"/> 
        <property name="minEvictableIdleTimeMillis" value="-1"/> <!-- preparedStatement 풀링 여부 --> 
        <property name="poolPreparedStatements" value="true"/> 
        <property name="maxOpenPreparedStatements" value="50"/> 
    </bean>
    ```

    

  