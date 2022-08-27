---
layout: single
title: "Confusing Typescript"
comments: true
description: "type script 다룰 때 간혹 만날수 있는 햇갈리는 부분"
categories: typescript
tag: ["typescript", "general", "function", "type"]
toc: true
author_profile: false
published: true
sidebar:
  nav: "docs"
---

![]({{site.url}}\images\2022-08-27\typescript.excalidraw.png)
<br/> <br/>
&nbsp; typescript의 함수 type과 관련해 초보자라면~~(=나)~~ 헷갈릴 만한 부분을 좀 정리해보려고 한다. 우선 기본적인 부분을 정리 해보자면, javascript에서 함수는 네가지 방법으로 정의 할 수 있다. 다만, [생성자](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Function)로 정의하는 건 생소한 부분이고 비실용적인것 같아 링크만 남겨둔다.

```javascript
function func1() {
  return "func1";
} //함수 선언식

const func2 = function () {
  return "func2";
}; //함수 표현식

const func3 = () => "func3"; // 화살표함수
```

- 타입스크립트에서 함수의 타입은 type, interface 모두 사용가능하다.

```typescript
type TFunc = (arg: number) => number;
const identical1: TFunc = function (number) {
  return number;
};
const identical2: TFunc = (number) => number;

interface IFunc {
  (arg: number): number;
}
const identical1: IFunc = function (number) {
  return number;
};
const identical2: IFunc = (number) => number;
```

- type 이나 interface 로 따로 정리하지 않아도 함수를 정의할 때 개별 인자에 타입을 넣어 줄 수 있다.

```typescript
function identical1(number: number): number {
  return number;
}
const identical2 = function (number: number): number {
  return number;
};
const identical3 = (number: number): number => number;
```

- 위 예시에서 제네릭을 사용해볼 수도 있다.

```typescript
function identical1<T>(number: T): T {
  return number;
}
const identical2 = function <T>(number: T): T {
  return number;
};

const identical3 = <T extends {}>(number: T): T => number;
```

- 제네릭을 활용한 약간 헷갈릴 수 있는 예시

```typescript
type Type1<T> = (arg: T) => T;
type Type2 = <T>(arg: T) => T;

const identical1: Type1<number> = (arg) => arg;
const identical2: Type2 = (arg) => arg;
const a = identical1(1); // 1
const b = identical2<number>(2); // 2
```

- 위 두 예시를 믹스

```typescript
type Type3<T1> = <T2>(arg1: T1, arg2: T2) => T2;
const strToNum: Type3<string> = (arg1, arg2) => arg2;
const a = strToNum<number>("hi", 2); //2
```

- 함수 리턴 값의 타입

```typescript
function f() {
  return { x: 10, y: 3 };
}
type P = ReturnType<typeof f>;
```

### 참고자료

- [Function](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Function)
- [Typeof Type Operator](https://www.typescriptlang.org/docs/handbook/2/typeof-types.html)
