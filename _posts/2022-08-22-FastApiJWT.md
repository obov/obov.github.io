---
layout: single
title: "JWT 작동방식 이해해보기"
comments: true
description: "FastApi와 RestApi를 활용해 JWT가 어떻게 구동되는지 정리해보았다"
categories: JWT
tag: ["JWT", "FastApi", "RestApi"]
toc: true
author_profile: false
published: true
sidebar:
  nav: "docs"
---

![]({{site.url}}\images\2022-08-22\fastapi.excalidraw.png)
<br/><br/><br/><br/>
&nbsp; JWT가 작동하는 방식을 이해해보는 시간을 가져보자. JWT는 쉽게 생각하자면 고객에게 찍어주는 쿠폰도장인데 좀 복잡하게 만들어져 있어서 나만 알아볼수 있는 도장이다.<br/>
&nbsp;비유적으로는 그렇고 간단한 서버를 만들어서

- 어떻게 도장을 만들고
- 어떻게 나만 알아보는지 이해해보자!

&nbsp; 오늘도 가볍게 자주쓰는 FastApi를 Rest Client와 함께 사용했다.<br/>
&nbsp;[Rest Client 사용법]({{site.url}}\coding\RestClient2)<br/>
&nbsp;[FastApi 사용법]({{site.url}}\python)
<br/><br/>

### 기초공사

&nbsp; 우선 간단한 서버를 만들고 .http 파일을 만들어 response를 서버가 돌아간다는 사실을 확인한다.

```python
# main.py

from fastapi import FastAPI
app = FastAPI()
async def get():
    return "<p>GET, World!</p>"

@app.post("/")
async def post():
    return "<p>POST, World!</p>"
```

<br/><br/>
&nbsp; 이번에는 서버가 post body를 통해 중요한 데이터를 받을 수 있도록 준비한다.
<br/>
![]({{site.url}}\images\2022-08-22\fastapi_jwt1.jpg)

```python
from fastapi import FastAPI
from pydantic import BaseModel
app = FastAPI()

@app.get("/")
async def get():
    return "<p>GET, World!</p>"

class Important(BaseModel):
    to_be_encoded: str

@app.post("/")
async def post(important:Important):
    print(important)
    return "<p>POST, World!</p>"

```

&nbsp; print가 잘되는 지 확인되면, 이번에는 JWT 토큰으로 암호화를 시켜보자
<br/>

### JWT 암호화 (도장 만들기)

```python
# 중복되는 부분은 제거했다
# pip install pyjwt

import jwt
@app.post("/")
async def post(important:Important):
    encoded_jwt = jwt.encode({"hi": important.to_be_encoded}, "secret", algorithm="HS256")
    print(encoded_jwt)
    return "<p>POST, World!</p>"

```

&nbsp; 긴 문자 가운데 점이 두개 찍혀있는 걸 보면 인코딩이 잘된 걸 확인할 수 있다.
![]({{site.url}}\images\2022-08-22\fastapi_jwt5.jpg)<br/><br/>
&nbsp; 토큰이 잘 print 되는 걸 확인했으니 [JWT](https://jwt.io/)에서 파싱하면 어떻게 나오는지 확인해보자

1. 먼저 비밀키를 넣어준다. [pyjwt](https://pyjwt.readthedocs.io/en/stable/)의 대문에 있는 예시를 활용하느라 비밀키를 변경하지 않았다.
2. 아까 서버콘솔에 찍힌 토큰을 왼쪽에 넣어준다.
3. 가운데 페이로드에 "hi":"I'm important data"가 찍혀있는걸 확인할 수 있다.
   ![]({{site.url}}\images\2022-08-22\fastapi_jwt6.jpg)

<br/><br/>

### JWT 유저에게 전송 (도장 찍어주기)

&nbsp; 이번에는 post요청과 해당 데이터를 쿠키로 만들어 유저에게 삽입하는 과정을 진행해보자

```python
from fastapi import Cookie, Response

@app.post("/cookie")
async def post_cookie(important:Important,response: Response):
    encoded_jwt = jwt.encode({"hi": important.to_be_encoded}, "secret", algorithm="HS256")
    response.set_cookie(key="hijwt", value=encoded_jwt)
    print(encoded_jwt)
    return "<p>POST, World!</p>"
```

&nbsp; 위처럼 서버를 준비하고 아래처럼 request를 날린다. 그런데 [RestClient]({{site.url}}\coding\RestClient2)환경에서 어떻게 쿠키를 받았는지 확인할 수 있을까? ~~보내보면 알 수 있다.~~
![]({{site.url}}\images\2022-08-22\fastapi_jwt8.jpg)

<br/> <br/>
&nbsp; response 헤더에 [set-cookie](https://developer.mozilla.org/ko/docs/Web/HTTP/Headers/Set-Cookie)라는 부분이 바로 브라우저에 저장이 되는 쿠키 부분이다.
![]({{site.url}}\images\2022-08-22\fastapi_jwt9.jpg)
<br/> <br/>

### 유저로부터 받은 JWT 디코딩 (도장 알아보기)

&nbsp; 이제 마지막 단계로 [set-cookie](https://developer.mozilla.org/ko/docs/Web/HTTP/Headers/Set-Cookie)에 있던 쿠키를 담아 request를 날려서 서버에서 그걸 디코딩 해보자
<br/>
&nbsp; 디코딩해줄 서버를 준비하고, request header에 [Cookie](https://developer.mozilla.org/docs/Web/HTTP/Headers/Cookie "한글번역이 좀 애매하게 된것 같아 원문 링크를 걸었음")라는 헤더를 넣고 전해받은 쿠키를 모두 넣어준다.

```python
@app.get("/cookie")
async def get_cookie(hijwt=Cookie()):
    decoded = jwt.decode(hijwt, "secret", algorithms=["HS256"])
    print(decoded)
    return "<p>Cookie, World!</p>"
```

![]({{site.url}}\images\2022-08-22\fastapi_jwt11.jpg)
<br/> <br/>
&nbsp; **서버쪽에 log가 잘 나온다!**
![]({{site.url}}\images\2022-08-22\fastapi_jwt12.jpg)

&nbsp; 서버쪽으로 데이터가 왔을 때 추가데이터를 넣었다면 좀더 괜찮은 예제 였겠지만, 다음에 하는걸로 하자. 살짝 아쉬움은 남지만, 지금까지 순조롭게 따라왔다면 JWT에 대한 기본적이고 전반적인 이해가 됐을 거라고 생각한다.

### 참고자료

- [Set-Cookie](https://developer.mozilla.org/ko/docs/Web/HTTP/Headers/Set-Cookie)
- [Cookie](https://developer.mozilla.org/docs/Web/HTTP/Headers/Cookie)
- [Cookies](https://developer.mozilla.org/ko/docs/Web/HTTP/Cookies)
- [JWT](https://jwt.io/)
- [pyjwt](https://pyjwt.readthedocs.io/en/stable/)
