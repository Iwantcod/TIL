# Junit
Junit 대표적인 요소에 대한 개념 정리

### Junit 관련 요소

**@ExtendWith(MockitoExtension.class)**
- Junit 5의 확장 매커니즘을 사용하겠다는 선언
- MockitoExtension을 등록하여 @Mock, @InjectMocks 같은 Mockito 어노테이션이 동작하도록 해준다.
- 테스트를 위한 mock 객체를 만들고 주입하는 역할을 담당한다.

**assertEquals(a, b)**
- Junit의 단정문 중 하나
- equals() 메서드를 사용하여 두 파라미터를 비교한다.
- 같지 않으면 테스트 실패를 발생시킨다.

**assertSame(a, b)**
- 두 객체를 동일비교한다.
  - 즉, 참조가 같은지 비교한다.(primitive type의 경우 단순 값 비교)

**assertThrows(예외명.class, Lambda Method)**
- 특정 코드를 실행했을 때 예상한 타입의 에러가 발생하는지 검증하는 메서드
- 두 가지 역할을 수행
  - 지정한 예외 타입이 실제로 던져지는지 확인(아니라면 실패)
  - 던져진 예외 객체를 반환하여 해당 예외의 필드나 메시지를 추가로 검증 가능

ex)
```java
ApplicationException ex = assertThrows(
        ApplicationException.class,
        () -> authService.commonJoin(joinDto)
);
```