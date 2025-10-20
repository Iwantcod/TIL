# MVCC

- [개념](#개념)
- [격리 수준](#격리-수준isolation-level)
- [Read View(스냅샷)](#read-view스냅샷)
  - [가시성 판단 공식](#가시성-판단-공식)
  - [Redo/Undo Log](#redoundo-log)
  - [트랜잭션 이상 현상](#트랜잭션-이상-현상)
- [Read View: Repeatable Read](#read-view-repeatable-read)
- [Read View: Read Committed](#read-view-read-committed)
- [트랜잭션 격리 수준 차이점 예시](#repeatable-readrr-read-committedrc-차이점-예시)
- [MVCC Update 작업 처리](#mvcc-update-작업-처리)

## 개념

<img width="1374" height="696" alt="Image" src="https://github.com/user-attachments/assets/4fbc155e-fee8-48cf-8879-8795b733db94" />

MVCC란? Multi-Version Concurrency Control 즉 다중 버전 동시성 제어의 약자이다. 동시 접근을 허용하는 DB에서 동시성을 제어하기 위한 방법 중 하나이다. 트랜잭션 간의 격리 수준을 통해 동시성을 제어한다.

## 격리 수준(Isolation Level)
격리 수준은 4가지로 구분된다.

|격리 레벨|격리 범위|
|--|--|
|Read Uncommitted|다른 트랜잭션에서 커밋하지 않은 데이터도 조회할 수 있다.|
|Read Committed|이미 커밋된 데이터만 조회할 수 있다.|
|**Repeatable Read**|트랜잭션 생성 시점에서 이미 커밋되었던 데이터만 조회할 수 있다.|
|Serializable|여러 트랜잭션이 동일한 레코드에 접근할 수 없다.|

*Default(MySQL/MariaDB): **Repeatable Read***

그렇다면 DBMS에서는 어떤 원리로 트랜잭션 격리 수준에 따라 데이터의 조회 여부를 검증하는 것일까?

## Read View(스냅샷)
각 트랜잭션에서는 Read View라는 데이터를 통해 격리 수준에 따라 조회 가능 여부를 판단할 수 있다.<br>
Read View는 스냅샷이라고도 하며, 특정 버전의 데이터(레코드)를 현재 트랜잭션에서 조회할 수 있는지를 검증하기 위한 메타데이터이다. Read View의 구성 요소는 다음과 같다.

||구성 요소|역할|
|--|--|--|
|1|creator_trx_id|Read View를 생성한 트랜잭션 ID|
|2|up_limit_id|Read View 생성 시점에 '아직 시작되지 않은' 트랜잭션 ID의 최소값|
|3|low_limit_id|Read View 생성 시점에 '수행 중'인 트랜잭션 중 가장 작은 ID|
|4|active_trx_ids|Read View 생성 시점에 '수행 중'인 트랜잭션 ID 리스트|

### 가시성 판단 공식
특정 버전의 데이터를 조회할 수 있는지에 대한 여부를 검증하는 로직은 다음과 같다.
```java
// trx_id: InnoDB 버퍼 풀의 레코드 메타데이터
if (trx_id < low_limit_id) {
    return TRUE; // 이미 커밋된 트랜잭션
}
if (trx_id >= up_limit_id) {
    return FALSE; // 아직 시작하지 않은 미래 트랜잭션
}
if (trx_id in active_trx_ids) {
    return FALSE; // 아직 커밋되지 않은 트랜잭션
}
return TRUE; // 그 외의 경우 커밋된 상태로 판단
```

>ex) 트랜잭션 1, 2, 3이 존재하고 2만 종료되었으며 트랜잭션 4에서 조회하는 상황을 가정해보자.

low_limit_id: 1, up_limit_id: 5, active_trx_ids: {1, 3}<br>
InnoDB 버퍼풀의 레코드 trx_id가 1, 3인 경우 3번째 조건문에서 걸러져 '조회 불가'하다. 이 경우 레코드 메타데이터의 undo log 포인터를 따라 해당 레코드의 undo log를 조회한다.<br>
trx_id가 2인 데이터의 경우 모든 조건문에 해당되지 않으므로 '조회 가능'하다. 버퍼 풀이 레코드를 그대로 조회한다.

> low_limit_id는 active_trx_ids 중 최소값이다. 따라서 이 값보다 낮은 값의 trx_id는 모두 종료(커밋)된 트랜잭션이라는 사실이 보장된다.

### Redo/Undo Log

<img width="1360" height="1200" alt="Image" src="https://github.com/user-attachments/assets/589189b8-012b-4bdb-bd83-638de5394ba3" />

모든 쓰기 작업 시 Redo/Undo Log가 생성되어 별도의 테이블에 저장된다.<br>
**Redo Log**: 쓰기 작업 도중 충돌 발생 시 복구를 위한 데이터이다. 커밋 시 InnoDB는 Redo Log를 디스크에 플러시하여 물리적인 쓰기 작업 시 충돌이 발생해도 변경사항이 반영되도록 한다. <br>
**Undo Log**: 이전 버전의 복구를 위한 데이터이다. 별도의 테이블에 저장되지 않고 undo tablespace에 저장된다. SELECT 시 Undo Log를 조회할 수 있다.


### 트랜잭션 이상 현상
|현상|설명|
|--|--|
|Dirty Read|다른 트랜잭션이 **아직 커밋하지 않은** 데이터를 읽는 현상|
|Non-Repeatable Read|같은 트랜잭션 내에서 같은 조건으로 두 번 조회했는데 **결과가 다른** 현상|
|Phantom Read|같은 트랜잭션 내에서 동일 쿼리를 반복 실행할 때 **행의 수(존재 여부)**가 달라지는 현상|
|Lost Update|서로 다른 트랜잭션이 같은 데이터를 동시에 갱신하면서 한 쪽의 변경 사항이 **논리적으로 덮어씌워지는** 현상|

'논리적으로 덮어씌워진다'는 것은 애플리케이션 로직에서의 경우를 뜻한다.

<img width="655" height="218" alt="Image" src="https://github.com/user-attachments/assets/b0ecb009-edc9-445b-8e23-db95ad386330" />

## Read View: Repeatable Read
Repeatable Read 격리 레벨에서는 `트랜잭션 시작 시 스냅샷을 생성`하고, 트랜잭션 종료 시 까지 해당 스냅샷을 **재활용**한다. 즉, 트랜잭션 생성 시점에서 '이미 커밋되었던' 데이터만 조회할 수 있다.<br>
이 트랜잭션이 수행되는 도중 다른 트랜잭션에서 새로운 커밋이 발생해도, 그러한 새 버전의 데이터는 이 트랜잭션에서는 '조회 불가'하다.<br><br>
Repeatable Read에서는 Phantom Read, Non-Repeatable Read 문제가 발생하지 않는다. 동일 트랜잭션 내의 select, select for update의 결과가 서로 달라질 수는 있다.(락 적용을 위해서는 레코드를 조회해야 하기 때문이다.) 그러나 이 경우는 Phantom Read는 아니다. 두 조회가 '동일한 조건'이 아니기 때문이다.(일반 select / 락 활성화 select)<br>
*동일한 select for update가 여러 번 수행되었을 때 결과가 다르다면 Phantom Read이다.*


## Read View: Read Committed
Read Committed 격리 레벨에서는 `트랜잭션 내의 select문 실행마다 스냅샷을 생성`한다. 따라서 Non-Repeatable, Phantom Read 문제가 발생할 수 있다.


## Repeatable Read(RR), Read Committed(RC) 차이점 예시
전제: age가 20 이상인 유저가 100명 존재
```
시나리오
1. t1 시작
2. t2 시작
3. t2에서 모든 유저의 age를 0으로 초기화 후 커밋
4. t1에서 'update users set status = '성인' where age >= 20' 수행 후 커밋
```
> t1이 RR이나 RC냐에 따라 결과가 달라진다. update 문의 where 절을 통해 '수정 대상 레코드'를 파악할 때 해당 트랜잭션의 Read View를 사용하기 때문이다.

t1이 Repeatable Read인 경우
1. t1은 t2가 커밋한 버전을 조회할 수 없다.
2. 따라서 모든 유저의 Undo Log 데이터를 이용하여 '수정 대상 레코드'를 선정한다.
3. InnoDB 버퍼 풀에서 선정된 레코드를 찾아 수정한다.(100개 레코드 수정)

t1이 Read Committed인 경우
1. t1의 update 쿼리가 t2가 커밋한 버전을 조회할 수 있다.
2. 따라서 모든 유저의 InnoDB 버퍼 풀 데이터를 이용하여 '수정 대상 레코드'를 선정한다.
3. 선정된 레코드가 없어 수정 작업이 진행되지 않는다.

두 경우 모두 이상현상은 발생하지 않았다. 격리 수준 차이에 따라 update 결과가 달라진 것이다.

## MVCC Update 작업 처리
Update 작업 수행 시 먼저 수정 대상 레코드를 탐색하고 대상 레코드에 X락을 활성화하여 순수 select 외에는 접근하지 못하도록 막는다. '수정 대상 레코드 선정'의 경우 select문과 동일한 조회 매커니즘이 적용될 수 있다.(update문의 where 절)<br>
Update 전 Undo Log를 생성한 뒤, 수정 대상 레코드의 최신 버전(즉, InnoDB 버퍼풀에 존재하는 해당 레코드 데이터)을 수정한다.


---
*이미지 출처: [miintto.log](https://miintto.github.io/docs/transaction-isolation-level), [Better Programming - Dwen](https://www.google.com/url?sa=i&url=https%3A%2F%2Fbetterprogramming.pub%2Fin-depth-analysis-of-the-mvcc-principle-of-mysql-c4a5529e4f5&psig=AOvVaw3PiymmxYJCzqBjOVHnLU7i&ust=1761057628783000&source=images&cd=vfe&opi=89978449&ved=0CBgQjhxqFwoTCLCB8YSBs5ADFQAAAAAdAAAAABAK)*