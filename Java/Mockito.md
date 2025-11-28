# Mockito

mockito란? 자바에서 사용되는 Mocking 라이브러리로, 단위 테스트에서 외부 의존성을 가짜 객체로 대체해주는 도구이다.<br>
단위 테스트의 핵심 원칙 FIRST 중 하나인 I(Isolated)을 지키기 위해 테스트 대상 클래스만 고립시켜 테스트할 수 있게 해준다.

### 핵심 기능 5가지
1. Mock 생성(@Mock)
2. Mock 주입(@InjectMocks)
3. Stubbing(기대하는 행동 정의, given/when-thenReturn)
4. Verification(호출 여부/횟수 검증)
5. ArgumentCaptor(저장/호출된 인자를 캡처)

### 기능 1: Mock 생성
ex)
```java
@Mock
UsersRepository usersRepository;
```
실제 UsersRepository 구현체 대신 가짜 객체를 만든다. 이 객체는 메서드를 호출해도 실제 작업을 수행하지 않는다. 대신 우리가 stubbing을 통해 '특정 상황'에서 '특정 값'을 반환하도록 설정할 수 있다.<br>

**특징**
- 아무 설정 없이 호출하면 null 혹은 기본값 반환
- 사이드 이펙트 X
- 매우 빠른 속도

### 기능 2: Mock 자동 주입

**동작 정의: 테스트 대상 클래스의 생성자에 Mock 객체를 의존성으로 주입**

ex)
```java
@InjectMocks
AuthService authService;
```
AuthService의 필드 중 @Mock 으로 선언된 타입들은 자동으로 주입된다. 예제에서 AuthService는 다음의 의존성을 생성자를 통해 주입받는다.
```
UsersRepository usersRepository,
BCryptPasswordEncoder bCryptPasswordEncoder,
RoleRepository roleRepository,
...
```
테스트에서는 스프링 컨텍스트가 없기 때문에 DI 과정이 발생하지 않는다. 대신 Mockito가 @InjectMocks AuthService 인스턴스를 생성하고, 그 내부에 @Mock 설정한 객체를 주입한다.<br>
즉, 테스트 대상 Service 클래스의 생성자에 Mock 객체를 주입한다.

### 기능 3: Stubbing (given/when - thenReturn)

**동작 정의: Mock 객체의 메서드 반환값(동작) 정의**

Mock은 기본적으로 모든 메서드에서 null/기본값을 반환하므로, 테스트에 맞게 상황에 따른 값을 직접 정의해야 한다.
ex)
```java
given(roleRepository.findByName(RoleType.ROLE_USER))
        .willReturn(Optional.of(role));
```
- roleRepository의 findByName() 메서드에 RoleType.ROLE_USER가 전달되면 Optional.of(role)을 반환하는 규칙 설정

예외 throw도 가능하다.

```java
given(usersRepository.findByEmail("aaa@bbb.com"))
        .willThrow(new ApplicationException(USER_NOT_FOUND));
```

Stubbing의 기본 문장 구조는 다음과 같다.
```java
givin(when
        .willRetrn
        or
        .willThrow)
```

### 기능 4: Verification

**동작 정의: 특정 메서드 호출 여부 검증/횟수 카운트**

단위 테스트에서 메서드의 호출 여부를 검증해야 하는 경우 사용한다. Mock 객체는 **메서드의 호출 기록을 내부적으로 저장**하는데, verify()는 이 기록을 확인하는 작업을 수행한다.

<br>
ex)
- 정상적인 회원가입 시 .save() 메서드가 호출되는지에 대한 여부
- ROLE 정보가 존재하지 않는 경우 .save() 메서드가 호출되지 않는지 검증

<br>

.save() 메서드의 파라미터 타입에 상관없이 '호출 되었는지' 여부 검증

```java
verify(usersRepository).save(any());
// 호출되지 않으면 테스트 실패
```

해당 메서드가 호출되지 않았는지에 대한 여부 확인

```java
verify(usersRepository, never()).save(any());
// 호출되었다면 테스트 실패
```

해당 메서드가 몇 번 호출되었는지 확인
```java
verify(redisService, times(2)).deleteSession(anyLong());
```

### 기능 5: ArgumentCaptor

```java
ArgumentCaptor<Users> usersCaptor = ArgumentCaptor.forClass(Users.class);
authService.commonJoin(joinDto); // when

// authService 내부에서 의존성 객체의 메서드가 호출되었는지 검증
verify(roleRepository).findByName(RoleType.ROLE_USER);
verify(bCryptPasswordEncoder).encode("password");
// 캡처한 Users Entity가 save()의 파라미터로 전달되었는지 확인
verify(usersRepository).save(usersCaptor.capture());

// 전달되었던 Users Entity 캡처본을 Load하여 해당 Entity에 대한 검증과정 추가적으로 수행
Users savedUsers = usersCaptor.getValue();
```

- Mocking된 usersRepository.save() 메서드가 호출되었을 때 전달된 Users 객체를 '캡처'한다.

**필요성**
- repository.save()는 void or Entity 반환
- 서비스 메서드 내부에서 Entity 객체를 