## Strict mode
ES5부터 도입된 strict mode는 자바스크립트 코드에 더욱 엄격한 오류 검사를 적용해준다.

```javascript
function f() {
  x = 10;
}

console.log(x);
```
위 예제를 보면, 함수 내에서 선언하지 않은 변수 x에 값을 할당한다. 자바스크립트 엔진 x에 값을 할당하기 위해 스코프 체인을 통해 x가 어디에서 선언되었는지를 검색한다. x의 선언을 발견하지 못한 자바스크립트 엔진은 에러를 발생시키는 대신 암묵적으로 전역 객체에 프로퍼티 x를 동적 생성하고 x는 전역변수가 된다. 이렇게 자바스크립트 엔진에 의해 생성된 암묵적 전역 변수는 오류를 발생시키는 원인이 될 가능성이 크다.

strict mode를 사용하면 방금 예시처럼 기존에는 무시되었지만 오류를 발생시킬 가능성이 높거나 자바스크립트 엔진의 최적화 작업에 문제를 일으킬 수 있는 코드에 대해 명시적인 에러를 발생시킨다.

ESLint와 같은 린트 도구를 사용해도 strict mode와 유사한 효과를 얻을 수 있다.

strict mode는 스크립트의 맨 처음에 `"use strict"` 지시어를 사용해 선언할 수 있다. (`"use strict"` 지시어가 최 상단에 위치하지 않으면 적용되지 않는다.) `"use strict"` 지시어를 함수 앞에 사용하면 해당 함수만 strict mode로 실행된다.

클래스와 모듈같은 모던 자바스크립트 문법을 사용하면 `"use strict"`를 생략해도 strict mode가 자동 적용된다.

다음은 기존 자바스크립트의 문법과 다른 strict mode의 대표적인 문법들이다.
- 선언되지 않은 변수나 객체를 사용할 수 없다. ReferenceError가 발생한다.
- `delete` 키워드를 사용해 변수, 함수, 매개변수 등을 삭제할 수 없다.
- 매개변수의 이름이 중복되면 안된다.
- `with` 문을 사용할 수 없다.
- 일반 함수에서 this는 undefined에 바인딩된다.

브라우저 콘솔에서 strict mode를 적용하려면 `"use strict";`를 입력한 후, `shift + Enter`를 눌러 줄바꿈한 뒤 스크립트를 작성한다. 또는 스크립트 전체를 즉시 실행 함수로 감싸준다.

strict 모드는 IE10 이상부터 지원한다.

### Reference
http://tcpschool.com/javascript/js_exception_strict
https://poiemaweb.com/js-strict-mode
https://ko.javascript.info/strict-mode