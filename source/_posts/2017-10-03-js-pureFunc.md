---
title: 순수 함수
date: 2017-10-03 22:46:56
tags: programming
thumbnail: https://upload.wikimedia.org/wikipedia/commons/thumb/3/3b/Function_machine2.svg/220px-Function_machine2.svg.png
banner: https://upload.wikimedia.org/wikipedia/commons/thumb/3/3b/Function_machine2.svg/220px-Function_machine2.svg.png
categories:
- programming
- software
---

# 순수함수란 ?

## 순수함수 (pure function)

순수 함수(pure function)는 순수 입력과 순수 출력으로 구성되어 있다. 즉 순수 함수는 부수 효과(Side-Effect)가 없는 함수이다. 사이드 이펙트가 없다면 함수는 언제, 어디서나, 몇번이든 동일한 출력을 갖는다. 즉 똑같은 입력값에 대하여 똑같은 출력값을 돌려주는 것 의외에 다른 기능은 하지 않아야 한다. 이런 개념을 참조 투명성이라고 한다.

***부수효과 (Side Effect)***
> 함수의 입력 -> 실행 -> 출력 과정에서 외부에 있는 어떠한 것에 의해 함수의 출력값이 바뀌는 것을 의미한다.

***참조 투명성 (referential transparency, RT)***
> 모든 프로그램에 대해 어떤 표현식(expression) e를 모두 그 표현식의 결과로 치환해도 프로그램에 아무 영향이 없다면 그 표현식 e는 참조에 투명하다(referentially transparent).


```javascript
var outside = 2;

var F = function (x) {
    var inside = outside + x;
    return inside;
}

F(5);
```
<!-- more -->

위의 함수는 순수 함수가 아니다. 함수의 외부스코프에있는 outside의 값이 변하면 *F(5)*의 값도 같이 변하기 때문이다.

만약 outside가 var가 아닌 const로 선언되었다면 함수 F는 순수 함수라 할 수 있다. 외부 변수인 outside는 읽기 전용이며, 값이 바뀔 염려가 없기 때문이다.


## 순수함수의 장점은 무엇인가?

1. 모듈성
로직 처리에 쓰이는 모든 변수를 파라미터로 받는다면, 함수의 모듈성은 크게 증가한다. 한 클래스의 메소드를 다른 클래스에서 다른 쪽에서 쓸 일이 생긴다면, 공통유틸에 이동하기만 하면 되기 때문이다.

2. 테스트
함수에서 로직을 처리할 때 모든 것을 this안에서 끌어다 쓴다고 생각해보라. 클래스가 커지면 전역 프로퍼티의 흐름을 알 수 없기 때문에, 디버깅이 매우 어렵다.

..


## 의문을 가져보자.

> 입력값에 영향을 준다면 그것은 순수함수라고 할 수 있나요 ??

```javascript
var test = function (string) {
    return test + 'world';
}

var string = 'hello';

console.log(test(string)); // 'hello world';
console.log(string); // 'hello'
```

```javascript
var test2 = function (array) {
    return array.push('world');
}

var array = ['hello'];
console.log(test(array).join(' ')); // 'hello world'
console.log(array.join(' ')); // 'hello world'
```

위의 함수는 함수에 파라미터로 전달한 string 변수의 값이 함수 test를 실행한 후에도 영향을 받지 않았지만, 아래 예제의 array는 입력 변수의 값이 바뀌어 버렸다.(기본적으로 자바스크립트에서 string은 불변 객체이고, array는 가변 객체이기 때문이다.) 

test2 함수는 순수 함수라고 할 수 있는가? (사내 스터디중 경력자 분께 직접 물어본 질문이다.)

-> *순수함수라 할 수 있다.*

순수 함수에서 입력값의 변화에 관한건 정의가 되어있지 않기 때문에, 입력값의 변경 여부는 순수함수와는 거리가 멀다는 답변을 받았다.

하지만 내 생각은 좀 다르다. 인풋으로 들어간 값이 불변이 아니라 가변이여서 함수를 호출할 때마다 바뀐다면, 함수를 매번 호출할때마다 값이 다르게 나올 것이다.

특히 멀티 스레드나 멀티 프로세싱과 같이 한 데이터를 동시에 병렬처리를 진행한다면, 원하지 않는 값이 매번 도출될 것이다.

그래서 파라미터로 넘겨준 값은 함수가 실행된 후에도 변하지 않아야 한다는게 내 생각이다.