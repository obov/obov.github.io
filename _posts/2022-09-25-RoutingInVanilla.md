---
layout: single
title: "vanilla JS로 라우팅"
comments: true
description: "spa를 구현하는데 있어 페이지간 자연스러운 전환은 필수적 요소이다. vanilla javascript 화면만 바뀌는 것이 아닌 주소창, 뒤로가기 등의 기능이 자연스러운 동작을 하기 위해 고려해야하는 부분을 정리해보았다."
categories: "Javscript"
tag: ["javascript", "web", "browser"]
toc: true
author_profile: false
published: true
sidebar:
  nav: "docs"
---

![]({{site.url}}\images\2022-09-25\title.png)
<br/> <br/> <br/>
&nbsp;우리는 웹을 사용하면서 수없이 많은 사이트를 돌아다닌다. 그리고 그 사이트에는 정말 수없이 많은 페이지들이 있다. 현대 프로그래밍 기술의 발전으로 일일이 페이지를 만들지 않아도, 동적으로 페이지를 만들 수 있게 되었고,

> 우리는 하루도 빠지지 않고 울창한 페이지 정글 속을 해쳐나가며 내게 필요한 정보를 찾아 헤멘다.

&nbsp; 이러한 페이지 정글 속에서 어떻게 사용자가 길을 잃지 않고, 자연스러운 **페이지 전환**을 구현 할 수 있는지 최근 공부하며 알게된 부분을 짧게 공유하려고 한다.

### 이 글을 읽고나면

