# Entity 생성(Reflection)
JPA를 통해 DB를 조회하여 얻은 결과를 Entity로 변환하고 영속성 컨텍스트에 등록하는 과정을 공부한 내용이다.


## 1. 조회 메서드 호출
1차 캐시(영속성 컨텍스트)에 조회 대상이 존재하는지 확인하고 존재하면 쿼리문을 실행하지 않고 값을 반환한다.<br>
존재하지 않는다면 JPA가 JDBC API를 통해 SQL을 실행한다.

```java
Member m = em.find(Member.class, 1L);
```

메서드 호출 시 반환 객체 타입에 대한 클래스 정보가 파라미터로 전달된다.(예제의 첫번째 파라미터)

## 2. ResultSet을 담을 Entity 클래스 탐색 후 인스턴스 생성
Hibernate가 조회 메서드 호출 시 전달받은 클래스 타입을 기준으로, 클래스 타입에 맞는 EntityPersister를 사용한다.<br>
EntityPersister 내부에는 EntityMetamodel이 들어있다. 이 EntityMetamodel의 정보로 기본생성자를 호출하여 인스턴스를 생성한다.

## 3. Reflection API를 통해 Entity 인스턴스 생성
EntityPersister가 Reflection API를 이용해서 ResultSet과 Entity Class를 매핑한다.<br>
먼저 Class<?> 객체의 **기본 생성자**를 호출한다. 즉, new 키워드가 아닌 리플렉션을 통해 런타임에 동적으로 인스턴스를 생성한다.

```java
Constructor<?> ctor = entityClass.getDeclaredConstructor();
Object entity = ctor.newInstance();
```

## 4. ResultSet -> Entity 필드 주입
조회 결과의 ColumnMapping 을 순회하며 각 컬럼 값을 그에 대응하는 Entity 필드로 값을 주입한다.

```java
for (ColumnMapping cm : meta.getColumns()) {
    Field f = cm.getField();
    f.setAccessible(true); // private 필드로의 직접 접근 허용
    Object value = rs.getObject(cm.getColumnName());
    f.set(entity, value);
}
```

이때 필드의 모든 값은 리플렉션을 통해 `private 필드로 직접 주입`된다.<br>
연관관계에 속한 필드(@ManyToOne 등)에는 프록시 객체가 주입된다.

## 5. 1차 캐시(영속성 컨텍스트) 등록
완성된 엔티티는 1차 캐시(영속성 컨텍스트)에 저장된다.
```java
persistenceContext.put(EntityKey(Member.class, id), entity);
```

## 6. 완성된 엔티티 객체 반환
완성된 엔티티 객체(인스턴스)가 애플리케이션 코드로 반환된다.


## 관련 클래스
- EntityMetamodel: **DB 매핑을 위한** 특정 Entity의 클래스 설계도
  - ex: 기본키 및 외래키, 각 컬럼의 타입과 제약조건 등에 대한 정보 보유
  - 애플리케이션 시작 시 전역적으로 Entity를 scan하여 'EntityMetamodelMap'에 저장한다.
- EntityPersister: Entity 설계도를 이용해 SQL 실행 결과와 매핑을 수행(Entity마다 하나씩 존재)
  - 내부에 자기 자신의 Entity의 EntityMetamodel을 포함한다.
- EntityMetamodelMap(혹은 EntityPersisterMap): 모든 Entity Persister을 모아두는 전역 Map