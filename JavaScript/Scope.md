## Scope
자바스크립트에서 **스코프**는 어떤 변수에 접근할 수 있는지를 정의합니다. 변수는 전역이나 코드 블록 또는 함수 내에 선언 가능하며, 코드 블록이나 함수는 중첩될 수 있습니다. 변수는 어디에 선언되었는지에 따라 자신이 유효한 범위를 갖습니다.

자바스크립트에서는 크게 2가지로 스코프를 나눌 수 있습니다.

### 전역 스코프
변수가 전역에서 선언된 경우 전역 스코프를 가지는 전역 변수가 되며, 어디에서든지 참조할 수 있습니다. var 키워드로 선언한 전역 변수는 전역 객체 windonw의 프로퍼티가 됩니다.

### 지역 스코프
지역(함수 또는 코드 블록) 내부에서 선언된 변수는 지역변수라고 하며 지역 스코프를 가집니다. 선언된 지역과 그 하부 지역에서만 참조할 수 있습니다. 이때 함수 내에 변수를 선언한 경우 **함수 스코프**를 블록 내에 선언한 경우 **블록 스코프**를 가집니다. (블록 스코프를 사용하려면 let, const로 변수를 선언해야 합니다.)

```javascript
var x = 0;
{
  var x = 1;
  console.log(x); // 1
}
console.log(x);   // 1

let y = 0;
{
  let y = 1;
  console.log(y); // 1
}
console.log(y);   // 0
```
위 예제에서 변수 x는 var 키워드로 선언되었기 때문에 블록 내에 선언되어도 블록 스코프가 아닌 전역 스코프를 가지는 전역 변수가 됩니다.

```javascript
var x = 'global';
function f() {
	var x = 'local';
	x = 'change';
}
f();
console.log(x); // 'global'
```
f 함수 바깥의 x는 전역변수, f 함수 안의 x는 지역변수입니다. 지역 변수 x는 함수 내에서만 접근 가능하며 함수 안에서 x의 값을 변경하면 지역 변수의 값이 변경됩니다.

### 스코프 체인
내부 함수에서는 외부 함수의 변수에 접근 가능하지만 반대로 외부 함수에서는 내부 함수의 변수에 접근할 수 없습니다. 또한 모든 함수에서는 전역 변수에 접근할 수 있습니다. 아래 예시를 봅시다.
```javascript
var a = 'a';
function outer() {
  console.log('outer', a);
  function inner() {
    var b = 'b';
    console.log('inner', a);
  }
  inner();
}
outer();
console.log(b); // ReferenceError
```
inner 함수는 a 변수를 찾기 위해 먼저 자신의 스코프에서 찾고, 없는 경우 outer 스코프에서 찾고, 없으면 결국 전역 스코프에서 찾습니다. 위 경우에는 전역 스코프에서 a 변수를 찾아 값을 얻을 수 있었습니다. 이렇게 계속 범위를 넓혀가며 변수를 찾는 것을 **스코프 체인**이라고 합니다.

### 렉시컬 스코핑(lexical scope)
자바스크립트에서는 함수가 어디서 호출되었는지가 아닌 함수를 어디서 선언했는지에 따라 함수의 상위 스코프를 결정합니다. 이를 **lexical scope** 또는 **정적 스코프**라고 합니다. 아래 예제를 봅시다.
```javascript
var name = 'joy';
function log() {
  console.log(name);
}

function wrapper() {
  name = 'boy';
  log();
}
wrapper();
```
wrapper 함수에서 log 함수를 호출하기 전에 전역 변수 name의 값을 변경하기 때문에 console에는 'boy'가 출력됩니다.
```javascript
var name = 'joy';
function log() {
  console.log(name);
}

function wrapper() {
  var name = 'boy';
  log();
}
wrapper();
```
이번에는 살짝 바꿔 wrapper에서 지역 변수 name을 선언 후 log 함수를 호출했습니다. 이 경우에는 'boy'가 아닌 'joy'가 호출됩니다. 그 이유는 log 함수가 전역에 선언되었기 때문에 log 함수의 상위 스코프가 전역 스코프이고, 함수 내부의 변수 name은 자기 스코프로부터 가장 가까운 지역의 변수인 전역 변수를 참조하기 때문입니다.


### 전역 변수의 사용
전역 변수의 사용은 변수 이름의 중복, 의도치 않은 재할당과 같이 예측하기 어려운 문제를 야기하기 때문에 사용을 지양해야 합니다.

전역 변수의 사용을 최소화하는 방법 중 하나는 전역 변수 객체를 하나 만들어 사용하는 것입니다.
```javascript
var MYAPP = {};

MYAPP.student = {
  name: 'Lee',
  gender: 'male'
};

console.log(MYAPP.student.name);
```
하지만 위 방식은 누군가에 의해 고의적으로 값이 변경될 수 있습니다. 이를 방지하기 위해 다음과 같이 즉시 실행 함수를 사용해 비공개 변수를 만드는 방법이 많이 사용됩니다.
```javascript
var newScope = (function () {
  var x = 'local';
  return {
    y: function() {
      alert(x);
    }
  };
})();
```


### Reference
- https://poiemaweb.com/js-scope
- https://www.zerocho.com/category/JavaScript/post/5740531574288ebc5f2ba97e