&nbsp; 우리가 웹정글을 해쳐나가면서 숨쉬듯 너무나 당연하게 사용하고 있던 기능이 사실은 모두 구분 동작의 연속체 였다는 사실을 알게 될 것이다. 즉, [spa](https://ko.wikipedia.org/wiki/%EC%8B%B1%EA%B8%80_%ED%8E%98%EC%9D%B4%EC%A7%80_%EC%95%A0%ED%94%8C%EB%A6%AC%EC%BC%80%EC%9D%B4%EC%85%98)에 대한 이해도를 높히는 데 도움이 될 것이라 생각한다.
<br/> &nbsp; 나아가 보안 또는 여러가지 이유로 뒤로가기로는 접근해서는 안되는 경우 해당 경로로의 접근을 막는 로직 구현에 도움을 줄 수 있지 않을까 생각해본다.

<br/>

### 용어 및 한계 설정

&nbsp; 본격적인 글을 시작하는 데 앞서 본 글에서 사용하려는 용어가 애매하게 느껴질 수 있어서 해당 부분에 대해 언급이 필요한 것 같다. **페이지 전환** 이라고 하면 소위 라우팅이라고 표현하는 말과 같은 뜻이고, 단순히 **화면을 변경하는 것**은 유저입장에서 본인이 보고 있는 페이지에 어떤 변화가 생기는 것을 말한다.
<br/> &nbsp; 그리고 리엑트등의 라이브러리를 통한 라우팅 혹은 a태그를 통한 **페이지 전환**은 배제하고 Vanilla JS를 통한 **페이지 전환**에 집중하려고 한다.<br/>

### 페이지 전환 시 필요한 기능

1. 화면 바꿔주기
2. 주소창을 적절하게 수정해주기
3. 새로고침을 하거나 뒤로가기를 했을 때 자연스럽게 보여주기
   <br/> <br/>

#### 화면 바꿔주기

&nbsp; 유저가 보고 있는 **화면을 움직이는 방법**은 너무나 많다. javscript를 활용해 특정 요소의 색을 변경한다던지, 서버에 요청을 보내 특정 데이터를 화면에 보여준다든지 등의 일련의 모든 작업은 유저가 보는 **화면을 변경**시킨다. css만 활용해도 유저입장에서 화면에서 특정요소만 변경되는 것이 아니라 어떤 페이지로 들어갔다고 느끼게 만들 수 있다. **화면변경**은 어쩌면 우리가 프로그래밍을 공부하는 이유일 것이다.<br/>
&nbsp; 웹페이지의 구조를 구현하는 데 중요한 것은 **화면의 어떤 부분이 변경**되었을때 사용자 입장에서 화면만 살짝 변경된 게 아니고 **페이지가 변경**되었다고 생각하는지, 그리고 그에 맞는 자연스러운 구현일 것이다.

> &nbsp; 어떤 게시물에 좋아요만 눌러도 url이 변경되도록 구현할 수는 있지만, 유저입장에서 뒤로 가기를 누르다가 내가 좋아요 표시를 한 게시물이 취소가 된다면 좀 황당할 것이다.

&nbsp; **화면 변경**은 **페이지 전환(=라우팅)**의 필요조건으로 다음 몇가지 사항을 추가해줘야한다.

#### 주소창을 적절하게 수정해주기

&nbsp; [spa](https://ko.wikipedia.org/wiki/%EC%8B%B1%EA%B8%80_%ED%8E%98%EC%9D%B4%EC%A7%80_%EC%95%A0%ED%94%8C%EB%A6%AC%EC%BC%80%EC%9D%B4%EC%85%98) 라우팅은 주소창을 변경하면서 시작이라고 생각하면 된다. 다음과 같은 코드로 주소창을 변경할 수 있다.

```javascript
const state = { page_id: 1, user_id: 5 };
const url = "hello-world.html";

history.pushState(state, "", url);
```

[출처 : MDN - History.pushState()](https://developer.mozilla.org/en-US/docs/Web/API/History/pushState)

&nbsp; 여기서 세번째 인수 `url`이 주소창 자체를 변경시키는 부분이고 첫번째 인수 `state`와 두번째 인수 `unused`은 해당페이지에 대한 정보를 전달하는 부분이다. state로 전달된 값은 [`history.state`](https://developer.mozilla.org/en-US/docs/Web/API/History/state)에서 확인 할 수 있다. 주소창을 변경할 때 전달 되는 값이므로 해당 페이지에 대한 정보를 저장해 둘 수 있다. `unused`는 [mdn 공식문서](https://developer.mozilla.org/en-US/docs/Web/API/History/pushState)에 따르면 현재 사용하지 않는 인수로 `""`를 전달해주면 되고 추후에는 변경될 수 있다고 한다. <br/>
&nbsp; 이렇게 [`pushState`](https://developer.mozilla.org/en-US/docs/Web/API/History/pushState)를 하면 말그대로 주소창이 변경되고 뒤로 가기도 생긴다*(**화면 변경**은 전혀 없는 채로)*. page stack에 데이터하나가 push되는 것과 같다고 볼수 있다. 또 뒤로가기 후 되돌아왔을 때도 좀전에`pushState()`함수를 통해 전달한 `state`가 유지된다. 그래서 페이지 별로 동적인 타이틀을 사용하는 경우 페이지 별로 해당 값을 저장해두고 뒤로가기 또는 앞으로가기를 통해 해당 페이지에 접근했을때 [`document.title`](https://developer.mozilla.org/en-US/docs/Web/API/Document/title)을 변경해 페이지의 타이틀을 관리할 수 있다. 이외에 위 예시와 같이 페이지 정보 유저정보도 담아 페이지를 정보를 관리할 수 있다(저렇게 하는게 적절한 방식인지 와는 별개로).<br/>

### 새로고침을 하거나 뒤로가기를 했을 때 자연스럽게 보여주기

&nbsp; 본인은 MDN 사이트에서 공부를 하다가 새롭게 알게된 정보가 있다면 개발자도구를 바로 열어 사용해보며 간단하게 익혀보는 방식을 즐겨 사용한다. 이번에도 위 코드를 받은 해당 페이지에서 코드를 바로 실행해보며 [`pushState`](https://developer.mozilla.org/en-US/docs/Web/API/History/pushState)에 대해 설명 하고자 한다. [`pushState`](https://developer.mozilla.org/en-US/docs/Web/API/History/pushState)를 한 경우 새로고침을 했을때 이 단락에서 다뤄보려는 **첫번째 문제**를 만날 수 있다.

![]({{site.url}}\images\2022-09-25\pushState.gif)
&nbsp; 생각해보면 너무 당연할 수 있다. 브라우저는 새로고침했을 때 해당 주소창의 주소로 서버에 GET 요청을 보내 html을 새로 받아 온다. 하지만 임의로 주소창을 수정했으니 새로고침 했을 때 서버로부터 html을 제대로 받아오지 못한 것이다. <br/>
&nbsp; 상상력을 발휘해보자! 위의 예시가 동적인 **화면 전환**을 구현하고, 해당 **화면 변경**을 유발하는 핸들러에 `pushState` 함수를 사용해 url도 `"hello-world.html"` 로 변경했다고 가정해보자.
<br/> &nbsp; 여기까지만 했을 때 유저가 해당 핸들러를 사용한 후 모종의 이유로 새로고침을 누르거나, 해당링크를 복사해뒀다가 나중에 접속하려는 경우에 위처럼 404 response 를 받게 될 것이다.

> &nbsp; 즉 spa를 구현하려면
>
> 1.  동적인 **화면 변경**과 함께
> 2.  변경된 화면과 같은 모습을 그려주는 html(적어도 열리며 동적으로 변경된 부분을 반영하는)
>
> 을 response 해주는 api를 함께 고려해야 한다.

&nbsp; **두번째 문제**는 뒤로가기 후 되돌아왔을때 발생한다.
![]({{site.url}}\images\2022-09-25\back_and_forward2.gif)
<br/><br/>
&nbsp; 그냥 보기에는 좀전과 같은 상황으로 보이지만 분명히 다른 문제 상황이다. 물론 서버에 요청을 보냈겠지만 다른 방식으로 요청을 보냈을 것이고, vanilla javascript로 [spa](https://ko.wikipedia.org/wiki/%EC%8B%B1%EA%B8%80_%ED%8E%98%EC%9D%B4%EC%A7%80_%EC%95%A0%ED%94%8C%EB%A6%AC%EC%BC%80%EC%9D%B4%EC%85%98)를 구현하려면 반드시 고려해줘야한다. 아래에서 브라우저상에서 직접확인할수 있는 차이점을 보여준다.
![]({{site.url}}\images\2022-09-25\reload_bf.gif)
&nbsp; 함께 비교하자면 뒤로가기 > 앞으로가기 상황에서는 변경된 부분만 바뀌고 있음이 개발자도구 element 탭에서 확인된다. 또한 reload 상황에서는 페이지 전체가 다시 로드 된다. 즉, 둘다 서버에 요청을 보낸것은 맞지만 서로 다른 요청을 보냈다는 것을 알수 있다. (실제로 해당 사이트에서는 둘간에 title이 서로 다르다.)

> &nbsp; 기술적으로 좀더 살펴보자면 refresh는 [`location.reload()`](https://developer.mozilla.org/en-US/docs/Web/API/Location/reload)를 실행시킨 결과이고, 뒤로가기 앞으로가기는 `window`객체의 [`popstate`](https://developer.mozilla.org/en-US/docs/Web/API/Window/popstate_event)이벤트를 바라보는 핸들러를 실행시킨 결과이다.<br/>

&nbsp; 해당 내용을 간단한 그림으로 도식화 시켜볼수 있겠다.

#### ancher tag 사용시

![]({{site.url}}\images\2022-09-25\new_page.png)

#### spa 첫번째

![]({{site.url}}\images\2022-09-25\new_page_spa.png)

#### spa 두번째 뒤로가기 앞으로가기

![]({{site.url}}\images\2022-09-25\popstate_event.png)

#### spa 세번째 refresh

![]({{site.url}}\images\2022-09-25\refresh.png)

&nbsp; 즉 [spa](https://ko.wikipedia.org/wiki/%EC%8B%B1%EA%B8%80_%ED%8E%98%EC%9D%B4%EC%A7%80_%EC%95%A0%ED%94%8C%EB%A6%AC%EC%BC%80%EC%9D%B4%EC%85%98)를 vanilla js 로 자연스러운 라우팅을 구현하기 위해서는 ancher 태그로 접근 하는경우를 제외한 아래쪽 세가지 접근경로에 대해 같은 페이지를 보여줄 수 있도록 구현해야 한다.
<br/> &nbsp; 또한 첫번째와 두번째에서 서버에 요청하는 핸들러는 같은 작업을 수행한다고 볼수 있다. 다만 차이점은 첫번째의 경우 핸들러가 어떤 이벤트를 바라보고 있는지 명시 해두지 않고 있다는 점의 차이만 있다. 본질적으로는 첫번째 경우가 두번째 경우를 포함한다고 볼수도 있다.
<br/> &nbsp; 다른 관점에서 보자면 리엑트와 같이 손쉬운 라우팅을 지원하는 모던 웹라이브러리의 경우 해당 부분을 묶어서 추상화 시켜두었다고 이해해볼 수 있을 것이다.

### 정리하기

&nbsp; vanilla js 를 통해 [spa](https://ko.wikipedia.org/wiki/%EC%8B%B1%EA%B8%80_%ED%8E%98%EC%9D%B4%EC%A7%80_%EC%95%A0%ED%94%8C%EB%A6%AC%EC%BC%80%EC%9D%B4%EC%85%98)를 구현하려는 경우 우선적으로 고려해야하는 것들.

1. **화면 바꿔주기**
   - 어떤 방식으로 화면이 변경되든 우리는 개발자로서 페이지라고 정의할 수 있지만 그것이 적절한지 고민이 필요
2. **주소창 적절하게 수정해주기**
   - [`history.pushState()`](https://developer.mozilla.org/en-US/docs/Web/API/History/pushState) 함수를 통해 수정가능
   - 필요한 경우 함수의 첫번째 인자로 객체를 전달하여 해당 페이지의 state를 저장할 수 있다.
3. **새로고침을 하거나 뒤로가기를 했을 때 자연스럽게 보여주기**
   - 화면 변경을 유발하고 주소창을 변경하는 핸들러와 [window 객체](https://developer.mozilla.org/en-US/docs/Web/API/Window)의 [`popstate`](https://developer.mozilla.org/en-US/docs/Web/API/Window/popstate_event) 이벤트를 바라보는 핸들러가 화면 변경에 대해서는 같아야함
   - 해당 주소에 get 요청을 보내는 모든 경우, 즉 해당 주소에서 [`location.reload()`](https://developer.mozilla.org/en-US/docs/Web/API/Location/reload) 함수를 사용, 유저가 refresh를 사용, 주소창에 해당 주소를 입력했을 때 변경된 화면과 동일한 모습의 html을 반환하거나 최소한 동적으로라도 변경된 화면과 같은 화면을 보여줘야 함

<br/> <br/>

<hr/>

> &nbsp; 모던 웹라이브러리로 손쉽게 작은 정원이든 울창한 숲이든 라우팅을 손쉽게 구현할 수 있는 시점에 이글에서 설명한 내용들이 직접적인 효용을 갖기는 어려울 수 있다.<br/> &nbsp; 하지만 모든 지식의 가치는 지식 자체로서 보다는 활용하는 사람에 의해 결정된다고 생각하는 입장에서는 어떤 지식도 사소할 수 없다. 누군가가 언젠가 이글의 지식을 활용에 누구도 모르는 아주 작은 문제를 해결하는데 약간이나마 도움이 된다면, 그걸로 충분하다.

&nbsp; 부족한 게 많은 초보개발자가 작성한 글입니다. 이글에서 vanilla js spa를 구현하는데 있어, 특히 라우팅과 관련하여 놓친 부분이 있다면 넉넉한 마음으로 생각해주시길 바라며 도움이 될 만한 내용이 있을 경우 댓글을 통해 알려주시면 살펴보겠습니다. 끝까지 읽어주셔서 감사합니다.

### 참고

- [History.state](https://developer.mozilla.org/en-US/docs/Web/API/History/state)
- [Document.title](https://developer.mozilla.org/en-US/docs/Web/API/Document/title)
- [History.pushState()](https://developer.mozilla.org/en-US/docs/Web/API/History/pushState)
- [Window: popstate event](https://developer.mozilla.org/en-US/docs/Web/API/Window/popstate_event)
- [location.reload()](https://developer.mozilla.org/en-US/docs/Web/API/Location/reload)
- [Window](https://developer.mozilla.org/en-US/docs/Web/API/Window)

### 연관된 링크

- [SPA](https://ko.wikipedia.org/wiki/%EC%8B%B1%EA%B8%80_%ED%8E%98%EC%9D%B4%EC%A7%80_%EC%95%A0%ED%94%8C%EB%A6%AC%EC%BC%80%EC%9D%B4%EC%85%98)
- [History.replaceState()](https://developer.mozilla.org/en-US/docs/Web/API/History/replaceState)
- [Document.title](https://developer.mozilla.org/en-US/docs/Web/API/Document/title)
