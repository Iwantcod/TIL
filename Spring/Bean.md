# Spring Bean
Bean이란? 스프링 IoC 컨테이너에서 생명주기를 관리하는 인스턴스를 말합니다. 즉, 스프링 프레임워크에서 관리하는 인스턴스입니다.

## 생성 과정
1. 애플리케이션 시작 시 컴포넌트 스캔 혹은 XML 정보를 토대로 `@Component` 기반 어노테이션의 클래스를 탐색합니다.
2. 어노테이션이 붙은 클래스를 통해 바로 Bean을 생성하는게 아니라, `BeanDefinition`을 생성합니다.
    - *BeanDefinition: Bean 인스턴스를 생성하기 위한 정보가 담긴 메타데이터(설명서)*
3. 생성된 `BeanDefinition`은 `BeanDefinitionRegistry`에 저장됩니다.
4. 실제 Bean 생성 단계에서 BeanFactory의 BeanDefinition을 토대로 생성합니다. *(IoC 컨테이너 종류에 따라 생성 전략이 달라집니다.)*
5. 생성된 인스턴스를 Spring IoC가 Bean으로 관리합니다.

- `@Configuration` 클래스를 탐색할 때, 그 내부의 `@Bean` 메서드를 추가로 탐색하여 `BeanDefinition`을 생성합니다.

### BeanDefinition 내용
`@Component 클래스`, `@Bean 팩토리 메서드`에서 생성되는 BeanDefinition의 형식은 서로 다릅니다.
- 클래스: '특정 경로'에 위치한 클래스의 생성자를 통해 Bean을 생성
- 팩토리 메서드: '특정 Configuration Bean' 내에 존재하는 특정 메서드를 통해 Bean을 생성

---

**Q. `@Component`로 클래스를 Bean으로 등록하면 되는데, `@Bean 팩토리 메서드`를 사용하는 경우가 있나요?**

A. 대표적으로 다음의 경우 사용할 수 있습니다.
1. 외부 라이브러리 클래스처럼 `@Component`를 붙일 수 없는 경우
2. 동일 타입의 Bean을 서로 다른 설정값으로 여러 개 등록해야 하는 경우
3. 인터페이스 타입으로 구현체를 선택해서 등록해야 하는 경우
