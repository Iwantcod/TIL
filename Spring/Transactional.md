# @Transactional Flow

여러 데이터 접근 작업을 하나의 트랜잭션에서 실행하도록 하는 트랜잭션 관리 기능입니다. RuntimeException, Error가 발생하는 경우 트랜잭션 내의 작업 내용을 롤백합니다. AOP 기반으로 동작합니다.<br>

**Rollback**: 롤백을 지원하는 데이터만 롤백할 수 있습니다(RDB 등). 롤백이 불가능한 작업은 되돌릴 수 없습니다(Redis, 이메일 발송 등).

## 내부적인 동작 원리

1. 스프링 애플리케이션 실행 시 Bean 생성
2. Bean 후처리 과정에서 `@Transactional` 어노테이션이 사용된 메서드 탐지 시, 해당 Bean의 `프록시 Bean`을 생성 후 컨테이너에 등록
- 프록시는 인터페이스 구현 혹은 대상 Bean 클래스를 상속하는 방식으로 생성됩니다.
2. 런타임에서 외부에서 해당 Bean의 메서드로 접근하는 경우 프록시 Bean을 경유
- Self-Invocation 혹은 접근제어자가 private인 경우는 적용 X
3. 프록시에서 실제 메서드 호출 전/후처리를 수행(commit/rollback 작업 등)

<img width="480" height="489" alt="Image" src="https://github.com/user-attachments/assets/e3a3b34a-482d-4fae-a060-d8a308c18561" />