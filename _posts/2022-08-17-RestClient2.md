---
layout: single
title: "vscode RestClient 초간단 사용방법"
comments: true
description: "restclient 간단 사용법 입니다"
categories: coding
tag: [RestClient, vscode_extension]
toc: true
author_profile: false
published: true
sidebar:
  nav: "docs"
---

<br/> 
&nbsp;vscode에서 api를 개발 시 간편하게 호출하기 위한 extension이다. 사용법을 간단하게 소개한다.<br/>
- 외부 프로그램 없이 작업하면서 창전환 없이 바로바로 결과물을 확인 할 수 있어 편리하다.
- 호출 파일을 함께 저장할 수 있어 협업 시 바로 호출해 결과를 확인할 수 있다.
- 필요에 따라 호출 파일에 자세한 설명을 제공하여 원활한 소통을 돕는다.
  <br/>

### 설치

- rest client 검색하면 받을수 있다.

 <img src="{{site.url}}\images\2022-08-10\RestClient.jpg" alt="RestClient" style="zoom:50%;" />

<br/>

### 사용법

- 사용법 테스트를 위해 간단한 api를 FastApi로 구현하였다. ~~flask로 구현시 알 수 없는 오류가 발생해 다시!~~
- 아래 예시는 [fasapi 공식문서](https://fastapi.tiangolo.com/tutorial/body/#import-pydantics-basemodel)와 같은 예시이다

  ```python
  # app.py
    from fastapi import FastAPI
    from pydantic import BaseModel


    class Item(BaseModel):
        name: str
        description: str | None = None
        price: float
        tax: float | None = None


    app = FastAPI()


    @app.post("/items/")
    async def create_item(item: Item):
        return item
  ```

- .http 파일 생성 후 다음과 같이 입력
  ![]({{site.url}}\images\2022-08-17\RestClient.jpg)

- 위에 보이는 Send Request를 누르면 Response 창이 열리며 서버 응답을 보여준다.
  ![]({{site.url}}\images\2022-08-17\RestClient2.jpg)

&nbsp;.http 파일을 api를 만들며 잘 정리해두면 docs를 따로 정리 하지 않아도 간략한 내용을 직관적으로 전달하고 바로 확인해 볼 수도 있어 유용하다고 생각한다.<br/>
물론 fastapi는 반응형 api docs를 자동생성 해주기 때문에 코드만 잘 짜놓으면 크게 신경 쓰지 않을 수도있다.<br/>

### 참고한 자료

- [vscode market place](https://marketplace.visualstudio.com/items?itemName=humao.rest-client)
- [자몽이이스티 맛의 기술블로그](https://jamong-icetea.tistory.com/250)
