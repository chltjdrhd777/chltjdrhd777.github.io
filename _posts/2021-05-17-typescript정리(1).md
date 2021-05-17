# 일곱째 포스팅

![javasciprt](https://img.shields.io/badge/javascript-up%20to%20date-yellow)
![typescript](https://img.shields.io/badge/typescript-up%20to%20date-blue)

**typescript 복습 중, 잘 모르고 있던 개념을 정리한다**

앞으로 타입스크립트를 복습할 예정이므로, 해당 포스트는 타입스크립트 시리즈로 진행을 좀 할 예정이다.  
간단하게 핵심만 써놓을거고 나중에 내가 다시 봤을 때에 틀리지 말자는 의미가 크다.

---

## 1. tuple type

```ts
const tupple: [string, number] = ["element1", 2];
```

마치 튜브처럼, 딱 저 형태만 취해라. 라는게 튜플이다

---

## 2. union

```ts
const union: string | number | "can be" = "value1";
```

선택권을 주어주고, 그 중에서 선택하는 개념이다.

---

## 3. enum

```ts
enum GreetingEnums {
  HELLO,
  HI,
}

const whatGreeting = GreetingEnums.HELLO;
```

enum은 객체와 거의 유사하다. 마치 const로 선언한 고정값들을 한데 모아서 사용하려는 느낌이라고 보는 게 좋다.  
저렇게 각자 그 자체로 value로 들어갈 수 있고, 객체처럼 key : value 느낌으로 들어갈 수도 있다.  
(enum 자체가 유사배열처럼 numbering 시스템으로 각 값을 구분짓고 있음)

하지만, 객체와 큰 차이점이라고 한다면 객체는 데이터 주소를 참조하는 것이기 때문에 값이 변해버릴 수 있음.  
고정값을 이용한다는 개념과 같이 enum은 변경되지 않는 값들을 안전하게 참조해서 쓸 수 있다는 장점

> 단, enum은 javascript로 변환될 때에 아까 말한 유사배열의 넘버링을 고려한 객체로 강제변환을 시키기 때문에 나중에 컴파일 후의 참조에 문제가 발생할 요지가 다분하므로, 이를 방지하는 **const enum** 을 사용하도록 한다.

---

## 4. literaltype

```ts
const literal: "Literal 1" | "Literal 2" = "Literal 1";
```

union에서 그 값을 포괄적인 string, number과 같은 것이 아니라 명확하게 지정해놓는 것을 의미한다.

---

## 5. Type Alias

```ts
type Listerals = "Literal 1" | "Literal 2";

const literal: Literals = "Literal 1";
```

통로(alias) 라는 의미처럼, 처렇게 바깥에 "type" 이나 "interface" 형태로 alias를 설정한 후, 이를 적용하는 것을 말한다.

일반적으로 대부분의 타입 정의는 alias형태를 자주 쓴다.
