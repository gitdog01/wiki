# TypeScript

1. [개요](#1-개요)
2. [Group 3 에서는 ?](#2-Group-3-에서는-)
3. [출처](#3-출처)

## 1. 개요

![](https://i.imgur.com/2MdkSB9.jpg)

> typescriptlang.org 의 첫 화면

&nbsp; 위에 설명 처럼 TypeScript 를 가장 잘 설명 하는 말은 Type을 가진 JavaScript 이다. 일단 우리가 배우고 있는 JavaScript의 ES6 (ES6를 사용하는 이유에 대해서는 따로 공부할 예정이다.) 에 많은 것들을 사용한다.
&nbsp; Type이 있다는 것은 정적타입을 사용하고 , 변수나 함수의 타입을 정확히 표기함으로써 타입으로 인한 에러를 많이 줄일 수 있고, 그래서 생산성을 증가시킬 수 있다.
&nbsp;또 한 기존 JavaScript는 재사용할 수 있는 컴포넌트를 만들기 위해 함수와 프로토타입-기반 상속을 사용했지만, 객체 지향 접근 방식에 익숙한 프로그래머의 입장에서는 클래스가 함수를 상속받고 이런 클래스에서 객체가 만들어지는 것에 다소 어색함을 느낄 수 있었다고 합니다. 그래서 TypeScript는 적극적으로 JavaScript 에서 할 수 있도록 도와줍니다.

## 2. Group 3 에서는 ?

- **사용한 이유** : 우리가 TypeScript를 사용하는 가장 큰 이유는 "협업" 하는 곳에 있다. TypeScript 는 위에 개요에서 언급했듯이 우리는 많은 에러를 줄일 수 있고, 명확히 표기된 타입으로 인해 input 과 output 를 **믿을 수 있게** 된다. 협업을 할때 JavaScript에 특징인 약한 타입검사와 동적인 타입은 우리가 한 마디라도 공유를 못 한다면 (공유했지만 그것이 상대방에게 온전히 받아들여지지 않을 수도) 우리는 서로의 코드에 대해서 두 번 생각해보아야 하는 상황이 올 수도 있다. 하지만 이러한 상황을 줄일 수 있음으로써, 서로의 코드를 **믿을 수 있게**되고 더 좋은 결과물을 얻을 수 있을 것이라고 생각해서 사용하게 되었다.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![](https://i.imgur.com/gYTCoxa.jpg)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;_완전 다른 것이 섞여 들어갈 수 있다는 말입니다._
<br>

---

- **힘들었던 점 1** : 처음에 이해 못 했던 말이 있는데 지금은 이해가 되었다.

```
any 를 도배하라고 만든 언어가 아니다 !
                                     - 어떤 익명의 개발자가 나무위키에 남긴 한 마디
```

&nbsp;&nbsp;이 부분에 대해서 이야기를 하자면, 라우트를 만드는데 부분 부터 굉장히 애를 먹었다.

```javascript=
router.get('/', function(req, res, next) {
  res.send("hello world!");
});
```

&nbsp;&nbsp; 과연 `req`,`res`,`next` 의 타입은 무엇일까? 굉장히 고민을 많이 했다. 이렇듯 router 를 이용해서 굉장히 많은 것을 만들었지만, 정작 나는 내가 사용하는 함수들의 입력값도 반환값도 몰랐던 것이다. 처음에는 any,unknown 을 쓰자고 하기 시작하니 너무 모르는 것들이 많아서 any가 넘쳐나기 시작했었다. 그렇게 된다면 우린 TypeScript 에서 Type 을 잃게 되는 것이였다. 하지만 공부를 통해서 지금 위와 같은 코드는 다음과 같이 고쳐나가고 있다.

```typescript=
app.get("/", (req: express.Request, res: express.Response, next: express.NextFunction) => {
  res.send("hello world!");
});
```

---

- **힘들었던 점 2** : ESLint 를 적용하는데 생각보다 애를 먹엇다. 처음에는 TSLint 를 사용하려고 했었다. TSLint 는 ESLint 처럼 코드에 가독성 및 오류를 미리 검사하는 정적 분석 도구이다. TSLint 가 2019년 부터 지원이 끝났다는 소식을 듣고 ESLint 로 이사하면서 여러가지 문제를 만났었다.
  - config.ts 의 위치를 제대로 받아오지 못 했던 문제
  - 몇몇 .ts 파일들을 모듈에서 불러올 때 제대로 가져오지 못 하는 문제

---

- **힘들었던 점 3** : 모듈을 불러올 때 기존에 가져왔던 라이브러리들을 @types/ 로 다시 깔아야 하는 문제가 있었다.

---

- **특징을 적용하기 위해 노력한 것** : 이를 활용해서, 우리는 TypeScript 에 있는 Generics 을 이용해서 더 좋은 코드를 짯다고 느낀 부분도 있었다.

```typescript=

```

## 3. 출처

- https://www.typescriptlang.org/
- https://typescript-kr.github.io/
- https://namu.wiki/w/TypeScript
