# ** 과연 나는 arguments, 렉시컬 스코프, 클로져를 정확히 이해했다고 할 수 있을까**

![javasciprt](https://img.shields.io/badge/javascript-up%20to%20date-yellow)

![typescript](https://img.shields.io/badge/typescript-up%20to%20date-blue)

---

의미적으로 알고있다고 생각했는데, 활용하는 방식을 보고 나는 털끝도 몰랐다는 사실을 깨닫고 다시 정리한다.

## 1. once 구현

```js
//테스트쪽 코드
let num = 0;
const increment = _.once(function () {
  num++;
});
increment();
increment();
increment();
expect(num).to.equal(1);

const once = function (func) {
  let alreadyUsed = false;
  let result;

  return function () {
    if (!alreadyUsed) {
      result = func(...arguments);
      alreadyUsed = true;
    }
    return result;
  };
};
```

해당 내용을 보면, increment라는 변수식별자를 위한 메모리를 설정하고(const) 거기에 할당 연산자를 통해서 함수의 리턴값을 주고있다. 함수 once의 리턴값은 "함수" 인데, 이 함수는 외부의 렉시컬 환경을 기억하고 있다(let alreadyUsed와 let result를 기억한다). 또한 변수로 들어오는 순수함수(의사함수) 를 그 내부에서 활용한다.

이게 바로 클로져의 활용법이다. 기억하자.

또한 하나 추가 더.

클로져는 평가 당시 자신이 "정의된" 환경의 렉시컬 스코프를 기억한다.

위에 once 상수를 보자. 함수를 값으로 갖는데, 이 함수는 파라메터에 함수를 받는다. 이 파라메터는 함수 내부에서 마치 변수와 같이 정의되면서 그 값에 할당을 함수로 하고있는데,

이 말인 즉슨 once 함수에 매개변수로 들어오는 함수는 once 함수의 스코프를 렉시컬 스코프로 인지한다는 소리다.

따라서, 만약에 이 once 함수의 내에 우리가 렉시컬 환경변수로 사용하고 싶은 내용들을 저장하는 게 아니라, 느닷없이 리턴되는 함수의 내부에다가 정의를 했을 경우, 매개변수로 들어오는 함수는 그 값을 이용하지 못한다(중요 1만배)

왜냐하면, 만약 리턴된 함수의 안에다가 변수를 설정해놨을 경우, 이 함수는 호출이 될 떄에 이미 그 내부에다가 정의되어있는 환경을 그대로 이용하여 리턴값을 내보낸다. 즉 변동이 없이 정적이다.

따라서, 만약 전달하는 인자들의 값이 달라진다면,
arguments 객체로 전달되는 값 또한 바뀌게 되고, 그 바뀐 값에 따라 결과값이 계속 바뀌어버린다. 우리가 원하는 것은 바뀌지 않는 것인데, 그렇다면 그것을 체킹할 특별한 변수가 필요했고 (alreadyUsed) 이것에 할당된 값은 계속되서 기록되어야 함에도 불구하고 리턴되는 함수의 내부 스코프 안에 정의해버린다면 그것은 계속 고정값으로 존재하다가 들어오는 arguments 객체의 변화에 따라 그 변화를 반영하여 그 순간에 그 변화를 저장할 뿐, 기존의 변화를 저장하지 못하는 것이다.
