# AOP

AOP란? 여러 곳에서 적용되는 '공통적인 관심사(부가기능)'를 한 곳으로 모듈화하는 기술이다.

## Proxy
특정 객체의 `행동을 대신 수행`하는 '대리 객체'이다. 따라서 특정 객체의 메서드 호출 전/후처리를 할 수 있다.<br>
ex) Logging, Transactional 등등

```java
public interface Service {
    void work();
}
public class ServiceImpl implements Service {
    @Override
    public void work() {
        System.out.println("비즈니스 로직 수행");
    }
}
----
public class ServiceProxy implements Service {
    private Service service;
    
    // DI 등을 통해 의존성 주입
    public ServiceProxy(Service service) {
        this.service = service;
    }

    @Override
    public void work() {
        System.out.println("프록시: 메소드 실행 전처리");
        service.work();
        System.out.println("프록시: 메소드 실행 후처리");
    }
}
```

JPA Entity의 연관관계에 속한 Entity 필드도 이러한 프록시 객체로 초기화된다.

## Spring AOP
Spring AOP는 기본적으로 프록시 기반의 AOP 기능을 제공한다. 여러 메서드에 반복적으로 등장하는 로직들을 하나의 관점(Aspect)으로 묶고, 런타임에 이를 이용하여 프록시 객체를 생성한다.*(Runtime Weaving)* <br><br>

Spring AOP는 **AspectJ**의 구조와 문법을 사용한다.

```java
@Component
@Aspect
public class LogAspect {
    // logging level
    // error < warn < info < debug < trace
    private final Logger logger = LoggerFactory.getLogger(this.getClass()); // logging 주체

    @Pointcut(value = "execution(int com.mycom.myapp.aspect..*.*(String, int, ..))") // pointcut 설정. value에 매칭과 관련된 값 입력(aspectj 표현식을 따른다.
    private void logPointCut() {

    }

    @Before("logPointCut()")
    public void beforeLog(JoinPoint joinPoint) {
        logger.info("[LogAspect: before] " + joinPoint.getSignature().getName());
    }

    @After("logPointCut()")
    public void afterLog(JoinPoint joinPoint) {
        logger.info("[LogAspect: after] " + joinPoint.getSignature().getName());
    }
}
```

### AOP 용어

1. Aspect: 공통적인 관심사를 모듈화한 클래스(@Aspect)
2. Join Point: Spring AOP가 적용될 수 있는 모든 지점을 의미하는 개념적인 용어
3. Pointcut: Join Point 중에서 실제로 AOP를 적용할 지점을 지정(클래스명, 메서드 패턴 등)
4. Advise: 언제 무엇을 실행할지를 정의한 메서드(before, around, after)
5. Weaving
    - 지정된 지점에 공통 로직을 삽입하는 과정
    - Spring AOP: Pointcut을 기반으로 런타임에 프록시 객체를 생성하는 과정

## AspectJ
자바 언어에 AOP기능을 확장한 프레임워크이자 언어
- 자바 컴파일러 수준에서 확장한 언어
- 바이트코드 레벨에서 코드 삽입이 수행된다.(Weaving)

거의 모든 경우에서 AOP를 적용할 수 있다.(객체 생성 시점, 필드 접근 시점, 예외 처리 블록 실행, static 초기화 등)
<br>
Spring AOP는 기본적으로 AspectJ의 구조와 문법을 따르나, '프록시 기반'의 AOP 기능을 제공한다.