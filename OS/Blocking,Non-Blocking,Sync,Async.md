# Blocking / Non-Blocking / Sync / Async
블로킹, 논블로킹, 동기, 비동기에 대해서 공부한 내용을 작성하였다.

## 정의
### Blocking & Non-Blocking

<img width="407" height="180" alt="Image" src="https://github.com/user-attachments/assets/b7634b9a-d888-446d-b0c9-280257c6133c" />


**블로킹:** 작업 요청 시, 처리 결과를 응답받을 때까지 멈춘다.
- 작업 결과 확인 시, 확인이 될때까지 제어권을 넘겨받지 못한다.

**논블로킹:** 블로킹과 달리 요청에 대한 결과를 응답받을 때까지 '가만히 기다리지' 않는다.
- 작업 결과 확인 시, 확인 여부와 상관없이 제어권을 넘겨받는다.

### Synchronous & Asynchronous

<img width="326" height="301" alt="Image" src="https://github.com/user-attachments/assets/1835d8a2-1bc9-4a07-aad8-d09b82c47d66" />

<img width="435" height="181" alt="Image" src="https://github.com/user-attachments/assets/11e9f265-5558-4593-b303-129b4ae75609" />

**동기:** 호출자가 작업 완료를 확인해야 다음 작업을 진행할 수 있다.<br>
**비동기:** 호출자가 작업 완료를 확인하지 않아도 다음 작업을 진행할 수 있다.<br>


## 가능한 조합

<img width="1292" height="772" alt="Image" src="https://github.com/user-attachments/assets/ced4c466-a6fc-4670-af35-768af5d477bb" />

### 동기 + 블로킹

```mermaid
sequenceDiagram
    participant Caller as 호출자
    participant Task as 작업

    Caller->>Task: 작업 요청
    Note over Caller: 대기 / 멈춤
    Task-->>Caller: 결과 반환
    Note over Caller: 다음 작업 진행
```

- 작업 하나를 완료한 뒤, 그 다음 작업을 수행한다.
- 작업 요청 시, 처리 결과를 응답받을 때까지 멈춘다.

### 동기 + 논블로킹

```mermaid
sequenceDiagram
    participant Caller as 호출자
    participant Task as 작업

    Caller->>Task: 작업 요청
    Task-->>Caller: 즉시 반환(미완료)

    loop polling
        Caller->>Task: 완료 여부 확인
        Task-->>Caller: 미완료 또는 완료
    end

    Note over Caller: 완료되면 호출자가 직접 결과 사용
```

- 작업 하나를 완료한 뒤, 그 다음 작업을 수행한다.
- 작업 요청 시, 처리 결과를 떠나 즉시 응답받는다.
    - **polling**을 통해 주기적으로 결과를 확인한다.
    - 따라서 polling 로직은 수행할 수 있다.

### *비동기 + 블로킹*

```mermaid
sequenceDiagram
    participant Caller as 호출자
    participant Task as 작업

    Caller->>Task: 비동기 작업 요청
    Note over Caller: 대기 / 멈춤

    Note over Task: 실제 작업은 비동기적으로 진행
    Task-->>Caller: CallBack: 완료 이벤트 / 결과 통지
    Note over Caller: 다음 작업 진행
```

- 작업이 완료되지 않아도 다음 작업을 바로 진행할 수 있다.
- 작업 요청 시, 처리 결과를 응답받을 때까지 멈춘다.

⇒ 두 개념은 서로 상충되며, 마치 ‘동기 + 블로킹’ 방식처럼 동작하게 된다. 따라서 일반적으로는 사용되지 않는다. 개발자의 실수에 의해 발생하는 안티패턴으로 취급되기도 한다.

### 비동기 + 논블로킹

```mermaid
sequenceDiagram
    participant Caller as 호출자
    participant Task as 작업
    participant Handler as 콜백/이벤트 처리기

    Caller->>Task: 비동기 작업 시작
    Task-->>Caller: 즉시 반환
    Note over Caller: 계속 다른 작업 수행

    Task-->>Handler: 완료 통지
    Note over Handler: 콜백 / 이벤트 / completion 실행
```

- 작업이 완료되지 않아도 다음 작업을 바로 진행할 수 있다.
- 작업 요청 시, 처리 결과를 떠나 즉시 응답받는다.
    - 각 작업이 완료될 때 콜백/이벤트 드리븐 등으로 제 3자가 이후 로직을 처리한다.
    - 제 3자는 원래 로직을 수행하던 스레드가 될 수도, 아예 별도의 스레드가 될 수도 있다.
    - 이 콜백을 별도의 스레드가 수행하는 경우, 호출(origin) 스레드의 thread-local을 사용할 수 없다.


---
*이미지 출처: [위키독스](https://wikidocs.net/168327), [Lifealong - Tistory](https://0soo.tistory.com/216)*