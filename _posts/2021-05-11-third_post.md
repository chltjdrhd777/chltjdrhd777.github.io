# 세번째 포스팅

![javasciprt](https://img.shields.io/badge/javascript-up%20to%20date-yellow)

---

## Closure

일반적으로 Clousre이라 함은 함수 내부에 또다른 함수가 존재하는 형태를 말합니다.  
이런 형태가 나오게 된 것은 javascript의 혼돈의 카오스적인 시기를 반영한 이유가 큽니다. 예를 들면

```javascript
function test(hello) {
  return "hi";
}

function dontTouchMe(really) {
  return "dont touch";
}
```

요로코롬 전역적으로 선언한 함수 두개가 있다고 가정합시다.

우리는 test 함수에는 누구든 접근하고 싶지만 dontTouchMe 함수에는 함수로 손대지 못하게 하고 싶습니다.

근데, 저렇게 전역적으로 선언해버리면 저 함수가 아무리 "날 손대는 것, 멈춰!" 라고 해봐야 아주 쉽게 만지작 할 수 있게 되는 겁니다.

하지만 만약

```javascript
function test(hello) {
  function dontTouchMe(really) {
    return "dont touch";
  }

  return "hi";
}
```

와 같은 케이스는 어떨까요?

전역적으로 설정되어 있는 `test` 함수에는 누구든지 접근을 할 수 있지만, 그 내부에 있는 clousure "dont touch me"에는 test 함수를 실행시키기 전까진 접근을 할 수 있는 방법이 없습니다.

> 이것이 바로 closure의 위대한 힘입니다(두둥!)

물론 최근에는 문법적 설탕이라고 할 수 있는 **class**가 prototype이나, closure 뿐만 아니라,  
Readonly, static, public 등과 같은 강력하고 편리한 기능들을 갖추게 되면서 클래스를 사용하는 경우가 많아졌습니다.
