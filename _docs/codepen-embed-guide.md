# codepen.io 사용하기

## 문서에 임베드하기

아래의 HTML 코드를 삽입하여 Codepen 에서 작성한 코드를 간편하게 임베드할 수 있다. 마크다운 본문에서 아래의 링크를 추가하고 `data-href` 를 작성하던 링크의 식별값으로 치환하면 작성이 완료된다.

단, 당연하게도 codepen 에서 제공하는 emebed를 위한 스크립트를 추가해야한다. 아래의 코드를 head 영역에 추가한다.

```html
<script async="async" src="//codepen.io/assets/embed/ei.js"></script>
```

또한, 자신이 작성하던 Codpen 화면에서 아래와 같이 url 링크를 가져와 고유 식별값을 가져오면된다. 아래의 링크에서 볼드처리된 문자열이 해당 pen 의 고유 식별 값이다.

https://codepen.io/code-machina/pen/**oNvwOGV**

마크다운 문서 내에서 해당 식별 코드를 기입하면 끝난다.

```html
<pre class="codepen" data-height="470" data-type="html,result" data-href="oNvwOGV" data-user="code-machina" data-safe="true"></pre>
```
