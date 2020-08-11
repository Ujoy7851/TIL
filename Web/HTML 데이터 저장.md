## HTML에 데이터를 저장하는 방법

### 데이터 속성

HTML5부터 도입된 데이터 속성을 사용하면 HTML 요소에 데이터를 저장할 수 있다. 사용 방식은 다음과 같이 `data-`로 시작하는 속성을 엘리먼트에 추가하면 된다.

```html
<article
  id="electriccars"
  data-columns="3"
  data-index-number="12314"
  data-parent="cars">
...
</article>
```

JavaScript에서 이 속성 값들을 읽기 위해서는 dataset 속성을 이용하면 된다.

```jsx
var article = document.getElementById('electriccars');

// 속성 값 가져오기
var column = article.dataset.column// "3"
var indexNumber = article.dataset.indexNumber // "12314"
var parent = article.dataset.parent // "cars"

// 속성 값 변경
article.dataset.column = 5
```

CSS에서도 데이터 속성에 접근할 수 있다.

```css
article::before {
  content: attr(data-parent);
}

article[data-columns='3'] {
  width: 400px;
}
article[data-columns='4'] {
  width: 600px;
}
```

데이터 속성에 저장된 값은 검색 크롤러가 찾지 못하며 IE 10 이하에서는 지원하지 않아 `getAttribute()`를 통해 데이터 속성에 접근해야 한다.

### Reference

https://developer.mozilla.org/ko/docs/Learn/HTML/Howto/%EB%8D%B0%EC%9D%B4%ED%84%B0_%EC%86%8D%EC%84%B1_%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0