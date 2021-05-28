![javasciprt](https://img.shields.io/badge/javascript-up%20to%20date-yellow)
![typescript](https://img.shields.io/badge/typescript-up%20to%20date-blue)

## **함수의 parameter**

<br>
함수는 객체입니다. 그것도 일급 객체입니다.

1. 일급객체란 다른 객체들에게 일반적으로 적용가능한 연산을 모두 지원하는 객체를 의미하며

2. 여기서 말하는 적용가능 연산은 예를들어 변수에 할당하거나, 인자로 다른 함수를 받거나, 결과값을 리턴하는 등의 행동들을 의미합니다.

또한 함수에서 가장 큰 특징 중 하나라면, **자기 자신의 스코프를 생성** 한다는 점입니다.
(스코프와 컨텍스트에 관한 이야기는 zerocho님의 블로그를 참조하시면 큰 도움이 됩니다.)

특징으로, 매개변수(parameter)에 외부로부터 전달인자(argument)를 받아서 결과값을 리턴할 수 있는 능력이 있다는 점인데,

```js
let argument = "yes";

function test(parameter) {
  return parameter;
}

test(argument); // return값 = "yes"
```

이때 받아들이는 전달인자가 참조하는 값을 복사하여 parameter을 변수처럼 선언 후, 할당합니다.

즉, 우리가 평소에 함수 바깥에서 let name = "value" 와 같이 전역변수를 선언하고 할당해서 써왔던 것처럼,
함수는 자신 내부의 context에 마치 let parameter = argument; 와 같은 형식으로 선언을 하고 할당을 하여 사용합니다.

```js
function test(parameter) {
  //암시적으로
  //let parameter = 무엇
  //과 같음.
}
```

따라서,

```js
function test(parameter) {
  let parameter = "hi";
  return parameter;
}
```

라고 설정하면 에러메세지는 친절하게  
"this identifier has already been declared"

"늬가 설정하려는 식별자는 이미 선언되었단다"

라고 불만을 표합니다.

---

이는 class의 constructor가 this로 내부에다가 변수를 선언하는 것과 구조적으로 비슷하다는 느낌을 받으며 정리를 마칩니다.
