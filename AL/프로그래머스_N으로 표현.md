### N으로 표현
**문제 설명**
아래와 같이 5와 사칙연산만으로 12를 표현할 수 있습니다.
```
12 = 5 + 5 + (5 / 5) + (5 / 5)
12 = 55 / 5 + 5 / 5
12 = (55 + 5) / 5
```

5를 사용한 횟수는 각각 6,5,4 입니다. 그리고 이중 가장 작은 경우는 4입니다.
이처럼 숫자 N과 number가 주어질 때, N과 사칙연산만 사용해서 표현 할 수 있는 방법 중 N 사용횟수의 최솟값을 return 하도록 solution 함수를 작성하세요.

**제한사항**
N은 1 이상 9 이하입니다.
number는 1 이상 32,000 이하입니다.
수식에는 괄호와 사칙연산만 가능하며 나누기 연산에서 나머지는 무시합니다.
최솟값이 8보다 크면 -1을 return 합니다.

**입출력 예**
|N	|number	|return|
|--|--|--|
|5	|12	|4|
|2	|11|	3|

**입출력 예 설명**
예제 #1
문제에 나온 예와 같습니다.

예제 #2
`11 = 22 / 2`와 같이 2를 3번만 사용하여 표현할 수 있습니다.

**나의 풀이**
```javascript
function iterateNumber(N, n) {
  let answer = '';
  for(let i = 1; i <= n; i++) {
      answer += N.toString();
  }
  return parseInt(answer);
}


function solution(N, number) {
  let cases = [...Array(8)].map(() => new Set());
  cases.forEach((c, i) => { c.add(iterateNumber(N, i + 1)) });
  for(let i = 1; i < cases.length; i++) {
    for(let j = 0; j < i; j++) {
      for(let num1 of cases[j]) {
        for(let num2 of cases[i - j - 1]){
          cases[i].add(num1 + num2);
          cases[i].add(num1 - num2);
          cases[i].add(num1 * num2);
          cases[i].add(parseInt(num1 / num2));
        }
      }
    }
    if(cases[i].has(number))
        return i;
  }
  return -1;
}
```
- 8개의 집합을 가진 배열을 만든다.
- 먼저 각 집합에 5, 55, 555, ... , 55555555를 먼저 넣어준 뒤 각각에 N을 1번 써서 만든 수, N을 2번 써서 만든 수, ... 8번 써서 만든 수를 구해서 넣어준다.
  - N을 2번 써서 만든 수 있는 수: `N을 1번 써서 만든 수` 사칙 연산(+, -, *, /) `N을 1번 써서 만든 수`
  - N을 3번 써서 만든 수 있는 수: `N을 1번 써서 만든 수` 사칙 연산(+, -, *, /) `N을 2번 써서 만든 수`, `N을 2번 써서 만든 수` 사칙 연산(+, -, *, /) `N을 1번 써서 만든 수`
  - ...
- 집합에 number가 있으면 해당 집합의 index + 1을 반환, 모든 집합에 number가 없으면 -1 반환

**다른 사람의 풀이**
```javascript
function solution(N, number) {
  const cache = new Array(9).fill(0).map(el => new Set());
  for(let i=1; i<9; i++){
    cache[i].add('1'.repeat(i) * N);
    for (let j=1; j < i; j++) {
      for(const arg1 of cache[j]){
        for(const arg2 of cache[i-j]){
          cache[i].add(arg1+arg2);
          cache[i].add(arg1-arg2);
          cache[i].add(arg1*arg2);
          cache[i].add(arg1/arg2>>0);
        }
      }
    }
    if(cache[i].has(number)) return i; 
  }
  return -1;
}
```
- N이 i번 반복하는 수를 만들 때 `'1'.repeat(i) * N`