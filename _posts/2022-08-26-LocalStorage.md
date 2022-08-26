---
layout: single
title: "How to Local Storage"
comments: true
description: "local storage 에 대한 설명글입니다."
categories: javascript
tag: ["javascript", "local storage", "basic"]
toc: true
author_profile: false
published: true
sidebar:
  nav: "docs"
---

![]({{site.url}}\images\2022-08-26\localstorage.png)
<br/> <br/>
&nbsp; 요즘 인증 인가와 같은 개념들을 공부중인데, 우연히 네이버가 ui 정보를 로컬스토리지에 저장하고 있는 것 같아서 본김에 관련 정보를 기록해두려고 한다. <br/> &nbsp; 다음은 본인의 브라우저에 네이버가 남겨둔 로컬 스토리지 이다.
![]({{site.url}}\images\2022-08-26\localstorage1.jpg)

&nbsp; 더보기를 눌렀을 때 왜 이쪽으로 빼놨을까 라고 생각하면서 궁금해서 value값을 몽땅 지웠지면 역시 직접적으로 ui가 바뀌지는 않았다. 그런데 키값을 보면서 확실하지는 않아도 유추 해볼수 있었다.
![]({{site.url}}\images\2022-08-26\localstorage2.jpg)<br/>

&nbsp; favorite으로 시작하는 걸 보면 유저의 선호 메뉴를 저장해주는 도구인 것 같다.

1. 쿠키에 담에서 매 리퀘스트 때마다 서버가 받을 필요가 없는 데이터
2. 보안상의 문제를 일으키지 않는 데이터
3. 다른 유저들과 상호 작용 할 필요없는 데이터
4. 그러나 유저의 선호를 반영해 사용자 경험을 개선 할 수 있는 데이터

&nbsp; 이 정도 조건에 만족이 된다면, 로컬 스토리지로 사용하기에 적절하지 않을까 생각해본다.
&nbsp; 물론 같은 유저 선호를 반영하는 데이터라고 해도 좋아요를 받은 갯수가 중요한 서비스의 경우 로컬스토리지를 사용하면 안되겠다. 3. 다른 유저들과 상호작용을 하고 있으니

### 사용방법

&nbsp; local storage는 꽤나 직관적이고 쉽게 접근해 사용가능 하다. 다만 다음과 같이 object를 바로 저장할 수 없고 `JSON.stringify` 와 `JSON.parse` 를 사용해 입출력 해줘야 한다.<br/>
![]({{site.url}}\images\2022-08-26\localstorage3.jpg)<br/>
&nbsp; 또한 꺼져도 데이터가 유지된다는 점에서 개인적인 용도의 어플리케이션이라면 충분히 고려해볼만 하다. 대표적으로 todo앱 또는 메모앱을 생각해볼수 있을 것 같다.
그리고 실제 데이터베이스를 구성하기전, 메모리로만 간이 디비를 사용하기는 약간 부족한 점이 있을 때, 활용해보기도 좋을 것 같다.<br/>
&nbsp; 그리고 [useLocalStorage](https://www.npmjs.com/package/use-local-storage)가 왠지 있을 것 같아서 검색해보니 역시 있었고 나중에 한번 빠르게 빌드가 필요할 때 활용할 수 있을 것 같다.

### 기능 지원 여부

&nbsp; 마지막으로 localstrage를 사용할 때 염두에 두어야하는 점이라면 지원하지 않거나 하는 등의 여러가지 이유로 오류가 날수 있다는 점을 인지해야 한다. [여기](https://developer.mozilla.org/ko/docs/Web/API/Web_Storage_API/Using_the_Web_Storage_API#사용_가능_검사)에서 사용 가능 검사함수를 소개 하고 있다.<br/>

```javascript
function storageAvailable(type) {
  var storage;
  try {
    storage = window[type];
    var x = "__storage_test__";
    storage.setItem(x, x);
    storage.removeItem(x);
    return true;
  } catch (e) {
    return (
      e instanceof DOMException &&
      // Firefox를 제외한 모든 브라우저
      (e.code === 22 ||
        // Firefox
        e.code === 1014 ||
        // 코드가 존재하지 않을 수도 있기 떄문에 이름 필드도 확인합니다.
        // Firefox를 제외한 모든 브라우저
        e.name === "QuotaExceededError" ||
        // Firefox
        e.name === "NS_ERROR_DOM_QUOTA_REACHED") &&
      // 이미 저장된 것이있는 경우에만 QuotaExceededError를 확인하십시오.
      storage &&
      storage.length !== 0
    );
  }
}
///
if (storageAvailable("localStorage")) {
  // 야호! 우리는 localStorage를 사용할 수 있습니다.
} else {
  // 슬픈 소식, localStorage를 사용할 수 없습니다.
}
```

<br/> <br/>

### 결론

&nbsp; 로컬스토리지는 브라우저에 직접 저장하며 브라우져를 껏다가 켜도 데이터가 유지 되며 쉽게 사용할 수 있다는 장점이 있지만, 일반적인 서비스의 경우 서비스 로직의 중요한 부분을 담당하도록 하는 것은 바람직 하지 않다.<br/> &nbsp; 고객의 사용성 개선을 주된 관심사로 두고서 제한적인 부분에서 사용해야한다. 이외에 개인적 프로젝트를 만들거나, 빠르게 crud를 시험해보는게 필요한 상황이라면 빠르게 구성해 적용 해볼 수 있을 것 같다.

### 참고자료

- [Web Storage API 사용하기](https://developer.mozilla.org/ko/docs/Web/API/Web_Storage_API/Using_the_Web_Storage_API)
