### [뒤로가기]( https://yunjae830.github.io/-Clearance/file/Connection )



# Java - JDBC , JAVA JDBC를 사용하여 MySQL과 연동

- 먼저 mysql-connector-java-8.0.18.jar 파일이 필요하기 때문에 아래 링크로 들어가서 다운 받을 수 있도록 합니다.

   https://dev.mysql.com/downloads/connector/j/ 

![](https://blogfiles.pstatic.net/MjAxOTExMTJfMjYx/MDAxNTczNTMyOTUyMTQ5.cg11qObdpBXfNj7y90n2-_9S71PPVjAr5ZLp4raAX0Ug.ftl4U_z-W4ooZlHYu-eEluhysjZKMef-12P8ioKv_aQg.PNG.jea830/20191112_132713.png)

- 위 사진에 보이는 ZIP 부분을 download 합니다.

- ![](http://postfiles13.naver.net/MjAxNzA2MTZfMjUy/MDAxNDk3NjE2MTEyMzgw.xJPyMYj9fEb-VgrLaSe_sjIwAzo-PaJf2cN34IJhLI0g.ehPnz9pAKzoIZRQVxjNH4oiqxqtF8EdLPcbHOCCq1lYg.PNG.lghlove0509/3.PNG?type=w580)

  압축을 풀고 안에 jar파일 필요하기 때문에 나머지는 지워도 됩니다.

- ![](http://postfiles2.naver.net/MjAxNzA2MTZfMTcx/MDAxNDk3NjE2MTUwMzcy.v8Rx-lKru76EDXMp5BZkK0p3YFWtGD974pIvghoRRjAg.K2MXDLpqZATk-kEpRnjGoT1o8RbrOqYw6pGgA6pTsJgg.PNG.lghlove0509/4.PNG?type=w580)

  위와 같이 프로젝트 생성 후 jar파일을 추가해줍니다.



### JDBC DB 연결 예제

```java
//insert
package kr.co.bizspring.DB;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.SQLException;

public class DBTest_Insert {

	public static void main(String[] args) {
		Connection con = null; // 데이터 베이스와 연결을 위한 객체
		PreparedStatement pstmt = null; // SQL 문을 데이터베이스에 보내기위한 객체
		// 1. JDBC Driver Class - com.mysql.jdbc.Driver
		String driver = "com.mysql.cj.jdbc.Driver";
		// 2. 데이터베이스에 연결하기 위한 정보
		String url = "jdbc:mysql://localhost/study_db?useUnicode=true&useJDBCCompliantTimezoneShift=true&useLegacyDatetimeCode=false&serverTimezone=UTC"; // 연결문자열
		String user = "root"; // 데이터베이스 ID
		String pw = "dbswo@1900"; // 데이터베이스 PW
		String SQL = "insert into student(name, studentNum) values(?, ?)";
		try {
			// 1. JDBC 드라이버 로딩 - MySQL JDBC 드라이버의 Driver Class 로딩
			Class.forName(driver);
			// 2. Connection 생성 - .getConnection(연결문자열, DB-ID, DB-PW)
			con = DriverManager.getConnection(url, user, pw);
			System.out.println("[ MySQL Connection ]\n");
			// 3. PreParedStatement 객체 생성, 객체 생성시 SQL 문장 저장
			pstmt = con.prepareStatement(SQL);
			// 4. pstmt.set<데이터타입>(? 순서, 값) ex).setString(), .setInt ...
			pstmt.setString(1, "유재석");
			pstmt.setString(2, "K반");
			// 5. SQL 문장을 실행하고 결과를 리턴 - SQL 문장 실행 후, 변경된 row 수 int type 리턴
			int r = pstmt.executeUpdate();
			// pstmt.excuteQuery() : select // pstmt.excuteUpdate() : insert, update, delete
			// ..
			System.out.println("변경된 row : " + r);
		} catch (SQLException e) {
			System.out.println("[SQL Error : " + e.getMessage() + "]");
		} catch (ClassNotFoundException e1) {
			System.out.println("[JDBC Connector Driver 오류 : " + e1.getMessage() + "]");
		} finally {
			// 사용순서와 반대로 close 함
			if (pstmt != null) {
				try {
					pstmt.close();
				} catch (SQLException e) {
					e.printStackTrace();
				}
			}
			if (con != null) {
				try {
					con.close();
				} catch (SQLException e) {
					e.printStackTrace();
				}
			}
		}

	}
}
```

```java
//select
package kr.co.bizspring.DB;

import java.sql.*;

public class DBTest {
	private final static String JDBC_DRIVER = "com.mysql.cj.jdbc.Driver";
	private final static String DB_URL = "jdbc:mysql://localhost/study_db?useUnicode=true&useJDBCCompliantTimezoneShift=true&useLegacyDatetimeCode=false&serverTimezone=UTC";
	
	private final static String USER_NAME = "root";
	private final static String PASSWORD = "dbswo@1900";
	
	public static void main(String[] args){
		Connection conn = null;
		Statement state = null;
		try {
			Class.forName(JDBC_DRIVER);
			conn = DriverManager.getConnection(DB_URL,USER_NAME,PASSWORD);
			System.out.println("[ MySQL Connection ]\n");
			state = conn.createStatement();
			
			String sql;
			sql = "SELECT * FROM student";
			ResultSet rs = state.executeQuery(sql);
			
			while(rs.next()) {
				String number = rs.getString("id");
				String name = rs.getString("name");
				String studentNum = rs.getString("studentNum");
				System.out.println("id : " + number + "\n" + "name : "+ name + "\n" + "studentNum : " + studentNum + "\n");
			}
			rs.close();
			state.close();
			conn.close();
		} catch(Exception e) {
			System.out.println(e);
		}finally {
			try {
				if(state!=null)
					state.close();
			} catch (Exception e2) {
				// TODO: handle exception
				System.out.println(e2);
			}
			try {
				if(conn!=null)
					conn.close();
			} catch (Exception e2) {
				// TODO: handle exception
				System.out.println(e2);
			}
		}
		System.out.println("MySQL Close");
	}
}
```

