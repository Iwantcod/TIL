# DispatcherServlet

스프링 MVC에서는 HTTP 요청을 처리하기 위해 여러 개의 Servlet 클래스를 등록하는 것 대신, 하나의 'DispatcherServlet'을 사용한다.<br>

## SingleTon
DispatcherServlet은 애플리케이션마다 하나의 인스턴스로 관리되고, 이 **하나의 인스턴스가 모든 요청**을 받는다.<br>
**Thread-Safe**한 구조로, 여러 스레드에서 동시에 사용된다.<br>
Tomcat과 Spring 애플리케이션 간의 징검다리 역할을 수행한다.

## 동작

<img width="582" height="536" alt="Image" src="https://github.com/user-attachments/assets/473ae818-715f-452d-a996-b988bef75027" />

[이미지 출처: Spring Docs](https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-servlet/context-hierarchy.html)

0. Tomcat에서 하나의 HTTP 요청을 하나의 스레드가 DispatcherServlet으로 전달한다.
1. 모든 HTTP 요청은 하나의 DispatcherServlet으로 전달된다.(DispatcherServlet.service() 호출)
2. DispatcherServlet: 해당 요청을 처리할 `컨트롤러를 탐색`한다.(Handler Mapping)
3. DispatcherServlet: 해당 컨트롤러의 메서드를 호출해서 `요청을 처리`한다.(Handler Adapter)
4. DispatcherServlet: 처리 결과로 생성된 View 이름 정보를 통해 `실제 View를 생성`한다.(View Resolver)
5. DispatcherServlet이 View를 클라이언트로 `응답`한다.

```
[HTTP 요청]
   ↓
[Tomcat Connector]
   ↓
[워커 스레드 할당]
   ↓
DispatcherServlet.service()
   ↓
  doDispatch()
     ↓
   HandlerMapping → Controller → Service → Repository
     ↓
   ViewResolver → View.render()
   ↓
[응답 생성 완료]
   ↓
워커 스레드 반환 (스레드 풀로 복귀)
```

### Handler Mapping
`@RequestMapping` 기반의 어노테이션이 붙은 메서드를 탐색하여 요청을 처리할 컨트롤러(핸들러)를 결정한다.(*@GetMapping* 등)

### Handler Adapter
컨트롤러의 메서드를 호출하여 요청을 처리한 뒤 View의 이름을 반환받고 View Resolver에 전달한다.<br>
REST 응답(RestController or @ResponseBody 메서드)의 경우 메서드의 반환 값을 받아 HTTP Body로 직렬화한다. 이때 ViewResolver 단계는 건너뛴다.


### View Resolver
View의 이름 정보를 이용해서 실제 View 객체를 생성하고, DispatcherServlet에 전달하여 클라이언트에게 응답한다.

## 장점
기존에는 API URL마다 Servlet Class를 별도로 생성해야 했다. 따라서 View를 호출하기 위한 Forwarding 코드가 중복되었으며, 컨트롤러의 공통적인 로직 처리가 어려웠다.<br>
DispatcherServlet에서는 모든 요청을 한 곳에서 받은 다음 처리하므로 공통적인 요소를 묶어서 처리할 수 있게 되었다.*(Front Controller Pattern)* '수문장' 역할을 하는 서블릿 하나만 사용하는 것이다.

<img width="1280" height="1826" alt="Image" src="https://github.com/user-attachments/assets/13655eba-9866-4b3e-b775-90c21631a4ac" />

[이미지 출처: 주발2 - tistory](https://zzang9ha.tistory.com/449#google_vignette)


## Tomcat 워커 스레드의 동작
Tomcat에서 하나의 HTTP 요청을 하나의 스레드가 DispatcherServlet으로 전달하고, **Spring 애플리케이션**의 비즈니스 로직까지 처리한다.