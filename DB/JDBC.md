# JDBC

- [JDBC란](#jdbc란)
  - [JDBC Driver](#jdbc-driver)
  - [DriverManager](#drivermanager)
  - [Connection](#connection)
  - [Statement, PreparedStatement](#statement-preparedstatement)
  - [CollableStatement](#callablestatement)
  - [ResultSet](#resultset)
  - [주의사항](#주의사항)
  - [쿼리문 전송 시점](#쿼)
- [JDBC 생명주기](#쿼리문-전송-시점)
  - [1. DriverManager가 JDBC 드라이버 탐색](#1-drivermanager가-jdbc-드라이버-탐색)
  - [2. JDBC Driver를 통해 연결 시도](#2-jdbc-driver를-통해-연결-시도)
  - [3. 소켓 연결(TCP/IP 네트워크 연결)](#3-소켓-연결tcpip-네트워크-연결)
  - [4. DB HandShake & 인증](#4-db-handshake--인증)
  - [5. DB 세션 생성](#5-db-세션-생성)
  - [6. JDBC Connection 객체 반환](#6-jdbc-connection-객체-반환)
- [Connection Pool](#connection-pool)
- [SQL 처리 과정](#sql-처리-과정)


## JDBC란
JDBC란 자바에서 DB와 연결해서 데이터 입출력 작업을 할 수 있는 기능을 제공하는 라이브러리이다. DBMS 종류와 상관없이 동일하게 사용할 수 있는 클래스와 인터페이스로 구성되어 있다.

<img width="551" height="281" alt="Image" src="https://github.com/user-attachments/assets/d577c3cf-fd9f-43ad-a860-4e2aa143ffce" />

*출처: https://devlog-wjdrbs96.tistory.com/139*

### JDBC Driver
JDBC를 통해 실제로 DBMS와 작업하는 것은 JDBC Driver이다.
- JDBC Driver: **JDBC 인터페이스의 구현체**
  - DBMS마다 별도로 구현체가 존재하며, 필요 시 다운로드받아 사용한다.

### DriverManager
JDBC Driver를 관리하며 DB와 연결해서 **Connection 구현체를 생성**한다.

### Connection
Statement, PreparedStatement, CallableStatement 구현체를 생성한다.<br>
트랜잭션 처리 및 DB 연결 종료 시에 사용된다.

### Statement, PreparedStatement
둘 다 SQL의 DDL, DML을 실행할 때 사용된다.<br>
차이점은 다음과 같다.

| |Statement|PreparedStatement|
|:--:|:--:|:--:|
|파라미터|런타임 중 파라미터 전달 불가|런타임 중 파라미터 전달 가능|
|컴파일|실행될 때마다|컴파일 후 캐싱하여 재사용|
|성능|낮은 편|비교적 높음|
|바이너리 데이터|처리 불가|처리 가능|
|사용처|한 번만 실행되는 쿼리|여러 번 사용되는 쿼리|
| |DDL(create, alter, drop)|동적 쿼리|

일반적으로 DML문을 위주로 사용하므로 PreparedStatement를 더 자주 사용한다.

### CallableStatement
DB에 저장되어 있는 프로시저나 함수를 호출할 때 사용된다.

### ResultSet
DB에서 가져온 데이터(select)를 읽을 때 사용한다.<br>
ResultSet의 데이터를 DTO 객체의 필드에 매핑하여 조회 결과를 활용한다.


### 주의사항
Connection, Statement, ResultSet은 자원이므로 사용한 뒤에는 반드시 close() 메서드를 호출해서 안전하게 **종료**해줘야한다.<br>
종료해주지 않으면? 사용되지 않는 잉여자원이 계속해서 생성되어 성능 저하 및 리소스 낭비, 더 나아가 보안 문제 발생 가능성이 생긴다.

### 쿼리문 전송 시점
PreparedStatement를 이용한 메서드(executeUpdate(), executeQuery() 등)가 호출되는 즉시 DB로 쿼리문이 전송된다.
- 단, 배치 처리의 경우 드라이버 내부에 쌓아뒀다가 한 번에 전송한다.

## JDBC 생명주기

### 1. DriverManager가 JDBC 드라이버 탐색
- DriverManager.getConnection() 호출 시 JVM에 로드된 JDBC 드라이버 목록에서 URL에 맞는 드라이버 탐색
  ```java
  // MySQL Driver
  Connection con = DriverManager.getConnection(
    "jdbc:mysql://localhost:3306/madang"
    , username
    , password);
  ```
  ⇒ Service Loader 매커니즘으로 *META-INF/service/java.sql.Driver* 에 등록된 드라이버들이 사용된다.

### 2. JDBC Driver를 통해 연결 시도
- 해당 드라이버의 connect() 메서드 호출
- 이때 URL, username, password 등 인정 정보를 받아 DB 서버와 실제 연결 절차를 진행

### 3. 소켓 연결(TCP/IP 네트워크 연결)
- 드라이버는 내부적으로 DB 서버와 TCP 소켓 연결을 맺는다.
  - ex) MySQL: 3306 port
  - ex) Oracle: 1521 port
- 해당 시점에서 **네트워크 레벨**의 *TCP 3-WAY Handshake*가 진행된다.

### 4. DB HandShake & 인증
- DB 서버는 클라이언트(Driver)와 DB Protocol Handshake을 통해 세부 설정을 교환한다.

### 5. DB 세션 생성
- 인증 성공 시 DB 서버는 새로운 Session을 생성한다.
- 관리 요소:
  - 사용자 권한
  - 트랜잭션 상태
  - 세션 변수(ex: autocommit, timezone 등)
  - 커서, proparedStatement 캐시
  - 등등

### 6. JDBC Connection 객체 반환
- 드라이버는 DB 세션과 연결된 Connection 구현체 객체를 생성하여 반환
- 애플리케이션에서 이 객체를 활용해 SQL을 수행
  - createStatement
  - preparedStatement
  - commit
  - rollback

## Connection Pool

<img width="1003" height="586" alt="Image" src="https://github.com/user-attachments/assets/b932011d-2510-4bf7-b8df-f7538fddd7b7" />

*출처: https://dkswnkk.tistory.com/685*

위의 Driver 탐색 ~ 세션 생성 과정은 비싼 작업이다.<br>
따라서 커넥션을 매 쿼리마다 생성하고 사용한 뒤 해제하는 것은 비효율적이다.<br>
커넥션 풀은 사전에 정의한 개수만큼 미리 커넥션을 생성하여 보관한 뒤, 보관된 커넥션을 재활용하여 사용하는 개념이다.
- 완전 고정적인 수의 커넥션을 유지하는 것이 아니라, 최소/최대 개수를 정의한 뒤 항상 최소 개수 이상의 커넥션은 유지하고 그 외에는 동적으로 개수를 조절한다.
- 애플리케이션 시작 시점에 커넥션 풀 설정 정보를 이용하여 생성된다.


## SQL 처리 과정

<img width="550" height="642" alt="Image" src="https://github.com/user-attachments/assets/e1a49ec1-678e-493a-ae99-135aa5c593ec" />

*출처: https://velog.io/@be_chobo/JDBC-%EA%B8%B0%EB%B0%98-%EB%8D%B0%EC%9D%B4%ED%84%B0-%EC%95%A1%EC%84%B8%EC%8A%A4-%EA%B3%84%EC%B8%B51-JDBC%EB%9E%80*

1. JDBC Driver Class 로드
2. Connection 생성
3. Statement/PreparedStatement 생성
4. SQL 실행
5. 결과 처리
   - select의 경우 결과를 ResultSet으로 순회하면서 행을 읽는다.
   - Connection이 열려있어야 사용할 수 있다.
6. 트랜잭션 처리
   - Auto-Commit이 기본 - 각 SQL 실행 직후 자동 커밋
7. 자원 생성 역순으로 해제
   - (select의 경우) ResultSet close
   - Statement/PreparedStatement close
   - Connection close
