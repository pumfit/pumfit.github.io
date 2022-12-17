﻿---
title: "[코드잇] 모던 자바스크립트 01"
date: 2022-11-26 20:00:06+0900
categories: [JavaScript,ModernJavaScript]
tags: [ECMAScript,Symbol,Bigint,Boolean]
---
해당 포스트는 코드잇 모던 자바스크립트 강의 내용을 정리한 내용입니다.

# **👀 모던 자바스크립트란?**
    
## ECMAScript

> **ECMAScript**는 JavaScript의 기반이 되는 스크립팅 언어 명세로 자바스크립트를 표준화하기 위해 만들어 졌다. [Ecma International](https://www.ecma-international.org/)에서 ECMAScript 표준화를 담당하고 있다.

이때, ECMAScript 버전중 가장 많은 발전이 있었던 ES2015 이후의 자바스크립트를 ES6+ 라고 부른다.

### JavaScript vs ECMAScript

JavaScript와 ECMAScript의 차이점
> 1. ECMAScript는 **프로그래밍 언어의 표준**이고 JavaScript는 **프로그래밍 언어**이다. 즉,  ECMAScript는 JavaScript가 갖추어야 할 내용을 정리해둔 **'설명서'**이고, JavaScript는 ECMAScript를 준수해서 만들어낸 **'결과물'** 이다.  
> 2. **JavaScript는** ECMAScript를 기반으로 하지만 ECMAScript에 정의된 내용뿐만 아니라, **다른 부가적인 기능도 포함되어 있다.**

🔗 https://www.codeit.kr/learn/4477
</div>  </details>

## 모던 자바스크립트

모던 자바스크립트란? 여러 버전의 자바스크립트 중 현재에 사용하기 적합한 범위 내에서 최신 버전의 표준을 준수하는 자바스크립트를 의미한다.

-   [ECMA-International 공식 ECMA-262문서](https://www.ecma-international.org/publications/standards/Ecma-262.htm)
- [버전 별 문법 지원 여부 확인을 위한 사이트](https://caniuse.com/)

## 🧩데이터 타입의 특징과 종류

JS는 데이터 타입이 상황에 따라 변할 수 있다는 점에 유의해야한다.

|기본형 (primitive Type)|참조형(Reference Type)  |
|--|--|
| Number, String, Boolean , Null, Undefined, Symbol(+ES2015), BigInt(+ES2015) |  Object|

### Symbol
Symbol 함수를 통해 심볼값을 만들고 있으며 Symbol 값을 담게 된 user라는 이름의 변수는 **다른 어떤 값과 비교해도 true가 될 수 없는 고유한 변수**가 된다.

```js
const user = Symbol('this is a user');

user === 'this is user'; // false
user === 'user'; // false
user === 'Symbol'; // false
user === true; // false
user === false; // false
user === 123; // false
user === 0; // false
user === null; // false
user === undefined; // false
...
// 같은 설명의 심볼도 값 비교시 false가 반환된다.
const symbolB = Symbol('this is Symbol'); console.log(symbolA === symbolB); // false
```
### BigInt
자바스크립트의 숫자는 number 자료형을 사용한다.  이는 [IEEE 754](https://ko.wikipedia.org/wiki/IEEE_754)에 기술된 [배정밀도 부동소수점 형식 숫자체계](https://ko.wikipedia.org/wiki/%EB%B6%80%EB%8F%99%EC%86%8C%EC%88%98%EC%A0%90)를 사용하기 때문이며 **자바스크립트의 숫자형(number type) 값에는 9000조 정도의 정수 표현의 한계가 존재**한다는 점에 유의해야한다.

**BigInt**는 이보다 큰 수를 표현하기위해 만들어진 타입이다.
BigInt 타입의 값은 정수 마지막에 알파벳 n을 붙이거나 BinInt 함수를 사용하면된다.
```js
console.log(9007199254740993n); //9007199254740993n  console.log(BigInt(9007199254740993)); //9007199254740993
```
단,  BigInt 타입은 말 그대로 큰 정수를 표현하기 위한 데이터 타입이기 때문에 소수 표현에는 사용할 수가 없다.

또한, BigInt 타입끼리만 연산할 수 있고, 서로 다른 타입끼리의 연산은 명시적으로 타입 변환을 해야 한다.

## 🪄 typeof 연산자
`typeof` 연산자는 키워드 다음에 공백(띄어쓰기)을 두고 값을 작성해도 되고, 함수를 사용하듯 괄호로 감싸서 사용할 수도 있다.
```js
typeof 'Codeit'; // string
typeof Symbol(); // symbol
typeof {}; // object
typeof []; // object
typeof true; // boolean
typeof(false); // boolean
typeof(123); // number
typeof(NaN); // number
typeof(456n); // bigint
typeof(undefined); // undefined
```

### 🤔null이 object라고..? function 이 object가 아니라고..?
`typeof null`을 하면 문자열 `null`이 리턴되는 게 아니라 문자열 **object**가 리턴되며 함수에서는 `typeof` 연산자를 사용하면 **function**이라는 값을 리턴한다.

자바스크립트에서 함수는 객체로 취급하지만 `typeof` 연산자를 함수에 사용하면 **function**이 리턴된다.
```js
typeof  null; // object

function sayHi() {
  console.log('Hi!?');
}

typeof sayHi; // function
```
	
## 👩‍💻Boolean

Boolean 타입이 아닌 값이 조건 식에 들어가면 형변환이 일어난다.

|Falsy 값|Truthy 값  |
|--|--|
| false , null, NaN, Undefined, 0, ''(빈 문자열) |  나머지, [] , {} |

## 📌 Other & Reference

[# 06. 불린인 듯 불린 아닌 불린같은 값 익히기](https://www.codeit.kr/learn/4483)

[ECMAScript](https://developer.mozilla.org/ko/docs/Glossary/ECMAScript)
