---
layout: post
title: Statement vs Prepared Statement
tags: [CS, 데이터베이스]
color: rgb(51,123,225) 
author: aerimforest
excerpt_separator: <!--more-->
---

## Statement
데이터베이스에 액세스 하는 데 사용된다. `Statement` 인터페이스는 매개 변수를 허용할 수 없으며 런타임에 정적 SQL문을 사용할 때 유용하다.  
SQL 쿼리를 <u>한 번만 실행</u>하려는 경우 Statement 인터페이스가 PreparedStatement보다 선호된다.

<!--more-->

```sql
Connection con = DriverManager.getConnection();

// Statement 객체 생성
Statement stmt = con.createStatement();
  
// Statement 실행
stmt.executeUpdate("CREATE TABLE STUDENT(ID NUMBER NOT NULL, NAME VARCHAR)");
```

<br><br>

## PreparedStatement
SQL 명령문을 DBMS에 보낼 때마다 DBMS는 매번 아래의 작업을 반복하게 된다.
```
1️⃣ SQL문자열 컴파일
2️⃣ 컴파일 된 SQL 명령문 실행
3️⃣ 실행 결과를 호출자에게 리턴
```
만약 우리가 특정 테이블에 계속해서 데이터를 입력할 경우, SQL 명령문은 대부분 동일하고 입력되는 데이터 값만 다를 것이다.  

따라서 위의 세가지 과정을 매번 반복하는 것보다는 `재사용`하는 것이 바람직할 것이고, 그래서 등장한 것이 `PreparedStatement`이다.

나중에 값을 설정하기 위해 `?`을 사용하고 이 기호는 1부터 시작되는 인덱스를 가진다.  
`?`은 전달되는 위치에서만 사용될 수 있고, 테이블이나 컬럼 이름과 같은 위치에는 사용될 수 없다.

PreparedStatement 인터페이스를 사용하면 재사용성이 높아질 뿐만 아니라 코드가 간결해지기에 가독성도 높아진다.  

PreparedStatement 인터페이스는 `런타임`에서 입력 매개 변수를 허용한다.

<br>

1~10번까지의 사원의 이름을 조회하는 경우를 생각해보자.  

1) Statement 객체 사용
```sql
Statement stmt = con.createStatment(); 

for (int i = 1; i <= 10; i++) {
    rs = stmt.executeQuery(“select * from emp where no=“+i+ “ and name=‘”+name[i]+”’”);
}
```
DBMS가 매번 쿼리를 새로 생성해야 할 뿐만 아니라 “, +, ‘ 등의 잦은 사용으로 인해 가독성도 저하된다.  

<br>

2) PreparedStatement 객체 사용
```sql
PreparedStatement pstmt = con.prepareStatement(“select * from emp where no=? and name=?”); 

for (int i = 1; i <= 10; i++) {
    pstmt.setInt(1, i); 
    pstmt.setString(2, name[i]); 
    rs = pstmt.executeQuery();
}
```
반면 PreparedStatement 객체를 사용할 경우 `?` 자리에 필요한 컬럼 값을 쉽게 읽을 수 있기 때문에 가독성도 높고 재사용성도 좋아진다.

<br><br>

## 한눈에 비교하는 Statement vs PreparedStatement

|`Statement`|`PreparedStatement`|
|:--:|:--:|
|SQL 쿼리 한 번 실행시 사용|SQL 쿼리 여러 번 실행시 사용|
|런타임에 매개 변수 전달 불가능|런타임에 매개 변수 전달 가능|
|CREATE, ALTER, DROP 문에 사용|여러 번 실행될 쿼리에 사용|
|성능 낮음|성능 우수|
|기본 인터페이스|Statement 확장한 것|
|일반 SQL 쿼리 실행시 사용|동적 SQL 쿼리 실행시 사용|
|이진 데이터 읽기/쓰기 불가능|이진 데이터 읽기/쓰기 가능|
|DDL문에 사용<br>(CREATE, ALTER, DROP, TRUNCATE)|모든 SQL 쿼리에 사용|

<br><br>

## References
- [geeksforgeeks.org/difference-between-statement-and-preparedstatement](https://www.geeksforgeeks.org/difference-between-statement-and-preparedstatement/)

