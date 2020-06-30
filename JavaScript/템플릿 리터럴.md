## 템플릿 리터럴 (Template literal)

ES6에서 새로 도입된 문자열 표기법

- `'` 또는 `"` 대신 `\`를 사용해 문자열을 나타낸다.
- 일반 문자열과 달리 여러 줄에 걸쳐 작성할 수 있으며, 템플릿 리터럴 내의 모든 공백이 그대로 적용된다.
- 문자열 인터폴레이션 기능을 제공해 `+` 연산자 없이 새로운 문자열을 삽입할 수 있다.

```jsx
const str = `'작은따옴표와 "큰 따옴표를 혼용할 수 있다.`;

// 공백, 줄바꿈
const template = `<ul class="nav-items">
  <li><a href="#home">Home</a></li>
</ul>`;

// String Interpolation
const first = 'Ung-mo';
const last = 'Lee';
console.log(`My name is ${first} ${last}.`);
console.log(`1 + 1 = ${1 + 1}`); // "1 + 1 = 2"
```
