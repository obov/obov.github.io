---
layout: single
title: "Pseudo React? 실제로 되려나?"
comments: true
description: "리엑트 없이 간단한 협업에 사용해 볼 수 있을까?"
categories: javascript
tag: ["vanilla"]
toc: true
author_profile: false
published: true
sidebar:
  nav: "docs"
---

<br/>
&nbsp;협업할 때 리액트와 같은 라이브러리를 활용할 수 없다면 분명 효율성이 많이 떨어질 것 같다는 생각을 하고, 방법을 생각해보다가 어느 정도 가능성이 보여서 블로그로 남겨둔다. 물론 학습용도 이외의 프로젝트에서는 쓸 이유가 없는 방법 ^^;

- 적당한 경로 js 파일을 만들고 아래와 같이 버튼을 만든다. 함수로 만들어 줘야한다 재사용을 위해

```javascript
const btn = (text = "") => {
  const div = document.createElement("div");
  div.style = `
      width : 200px;
      height : 100px;
      cursor : pointer;
      background-color : tomato;
  `;
  div.innerText = text;
  return div;
};
```

- html파일의 바디에 넣어준다

```html
<body>
  <script src="./js.js"></script>
  <script>
    const body = document.querySelector("body");
    const btn1 = btn("hi");
    const btn2 = btn();
    body.append(btn1);
    body.append(btn2);
  </script>
</body>
```

### 결론

- 생각보다 간단하게 작동해서 놀랐다.
- 리엑트등 여타 다른 라이브러리 또한 비슷한 방식이지 않을까 생각을 해봤다.
