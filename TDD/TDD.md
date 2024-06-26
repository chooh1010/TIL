## TDD

### Test Driven Development(테스트 주도 개발)

TDD = TFD(Test First Development) + 리팩토링

### TDD를 하는 이유

* 디버깅 시간을 줄여준다.
* 동작하는 문서 역할을 한다.
* 변화에 대한 두려움을 줄여준다.

### TDD 단계

* 실패하는 테스트를 구현한다.
* 테스트가 성공하도록 프로덕션 코드를 구현한다.
* 프로덕션 코드와 테스트 코드를 리팩토링한다.

### TDD 원칙

* 실패하는 단위 테스트를 작성할 때까지 프로덕션 코드를 작성하지 않는다.
* 컴파일은 실패하지 않으면서 실행이 실패하는 정도로만 단위 테스트를 작성한다.
* 현재 실패하는 테스트를 통과할 정도로만 실제 코드를 작성한다.
