- [ ] [뒤로가기](https://yunjae830.github.io/-Clearance/index)



# MySQL

###  user 생성과 권한

- `USE MYSQL;` MYSQL 데이터베이스 접속
- `select * from user;` user 테이블 확인
- `create user 'ID'@'%' identified by '비밀번호';` %는 외부접근 허용, localhost는 내부접근
- `FLUSH PRIVILEGES;` 사용자를 추가, 삭제, 권한 변경 등을 수행했을 때 변경 사항을 반영하기 위해서 사용
- `drop user 'ID';` 삭제
- `show grants;` 사용자 권한 확인
- `show grants for 'ID'@'%';` 부여된 권한 확인
- `grant all privileges on *.* to 'ID'@'%';` 사용자에게 모든 DB 모든 테이블에 모든 권한 부여
- `grant all privileges on DBname.* to 'ID'@'%';` 사용자에게 특정 DB에 대한 모든 권한 부여.
- `grant select, insert, update on DBname.* to 'ID'@'%';` 사용자에게 특정 DB에 대한 select, insert, update 권한 부여

### DB 권한

| **개발자** | **DELETE, INSERT, SELECT, UPDATE**                           |
| ---------- | ------------------------------------------------------------ |
| **설계자** | **ALTER, CREATE, DELETE, DROP, INDEX, INSERT, SELECT, UPDATE** |
| **DBA**    | **ALL**                                                      |

- `SHOW GRANTS` 자신의 권한이나, 특정 사용자의 권한을 열람한다.
- `REVOKE` 사용자의 권한을 제거 <문법> `REVOKE 권한 ON 데이터베이스.테이블 FROM 사용자`
- `DROP USER 'ID'@'%'` 사용자를 삭제

### Innodb

- transaction-safe 엔진
- commit, rollback, 장애복구, row-level locking, 외래키 등의 다양한 기능 지원
- row-level lock(행 단위) 을 사용하기 때문에 변경 작업(insert, update, delete) 속도가 빠름
- Full-text 인덱싱이 불가능
- 데이터 무결성 보장
- ibdata1, ibdata2 과 같은 파일에 index 및 파일데이터가 저장
- DB 및 테이블 정보는 /usr/local/mysql/data/DB명/테이블.frm 파일을 복사하여 백업
- MyIsam에 비해 약 1.5 ~ 2.5배 정도 파일이 커짐
- 테이블 단위의 Hat backup(파일복사)가 불가능
- mysqldump나 db 전체적인 복사가 필요

###  myisam  sequence

##### 하나의 Sequence 값만 필요한 경우

- **MySQL sequence emulator 테이블 준비**

  `CREATE TABLE id_generator (seq_currval BIGINT NOT NULL) ENGINE=MyISAM;`

  `INSERT INTO id_generator VALUES (0);`

- **Next sequence id 생성**

  `UPDATE id_generator SET seq_currval=LAST_INSERT_ID(seq_currval+1);`

- **생성된 Id 가져오기**

  `SELECT LAST_INSERT_ID();`

  또는

  `insert into datatable(fdkey, fd1) values (LAST_INSERT_ID(), '1st');`

##### 여러 개의 Sequence 값이 필요한 경우

- **MySQL sequence emulator 테이블 준비**

  ```sql
  CREATE TABLE id_generator (
    seq_name CHAR(1) NOT NULL COLLATE latin1_general_cs,
    seq_currval BIGINT UNSIGNED NOT NULL,
    PRIMARY KEY (seq_name)
  ) ENGINE=MyISAM DEFAULT CHARSET=latin1;
  ```

  ```sql
  INSERT INTO id_generator (seq_name, seq_currval) VALUES (‘B’,0); -- // Board Id
  INSERT INTO id_generator (seq_name, seq_currval) VALUES (‘D’,0); -- // Document Id
  INSERT INTO id_generator (seq_name, seq_currval) VALUES (‘C’,0); -- // Comment Id
  ```

- **Next sequence id 생성**

  ```sql
  UPDATE id_generator SET seq_currval=LAST_INSERT_ID(seq_currval+1) WHERE seq_name=’B’;
  UPDATE id_generator SET seq_currval=LAST_INSERT_ID(seq_currval+1) WHERE seq_name=’D’;
  UPDATE id_generator SET seq_currval=LAST_INSERT_ID(seq_currval+1) WHERE seq_name=’C’;
  ```

- **생성된 Sequence Id 가져오기**

  ```sql
  SELECT LAST_INSERT_ID();
  또는
  INSERT INTO board (board_id, board_name) VALUES (LAST_INSERT_ID(), ‘ThemeBoard’);
  ```

### auto increment

MySQL의 경우 시퀀스 생성이 간단합니다. 테이블 생성 시 auto_increment라는 속성을 시퀀스로 지정해줄 컬럼에 적용만 해주면 됩니다.

```mysql
create table sequence_table(
	seq int auto_increment primary key,
    title varchar(20)
)
```

##### MySQL 자동증가, auto increment 값 초기화 하기

```mysql
alter table [테이블 명] auto_increment = [시작할려는 순서]
```

### Limit

결과 값을 제한하는 명령어

- ```sql
  SELECT 열명 FROM 테이블명 LIMIT 시작행, 개수
  ```

  시작행을 0으로 지정하고 개수를 5개를 지정하면 시작행이 0부터 5건의 데이터를 보여주게 됩니다.

- ```mysql
  SELECT 열명 FROM 테이블명 LIMIT 행수 OFFSET 위치
  ```

  행수를 3으로 지정하고 OFFSET을 0으로 나타내면 결과값은 1,2,3이 나오게 됩니다. 만약 OFFSET을 3으로 지정하면 4부터 시작하게됩니다.