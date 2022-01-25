---
title: "Javascript의 동작 방식"
date: 2022-01-19T11:56:06Z
categories:
  - javascript
tags:
  - javascript
---

현재 자바스크립트는 클라이언트용 웹 페이지를 벗어나 백 엔드 서버, 모바일 애플리케이션 등 다양한 곳에서 활용 되고 있다.
[Githut](https://githut.info/) 통계에 따르면, 자바스크립트는 전셰게에서 가장 많이 사용되는 언어중 하나이며, 생태계 또한 급속도로 발전하고 있다.

자바스크립트에 대한 의존도는 점점 더 커질것이고, 이에 따라 개발자는 자바스크립트 언어와 생태계에가 제공하는 것에 대한 더 깊은 이해가 필요하다.

하지만, 자바스크립트를 많이 사용함에도 불구하고 이와 관련된 내용을 모르는 개발자들이 많다.

# 개요

아마 자바스크립트 개발자라면 V8, 단일 스레드, Callback queue 라는 단어를 들어보았을 것이다..

이번 포스트에서는 이 개념들에 대해 자세히 알아보고, 실제 자바스크립트가 어떻게 동작하는지에 대해 설명하고자 한다. 이러한 내용들을 이해하게 되면 조금 더 나이스하게 non-blocking 애플리케이션을 더 나이스하게 만들 수 있다.


# 자바스크립트 엔진

자바스크립트 엔진은 자바스크립트 파일을 기계어로 변환해주는 역할을 한다.

가장 대표적인 자바스크립트 엔진은 구글의 V8이다. V8은 Chrome 브라우저와 Node.js 에 사용된다.

자바스크립트 엔진은 두가지 중요한 요소를 포함하고 있다.

- Memory Heap : 메모리 할당이 이루어지는 영역
- Call Stack : 코드가 실행될 때 실행중인 함수(루틴)에 대한 정보를 저장하는 영역

![engine](https://miro.medium.com/max/2400/1*OnH_DlbNAPvB9KLxUCyMsA.png)

# 런타임

백엔드 개발자라면 node.js 가 될 것이고, 프론트엔드 개발자라면 웹 브라우저가 될 것이다. 이들은 엔진 바깥에서 자바스크립트 실행에 관여하며, 자바스크립트 개발자들을 위한 API(ex: setInterval)들과 **이벤트 루프**, **콜백 큐**를 가지고 있다.

![runtime](https://miro.medium.com/max/700/1*4lHHyfEhVB0LnQ3HlhSs8g.png)

# 콜스택

자바스크립트는 기본적으로 단일 스레드 기반이다. 이는 즉 한 번에 하나의 작업만을 처리 할수 있다.

콜스택은 기본적으로 실행중인 위치에 대한 정보를 담고 있다. 함수를 실행하게 되면, 해당 함수는 콜 스택의 최 상단에 위치하게 된다. 함수가 종료될 때(return 실행), 함수는 콜스택에서 제거된다.

예를 들어, 아래의 코드를 실행한다고 가정을 해보자.

```javascript
function multiply(x, y) {
  return x* y;
}

function printSquare(x) {
  const s = multiply(x, x);
  console.log(s);
}

printSquare(5);
```

처음 엔진이 이 코드를 실행하는 시점에는 콜 스택이 비어있을것이다. 하지만 코드가 실행되면서 콜스택은 아래와 같이 변하게 된다.


![callstack](https://joshua1988.github.io/images/posts/web/translation/how-js-works/call-stack.png)

기본적으로 단일 스레드 기반의 프로그래밍은 멀티 스레드에서 발생할 수 있는 복잡한 문제들을 고민하지 않아도 되기 때문에 난이도가 쉽다. 
하지만 단일 스레드 기반의 프로그래밍도 고민해야 할 몇가지 사항들이 있다.

만약에, 콜 스택안에 처리시간이 긴 함수가 있다고 가정을 해보자.
프론트엔드라면 웹 브라우저가 아무런 작업도 못하고 대기하는 상태가 될것이며, 백엔드라면 다른 클라이언트들의 요청을 처리하지 못하는 상태가 될 것이다.

# 콜백 큐

위의 상황 때문에 자바스크립트의 런타임에서 제공하는 API들은 비동기 실행을 지원한다. 기본적으로 대부분 API들은 Callback 함수를 인자로 받는다. API들은 실행후 인자로 받은 callback 함수를 콜백 큐에 넣는다.

**이벤트 루프** 는 Callstack이 주기적으로 확인한다. 만약에 callstack이 비어있으면, 이벤트 큐에서 callback 함수를 꺼내어 callstack에 넣는다.

## 예제

```javascript
const foo = () => console.log('First');
const bar = () => setTimeout(() => console.log('Second'), 500);
const baz = () => console.log('Third');

bar();
foo();
baz();
```

![callbackqueue](https://jwlee.dev/static/gifs/event-loop-6.gif?t=1628878628712) 