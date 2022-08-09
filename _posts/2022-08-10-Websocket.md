---
layout: single
title: "웹소켓 간략한 설명 및 vanilla js로 구현"
comments: true
description: ""
categories: javascript
tag: [blog, websocket]
toc: true
author_profile: false
published: true
sidebar:
  nav: "docs"
---

### 웹소켓이란?

- http연결은 client가 server에게 요청을 해야만 응답을 하는 방식이다. 기존에 제공받은 데이터 이외의 데이터를 제공받으려면 반드시 request를 해야하며 request 한번을 하면 response 또한 한 번 일어난다.
- 또한 stateless 연결이어서 상태를 자체적으로 저장하지 않는다. 쿠키등을 황용해 사용자에게 계속적으로 서비스 받고 있다는 느낌이 들게 할수는 있지만, 근본적으로 http는 상태에는 관심이 없다.
- 이에 반해 **ws** 또는 **wss** 연결은 마치 client와 server가 악수를 하고있는 상태이다. client가 server에게 ws 요청을 하면 server 와 client는 connection이 만들어지고 한번 만들어진 이후에는 서로 다양한 요청을 주고 받을 수 있다.
- connection이 만들어진 이후라면 양쪽 어디서든 상대방의 반응을 기다리지 않고 원하는 때 즉시 데이터를 주고 받을 수 있다.
- 대부분의 실시간 서비스는 웹소켓을 사용한다고 볼 수 있다. 채팅, 화상전화, 게임 등등
- serverless 서비스는 웹소켓을 구현하는데 한계가 있는 것으로 알고 있다.

- ![](..\assets\images\Websocket_connection.png "이미지 출처 : https://commons.wikimedia.org/wiki/File:Websocket_connection.png")
  <br/><br/><br/>

### 실제구현 방법

- [ws 라이브러리](https://www.npmjs.com/package/ws)를 설치하고

```javascript
// serverside

const wss = new WebSocket.Server();
wss.on("connection", (socket) => {
    // 여기에 필요한 연결이후 필요한 작업을 정의한다.
}

// clientside
const socket = new WebSocket(`ws://${window.location.host}`);
socket.addEventListener("open", () => {
});
socket.addEventListener("message", (message) => {
});
socket.addEventListener("close", () => {
}); // 각각 연결됐을때, 서버로부터 데이터를 받았을때, 연결이 끝났을때 실행되는 작업을 정의하여 사용하면 된다.
```

- 아주 기본적인 코드이며 실서비스에서는 전혀 다른 방식으로 구현되고 있을 가능성 99% 이다.

### 참고한 자료

- [줌클론코딩강의](https://nomadcoders.co/noom/lobby)
