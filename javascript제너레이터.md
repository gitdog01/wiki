스트림의 대해서 공부해보고 이것을 코드에 녹이고 싶었다.

# 일단 스트림이란 ?

컴퓨터 처리 환경에서 **스트림**(stream)은 시간이 지남에 따라 사용할 수 있게 되는 일련의 데이터 요소를 가리키는 수많은 방식에서 쓰인다.

- [C 프로그래밍 언어](https://ko.wikipedia.org/wiki/C_%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D_%EC%96%B8%EC%96%B4)에 기반을 둔 [유닉스](https://ko.wikipedia.org/wiki/%EC%9C%A0%EB%8B%89%EC%8A%A4) 관련 시스템에서 스트림은 개별 바이트나 문자열인 데이터의 원천이다. 스트림들은 파일을 읽거나 쓸 때, 네트워크 소켓을 거쳐 통신할 때 쓰이는 추상적인 개념이다. [표준 스트림](https://ko.wikipedia.org/wiki/%ED%91%9C%EC%A4%80_%EC%8A%A4%ED%8A%B8%EB%A6%BC)들은 모든 프로그램에 이용할 수 있는 세 개의 스트림을 말한다.
- [파이프라인](https://ko.wikipedia.org/wiki/%ED%8C%8C%EC%9D%B4%ED%94%84%EB%9D%BC%EC%9D%B8_(%EC%BB%B4%ED%93%A8%ED%8C%85))은 장치에 삽입된 제한이 없는 정보뿐 아니라 스트림으로 이해할 수 있다.
- [스킴 프로그래밍 언어](https://ko.wikipedia.org/wiki/%EC%8A%A4%ED%82%B4_(%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D_%EC%96%B8%EC%96%B4)) 등에서 스트림은 [느긋하게](https://ko.wikipedia.org/wiki/%EB%8A%90%EA%B8%8B%ED%95%9C_%EA%B3%84%EC%82%B0%EB%B2%95) 계산하거나 지연 처리된 일련의 데이터 요소를 말한다. 스트림은 리스트와 유사하게 사용되지만 나중에 이 요소들은 필요할 때에만 계산한다. 그러므로 스트림은 무한 [수열](https://ko.wikipedia.org/wiki/%EC%88%98%EC%97%B4)과 [급수](https://ko.wikipedia.org/wiki/%EA%B8%89%EC%88%98)를 대표할 수 있다.[[1]](https://ko.wikipedia.org/wiki/%EC%8A%A4%ED%8A%B8%EB%A6%BC_(%EC%BB%B4%ED%93%A8%ED%8C%85)#cite_note-1)
- [스트림 프로세싱](https://ko.wikipedia.org/wiki/%EC%8A%A4%ED%8A%B8%EB%A6%BC_%ED%94%84%EB%A1%9C%EC%84%B8%EC%8B%B1) - [병렬 컴퓨팅](https://ko.wikipedia.org/wiki/%EB%B3%91%EB%A0%AC_%EC%BB%B4%ED%93%A8%ED%8C%85)에서, 특히 그래픽 처리에서 스트림이라는 용어는 [소프트웨어](https://ko.wikipedia.org/wiki/%EC%86%8C%ED%94%84%ED%8A%B8%EC%9B%A8%EC%96%B4)뿐 아니라 [하드웨어](https://ko.wikipedia.org/wiki/%ED%95%98%EB%93%9C%EC%9B%A8%EC%96%B4)에도 적용된다.

출처 : [https://uxicode.tistory.com/entry/스트림-stream-이란](https://uxicode.tistory.com/entry/%EC%8A%A4%ED%8A%B8%EB%A6%BC-stream-%EC%9D%B4%EB%9E%80)

# JavaScript 에서 스트림 구현

일반 함수는 하나의 값(혹은 0개의 값)만을 반환합니다.

하지만 제너레이터(generator)를 사용하면 여러 개의 값을 필요에 따라 하나씩 반환(yield)할 수 있습니다. 제너레이터와 이터러블 객체를 함께 사용하면 손쉽게 데이터 스트림을 만들 수 있습니다.

```jsx
function* generateSequence() {
  yield 1;
  yield 2;
  return 3;
}

// '제너레이터 함수'는 '제너레이터 객체'를 생성합니다.
let generator = generateSequence();
alert(generator); // [object Generator]
```

### 제너레이터의 prototype

**Generator.prototype.next(value)**

- { done , value } 로 반환함
    - done 은 boolean 으로 제너레이터가 return 할때 true 반환
    - value 는 해당 값
- 파라매터로 넘긴 **value** 는 yield 의 식의 값이 됨.

```jsx
function* gen() {
  while (true) {
    let value = yield null;
    console.log(value);
  }
}

const g = gen();
g.next(1);
// "{ value: null, done: false }"
g.next(2);
// 2
// "{ value: null, done: false }"
```

<aside>
💡 **참고:**
 제너레이터가 처음에는 아무것도 생성하지 않았기 때문에 첫 번째 호출에서 기록되는 것은 없습니다.

</aside>

**Generator.prototype.return()**

- { done , value } 로 반환함
    - 제너레이터 함수의 제어 흐름이 끝에 도달한 경우 `true`입니다.
    - 제너레이터 함수의 제어 흐름이 끝에 도달하지 않고 더 많은 값을 생성할 수 있는 경우 `false`입니다. 이는 `return`이 `[try...finally](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Statements/try...catch#the_finally-block)`에서 실행되었고 `finally` 블록에는 더 많은 `yield` 식이 있을때만 발생할 수 있습니다.

```jsx
function* gen() {
  yield 1;
  yield 2;
  yield 3;
}

const g = gen();
g.next(); // { value: 1, done: false }
g.next(); // { value: 2, done: false }
g.next(); // { value: 3, done: false }
g.next(); // { value: undefined, done: true }
g.return(); // { value: undefined, done: true }
g.return(1); // { value: 1, done: true }
```

**Generator.prototype.throw()**

- { done , value } 로 반환함
    - return 의 catch 판
- `throw()`
 메소드는 호출 될 때, 이는 현재 중단 된 위치의 제너레이터에 삽입된 `throw exception;`
 문 처럼 보일 수 있습니다. `exception`은 `throw()`메서드에 전달 된 예외입니다. 따라서 일반적인 흐름에서 `throw(exception)`을 호출하면 제너레이터가 throw됩니다. 그러나 yield 식이 `try...catch`블록으로 감싸졌 다면, 오류를 포착할 수 있으며 제어 흐름은 오류 처리 후 재개하거나 정상적으로 종료 하도록 진행됩니다.

```jsx
function* gen() {
  while (true) {
    try {
      yield 42;
    } catch (e) {
      console.log('Error caught!');
    }
  }
}

const g = gen();
g.next();
// { value: 42, done: false }
g.throw(new Error('Something went wrong'));
// "Error caught!"
// { value: 42, done: false }
```

# 예시 문제를 풀어볼까 ?

[https://inflearn-quiz.vercel.app/javascript/19-generator-1](https://inflearn-quiz.vercel.app/javascript/19-generator-1)

[https://inflearn-quiz.vercel.app/javascript/20-generator-2](https://inflearn-quiz.vercel.app/javascript/20-generator-2)
