# Blocking / Non-Blocking / Sync / Async
블로킹, 논블로킹, 동기, 비동기에 대해서 공부한 내용을 작성하였다.

## 정의
### Blocking & Non-Blocking

<img width="407" height="180" alt="Image" src="https://github.com/user-attachments/assets/b7634b9a-d888-446d-b0c9-280257c6133c" />

**블로킹:** I/O, 외부 API 호출 등 요청에 대한 결과를 응답받을 때까지 가만히 기다리는 상태(sleep)를 뜻한다.<br>
**논블로킹:** 블로킹과 달리 요청에 대한 결과를 응답받을 때까지 '가만히 기다리지' 않는다.<br>

### Synchronous & Asynchronous

<img width="326" height="301" alt="Image" src="https://github.com/user-attachments/assets/1835d8a2-1bc9-4a07-aad8-d09b82c47d66" />

**동기:** 작업을 요청한 주체가 **작업 완료**를 기다린다.<br>
**비동기:** 작업을 요청한 주체가 작업 완료를 기다리지 않고 **즉시 반환**된다. 완료 여부는 콜백/이벤트로 알 수 있다.<br>

## 조합

<img width="1292" height="772" alt="Image" src="https://github.com/user-attachments/assets/ced4c466-a6fc-4670-af35-768af5d477bb" />

`동기 + 블로킹`

전통적인 방식이다. 스레드 하나가 작업의 요청과 완료를 책임지며, 블로킹 발생 시 대기한다.

`동기 + 논블로킹`

블로킹이 발생하지 않는 동기 방식이다. 대기 시간이 발생하는 작업을 만나는 경우 다른 작업을 수행할 수 있으나, 자신이 요청한 모든 작업에 대한 완료 여부를 계속해서 확인(*Polling*)해야하므로 효율적이지 않을 수 있다.

`비동기 + 블로킹`

작업을 요청한 스레드는 작업 완료를 기다리지 않고 즉시 반환된다. 이후 별도의 스레드가 이 작업을 처리하며, 여기에서의 작업에서는 블로킹이 발생할 수 있다.<br>
비동기 처리 작업은 **별도의 스레드 풀**을 활용하여 처리하기에, 이 스레드 풀 크기에 비례하여 처리 능력이 결정된다.
> **Spring**의 기본적인 비동기 처리(@Async)가 이 방식으로 수행된다. 

`비동기 + 논블로킹`

작업을 요청한 스레드가 작업 완료를 기다리지 않으며, 작업 처리 도중 블로킹이 발생하지 않는 처리 방식이다. 주어진 자원으로 가장 높은 처리량을 달성할 수 있는 이상적인 방식이나, 디버깅과 테스트가 어렵고 복잡도가 증가한다는 점이 있다.


---
*이미지 출처: [위키독스](https://wikidocs.net/168327), [Lifealong - Tistory](https://0soo.tistory.com/216)*