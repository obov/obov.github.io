---
layout: single
title: "vscode RestClient 사용방법"
comments: true
description: ""
categories: coding
tag: [RestClient, Error]
toc: true
author_profile: false
published: true
sidebar:
  nav: "docs"
---

### RestClient ?

- vscode에서 api를 개발하며 간편하게 디버깅하기 위한 extension이라고 할 수 있다.

- 디버깅 파일을 함께 저장할 수 있어 함께 작업하는 상황에서 상세 api문서 없이 api호출을 해볼 수 있다.

  <br/>

### 설치

- rest client 검색하면 받을수 있다.

 <img src="{{site.url}}\images\2022-08-10\RestClient.jpg" alt="RestClient" style="zoom:50%;" />

<br/>

### 사용법

- 사용법 테스트를 위해 간단한 api를 flask로 구현하였다

  ```python
  # app.py
  from flask import Flask
  app = Flask(__name__)

  @app.route('/')
  def home():
     return "hi"


  if __name__ == '__main__':
     app.run('0.0.0.0',port=5000,debug=True)
  ```

- .http 파일 생성 후 다음과 같이 입력

  <img src="..\..\images\2022-08-10\RestClient-4.jpg" alt="RestClient" />

- 위에 보이는 Send Request 를 누르면 원래 되야하는데... connection 오류가 났다.

  <img src="..\images\2022-08-10\RestClient-5.jpg" alt="RestClient" />

- 다행히 서버를 살짝 수정하고 바로 작동했다. 사진처럼 양쪽으로 분할해두면 확인하기 편하다

```python
# app.py
if __name__ == '__main__':
   app.run('127.0.0.1',port=5000,debug=True)
```

<img src="..\images\2022-08-10\RestClient-6.jpg" alt="RestClient" />

- 여기까지 글을 쓰고 POST json 전송 예시를 들면서 글을 마무리 하려고 했는데 문제가 발생했다.

- flask 서버에서 requst.form이 텅~ 비어있는 에러가 발생했다.

- 비슷한 확장프로그램이 rapid api라고 있는데, 그걸 활용해봐도 같은 문제가 발생하고 postman으로 같은 request를 보냈을 때는 문제가 없었다.

- 지금으로선 vscode 확장프로그램에서 요청보낼때 문제가 발생하는게 아닐까 추측해본다.

- 추후 알게되면 업데이트 해야겠다.

- ~~닦다가만 느낌이 영찜찜하다~~

### 참고한 자료

-
