### [뒤로가기]( https://yunjae830.github.io/-Clearance/file/Connection )



# DBCP 구현 - DB Connection Pooling

### Pooling 기법

- 미리 여러 개의 데이터베이스 Connection을 생성해서 보관
- Connection이 필요할 때마다 Connection Pool로부터 하나씩 꺼내서 사용하고 사용이 끝나면 다시 Connection을 보관
- Connection Pool이 데이터베이스의 연결과 해제를 직접 관리



### instance를 이용해서 DB작업을 한다. (Singleton 패턴으로, 하나의 Manager만 생성해서 사용한다.)

1. getConnection() 으로 Connection을 받아온다.
2. Connection에 PSMT나 STMT를 사용해서 쿼리를 실행(되도록 PSMT를 사용. 캐싱을 하므로 동일쿼리 수행시 성능이 훨씬 좋다.)
3. 해당 connection을 free 해줘야 한다.



### 사용 예

```java
		try {
                pstmt = connection.prepareStatement("SELECT * FROM SOME_TABLE WHERE 조건 등등");
                rs = pstmt.executeQuery();

                while (rs.next()) {
                    String gameCode = rs.getString("가져올 Column값(String)");
                    long period = rs.getInt("가져올 Column 값(long)");
                }

            } catch (SQLException e) {
                logger.error("{}", e.getMessage());
            } finally {
                // 매우 중요! Connection을 사용하고 반납을 해야 Pooling이 된다.
                DBManager.getInstance().freeConnection(connection, pstmt, rs);
            }
```

```java
import org.apache.commons.dbcp.ConnectionFactory;
import org.apache.commons.dbcp.DriverManagerConnectionFactory;
import org.apache.commons.dbcp.PoolableConnectionFactory;
import org.apache.commons.dbcp.PoolingDriver;
import org.apache.commons.pool.impl.GenericObjectPool;
import org.slf4j.LoggerFactory;

import java.sql.*;


/**
 * Created by stacks5978 on 2017-04-28.
 * DB Connection Pool을 구현한 Manager 클래스
 */
public class DBManager {

    protected final static org.slf4j.Logger logger = LoggerFactory.getLogger(DBManager.class);

    private static DBManager instance;

    synchronized public static DBManager getInstance() {

        try {
            if (instance == null) {
                instance = new DBManager();
                logger.info("DBManager initialize: {}", instance);
            }
        } catch (Exception e) {
            e.printStackTrace();
        }

        return instance;
    }

    private DBManager() {

        // Connection을 초기화
        initFirstConnection();

        // TODO : 다른 DB 사용 시 해당 DB 초기화.
    }

    private void initFirstConnection(){
        // Config Setting
        try {
            setupFirstDriver();
        } catch (Exception ex) {
            logger.error("Exception : {}", ex.getMessage());
        }
        logger.info("FirstConnection Created");
    }

    /**
     * Comeback Player Connection을 리턴함
     *
     * @return DB Connection
     */
    public Connection getConnection(DBConnectionType type) {
        Connection con = null;

        try {
            con = DriverManager.getConnection("jdbc:apache:commons:dbcp:first_connection");
        } catch (SQLException ex) {
            logger.error("SQLException : {}", ex.getMessage());
        }
        // TODO : Connection Type에 따라 분기.

        return con;
    }

    /**
     *  Connection Pool 설정
     *
     * @throws Exception
     */
    public void setupFirstDriver() throws Exception {
        // JDBC 드라이버 로딩(MSSQL 드라이버를 가져옴. 사용하는 jdbc 드라이버를 로드하면 됨
        Class.forName(Configuration.getProperty("com.microsoft.sqlserver.jdbc.SQLServerDriver"));

        // Connection Pool 생성, 옵션세팅
        GenericObjectPool connectionPool = new GenericObjectPool(null);
        connectionPool.setMaxActive(45);
        connectionPool.setMinIdle(4);
        connectionPool.setMaxWait(15000);
        connectionPool.setTimeBetweenEvictionRunsMillis(3600000);
        connectionPool.setMinEvictableIdleTimeMillis(1800000);
        connectionPool.setMaxIdle(45);
        connectionPool.setTestOnBorrow(true);

        // 실제 DB와의 커넥션을 연결해주는 팩토리 생성
        ConnectionFactory connectionFactory = new DriverManagerConnectionFactory(
                Configuration.getProperty("Database Remote URL"), // JDBC URL
                Configuration.getProperty("gompang"), // 사용자
                Configuration.getProperty("gompang_password"));

        // Connection Pool이 PoolableConnection 객체를 생성할 때 사용할
        // PoolableConnectionFactory 생성
        PoolableConnectionFactory poolableConnectionFactory = new PoolableConnectionFactory(
                connectionFactory,
                connectionPool,
                null, // statement pool
                "SELECT 1", // 커넥션 테스트 쿼리: 커넥션이 유효한지 테스트할 때 사용되는 쿼리.
                false, // read only 여부
                false); // auto commit 여부

        // Pooling을 위한 JDBC 드라이버 생성 및 등록
        PoolingDriver driver = new PoolingDriver();

        // JDBC 드라이버에 커넥션 풀 등록
        driver.registerPool("first_connection", connectionPool);
    }

    /*
        Connection Pool에 free 및 객체 소멸 함수들
     */
    public void freeConnection(Connection con, PreparedStatement pstmt, ResultSet rs) {
        try {
            if (rs != null) rs.close();
            if (pstmt != null) pstmt.close();
            freeConnection(con);
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }

    public void freeConnection(Connection con, Statement stmt, ResultSet rs) {
        try {
            if (rs != null) rs.close();
            if (stmt != null) stmt.close();
            freeConnection(con);
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }

    public void freeConnection(Connection con, PreparedStatement pstmt) {
        try {
            if (pstmt != null) pstmt.close();
            freeConnection(con);
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }

    public void freeConnection(Connection con, Statement stmt) {
        try {
            if (stmt != null) stmt.close();
            freeConnection(con);
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }

    public void freeConnection(Connection con) {
        try {
            if (con != null) con.close();
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }

    public void freeConnection(Statement stmt) {
        try {
            if (stmt != null) stmt.close();
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }

    public void freeConnection(PreparedStatement pstmt) {
        try {
            if (pstmt != null) pstmt.close();
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }

    public void freeConnection(ResultSet rs) {
        try {
            if (rs != null) rs.close();
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }
}

```



