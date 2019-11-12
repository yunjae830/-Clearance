### [뒤로가기]( https://yunjae830.github.io/-Clearance/file/Connection )



# hikariCP가 그렇게 빠르다던데?

### 장점

- 빠르며 Multi core CPU를 지원한다.
- BoneCP 보다 더 빠른 CP



### 1. pom.xml 설정

```xml
<dependency>
	<groupId>com.zaxxer</groupId>
	<artifactId>HikariCP</artifactId>
	<version>2.4.6</version>
</dependency>
```



### 2. properties

- 직접 설정할 수도 있고 여러가지 방법들이 있다보니 굳이 properties에 작성해야하는 것은 아니지만, 소스에 하드코딩하는 것이 아닌 파일이나 DB로 가지고 있는 것을 선호하기 때문에 properties를 작성합니다.

  ```properties
  #HikariMySQL.properties
  # 데이터소스 클래스 위치 (드라이버가 아닙니다.!!)
  dataSourceClassName=com.mysql.jdbc.jdbc2.optional.MysqlDataSource
  # 접속 url
  dataSource.url=jdbc:mysql://<호스트>:<포트>/<db명>?useSSL=true&useUnicode=yes&characterEncoding=utf-8
  dataSource.user=<계정>
  dataSource.password=<암호>
  # 옵션들
  dataSource.cachePrepStmts=true
  dataSource.prepStmtCacheSize=100
  dataSource.prepStmtCacheSqlLimit=2048
  dataSource.useServerPrepStmts=true
  maximumPoolSize=10
  ```



### 3. 연결 설정

- HikariMySQL.properties를 통해 설정파일을 불러옵니다.

- 설정을 소스로 사용하거나 수동으로 넣기 위해서는 conf.load 대신 conf에 직접 넣으면 됩니다.

  ```java
  @Configuration
  @EnableJpaRepositories
  (
  	basePackages = "com.web.repository.mysql", 
  	entityManagerFactoryRef = "mysqlFactoryBean", 
  	transactionManagerRef = "mysqlTransactionManager"
  )
  public class JdbcMysqlConfig
  {
  	@Bean(name = "mysqlDataSource")
  	public DataSource getDataSource() throws IOException
  	{
  		Properties conf = new Properties();
  		conf.load(DataSourceFactory.class.getClassLoader().getResourceAsStream("HikariMySQL.properties"));
  		return new HikariDataSource(new HikariConfig(conf));
  	}
  
  	@Bean(name = "mysqlFactoryBean")
  	public LocalContainerEntityManagerFactoryBean getFactoryBean(EntityManagerFactoryBuilder builder) throws IOException
  	{
  		return builder.dataSource(getDataSource()).packages("com.web.domain.mysql").build();
  	}
  
  	@Bean(name = "mysqlTransactionManager")
  	PlatformTransactionManager getTransactionManager(EntityManagerFactoryBuilder builder) throws IOException
  	{
  		return new JpaTransactionManager(getFactoryBean(builder).getObject());
  	}
  }
  ```



### 4. 실행

- 실행시에 아래와 같은 문구가 나오면 연결 성공

  -  시작 

    HikariPool-<번호> - Started. 

  - 등록

     Located MBean <DataSource빈이름>: registering with JMX server as MBean [com.zaxxer.hikari:name=<DataSource빈이름>,type=HikariDataSource] 



