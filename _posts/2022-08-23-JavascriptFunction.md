---
layout: single
title: "Javscript Function 이 가지고 있는 Methods"
comments: true
description: "apply, bind, call 에 대한 설명글 입니다"
categories: javascript
tag: ["javascript", "function"]
toc: true
author_profile: false
published: true
sidebar:
  nav: "docs"
---

![]({{site.url}}\images\2022-08-23\javascript.excalidraw.jpg)

<br/> <br/> <br/>

&nbsp; 리액트를 클래스로 다룰때 가끔씩 보이는 문법으로 `bind()` 를 볼수 있었다. 이번에 js함수라면 기본적으로 내장된 메서드 `apply()`, `bind()`, `call()` 에 대해 정리해보려고 한다.

> `func.apply( thisArg, [argsArray] )`

&nbsp; `apply()` 는 첫째 인자로 this로 사용할 인자를 받는다. 이후 해당 원소를 돌며 thisArg.func를 시행한다.<br/> &nbsp; forEach와 같은 구문은 배열에만 존재하며 유사배열 객체에는 통상 for문을 적용해야한다.<br/> &nbsp; 그런데 apply를 활용하면 for 문 없이 유사배열 객체(배열도 유사배열객체)에 각 요소를 돌며 함수를 실행시킬 수 있다.<br/> &nbsp; `thisArg` 에 null을 대입하면 `func(...[argsArray])` 와 같은 실행을 한다.<br/><br/>

```javascript
const array = ["a", "b"];
const elements = [0, 1, 2];
[].push.apply(array, elements);
console.info(array); // ["a", "b", 0, 1, 2]

const unrray = { 0: "a", 1: "b", length: 2 };
const elements = [0, 1, 2];
[].push.apply(unrray, elements);
console.log(unrray); //{ 0 :"a", 1:"b", 2:0, 3:1, 4:2, length:5}

const unrray = { 0: "a", 1: "b", length: 2 };
const elements = [0, 1, 2];
[].push.apply(elements, unrray);
console.log(elements); //[0, 1, 2, 'a', 'b']

console.log.apply(null, [1, 2, 3]); // 1,2,3

alert.apply(null, [1, 2, 3]); // alert message: 1
```

<br/> <br/> <br/>

> `func.bind( thisArg )` or `func.bind( thisArg, arg1, /* …, */ argN )`

&nbsp; `bind()`는 해당 함수의 인자들을 확정하여 **새로운 함수를 반환하는** 매서드이다. `bind()`의 첫재인자는 매서드 내에서 사용된 `this`에 전달된다. `this`는 해당함수가 호출되는 맥락에 따라 전혀 다른 값을 같게 되는 데, 그러한 불확실성을 줄이기 위해 사용된다.<br/>
&nbsp; 두번째 인자부터는 `func`의 parameter로 순차적으로 대입되며 parameter가 고정된 함수를 반환 한다.<br/>

```javascript
const module = {
  x: 42,
  getX: function () {
    return this.x;
  },
};

console.log(module.getX()); // 42

const unboundGetX = module.getX;
console.log(unboundGetX()); // The function gets invoked at the global scope
// expected output: undefined

const boundGetX = unboundGetX.bind(module);
console.log(boundGetX());
// expected output: 42
```

<br/> <br/> <br/>

> `call()` or `call(thisArg)` or `call(thisArg, arg1, /* …, */ argN)`

&nbsp; `call()` 은 해당 함수/매서드를 다른 객체에서도 사용할수 있게 해준다.<br/><br/>

```javascript
function Product(name, price) {
  this.name = name;
  this.price = price;
}

function Food(name, price) {
  Product.call(this, name, price);
  this.category = "food";
}

console.log(new Food("cheese", 5).name);
// expected output: "cheese"
```

<br/> <br/>

### 결론

&nbsp; `apply()`, `bind()`, `call()` 모두 특정 객체의 매서드로 정의된 함수를 다양한 형태의 또 다른 객체에서도 활용할 수 있다는 점에서 **코드의 재사용성**을 굉장히 높혀주는 매서드 라고 생각한다.<br/> <br/>

### 참고자료

- [# Function.prototype.apply()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/apply)
- [# Function.prototype.bind()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/bind)
- [# Function.prototype.call()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/call)
