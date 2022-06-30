# 트랜잭션 (Transaction)

데이터베이스의 상태를 변화시키기 위해서 수행하는 작업의 단위를 뜻한다.

모든 작업이 성공해서 데이터베이스에 정상 반영하는 것을 커밋( Commit )이라 하고, 작업 중 하나라도 실패해서 거래 이전으로 되돌리는 것을 롤백( Rollback )이라 한다.

## 트랜잭션 ACID

트랜잭션은 ACID라 하는 원자성(Atomicity), 일관성 (Consistency), 격리성(Isolation), 지속성(Durability)을 보장해야 한다.

- **원자성 :** 트랜잭션 내에서 실행한 작업들은 마치 하나의 작업인 것처럼 모두 성공 하거나 모두 실패해야 한다.
- **일관성 :** 모든 트랜잭션은 일관성 있는 데이터베이스 상태를 유지해야 한다. 예를 들어 데이터베이스에서 정한 무결성 제약 조건을 항상 만족해야 한다.
- **격리성 :** 동시에 실행되는 트랜잭션들이 서로에게 영향을 미치지 않도록 격리한다. 예를 들어 동시에 같은 데이터를 수정하지 못하도록 해야 한다. 격리성은 동시성과 관련된 성능 이슈로 인해 트랜잭션 격리 수준 (Isolation level)을 선택할 수 있다.
- **지속성 :** 트랜잭션을 성공적으로 끝내면 그 결과가 항상 기록되어야 한다. 중간에 시스템에 문제가 발생해도
  데이터베이스 로그 등을 사용해서 성공한 트랜잭션 내용을 복구해야 한다.

## **트랜잭션 격리 수준 - Isolation level**

READ UNCOMMITED(커밋되지 않은 읽기)
READ COMMITTED(커밋된 읽기)
REPEATABLE READ(반복 가능한 읽기)
SERIALIZABLE(직렬화 가능)

## **데이터베이스 연결 구조와 DB 세션**

<img width="845" alt="Untitled" src="https://user-images.githubusercontent.com/87989933/176586518-8799126d-1ca7-4265-a789-576b4914350f.png">

사용자는 웹 애플리케이션 서버(WAS)나 DB 접근 툴 같은 클라이언트를 사용해서 데이터베이스 서버에 접근할 수 있다. 클라이언트는 데이터베이스 서버에 연결을 요청하고 커넥션을 맺게 된다.

이때 데이터베이스 서버는 내부에 세션이라는 것을 만든다. 그리고 앞으로 해당 커넥션을 통한 모든 요청은 이 세션을 통해서 실행하게 된다. 쉽게 이야기해서 개발자가 클라이언트를 통해 SQL을 전달하면 현재 커넥션에 연결된 세션이 SQL을 실행한다.

세션은 트랜잭션을 시작하고, 커밋 또는 롤백을 통해 트랜잭션을 종료한다. 그리고 이후에 새로운 트랜잭션을 다시 시작할 수 있다. 사용자가 커넥션을 닫거나, 또는 DBA(DB 관리자)가 세션을 강제로 종료하면 세션은 종료된다.

### **트랜잭션 사용법**

데이터 변경 쿼리를 실행하고 데이터베이스에 그 결과를 반영하려면 커밋 명령어인 **commit** 을 호출하고, 결과를 반영하고 싶지 않으면 롤백 명령어인 **rollback** 을 호출하면 된다.

**커밋을 호출하기 전까지는 임시로 데이터를 저장**하는 것이다. 따라서 해당 트랜잭션을 시작한 세션(사용자) 에게만 변경 데이터가 보이고 다른 세션(사용자)에게는 변경 데이터가 보이지 않는다.

<img width="862" alt="Untitled (1)" src="https://user-images.githubusercontent.com/87989933/176586495-6a5011e5-8acf-4424-9dbf-5046a48fae5e.png">

**수동 커밋 -** set autocommit false

보통 자동 커밋 모드가 기본으로 설정된 경우가 많기 때문에, **수동 커밋 모드로 설정하는 것을 트랜잭션을
시작**한다고 표현할 수 있다. 수동 커밋 설정을 하면 이후에 꼭 commit , rollback 을 호출해야 한다.

참고로 수동 커밋 모드나 자동 커밋 모드는 한번 설정하면 해당 세션에서는 계속 유지된다. 중간에 변경하는 것은 가능하다.

## 📌 트랜잭션 전파

<img width="834" alt="Untitled (2)" src="https://user-images.githubusercontent.com/87989933/176586509-bac46008-0a5a-4602-914c-89b7cb903e5f.png">

외부 트랜잭션이 수행중이고, 아직 끝나지 않았는데, 내부 트랜잭션이 수행된다.

**외부 트랜잭션이**라고 이름 붙인 것은 둘 중 상대적으로 밖에 있기 때문에 외부 트랜잭션이라 한다.
처음 시작된 트랜잭션으로 이해하면 된다.

**내부 트랜잭션**은 외부에 트랜잭션이 수행되고 있는 도중에 호출되기 때문에 마치 내부에 있는 것 처럼 보여서 내부 트랜잭션이라 한다.

<img width="833" alt="Untitled (3)" src="https://user-images.githubusercontent.com/87989933/176586512-3481dff6-2312-4b55-abd2-771d1eb6033a.png">

스프링은 이 경우 외부 트랜잭션과 내부 트랜잭션을 묶어서 하나의 트랜잭션을 만들어준다. 내부 트랜잭션이 외부 트랜잭션에 참여하는 것이다. 이것이 기본 동작이고, 옵션을 통해 다른 동작방식도 선택할 수 있다.

<img width="836" alt="Untitled (4)" src="https://user-images.githubusercontent.com/87989933/176586517-13d8e41d-2ade-4e8a-90ba-c569441b21f1.png">

스프링은 이해를 돕기 위해 **논리 트랜잭션과 물리 트랜잭션**이라는 개념을 나눈다.
논리 트랜잭션들은 하나의 물리 트랜잭션으로 묶인다.

물리 트랜잭션은 우리가 이해하는 실제 데이터베이스에 적용되는 트랜잭션을 뜻한다. 실제 커넥션을 통해서 트랜잭션을 시작하고, 실제 커넥션을 통해서 커밋, 롤백하는 단위이다.
논리 트랜잭션은 트랜잭션 매니저를 통해 트랜잭션을 사용하는 단위이다.

이러한 논리 트랜잭션 개념은 트랜잭션이 진행되는 중에 내부에 추가로 트랜잭션을 사용하는 경우에 나타난다.
단순히 트랜잭션이 하나인 경우 둘을 구분하지는 않는다.

### ❗️**원칙**

- **모든 논리 트랜잭션이 커밋되어야 물리 트랜잭션이 커밋된다.**
- **하나의 논리 트랜잭션이라도 롤백되면 물리 트랜잭션은 롤백된다.**

→ 모든 트랜잭션 매니저를 커밋해야 물리 트랜잭션이 커밋된다. 하나의 트랜잭션 매니저라도 롤백하면 물리 트랜잭션은 롤백된다.

## 참고

인프런 - 김영한님 강의 Spring MVC DB1편,2편