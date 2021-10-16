# **async await이 비동기 처리의 스니펫처럼 생각했던 나에게**

![javasciprt](https://img.shields.io/badge/javascript-up%20to%20date-yellow)

![typescript](https://img.shields.io/badge/typescript-up%20to%20date-blue)

---

# 착각은 유분수

그동안 나는 Promise로 then으로 연결하여 처리되는 것을 간단하게 async, await을 이용하면 깔끔정리가 가능하다

라는 정도의 안일한 생각을 가지고 사용하고 있었었다.

하지만, 그 믿음이 완벽하게 깨지는 상황이 발생하게 되면서 async와 await, 더 나아가 에러처리에 대한 이해가 필요하다는 판단이 들었고

어제 정리한 에러처리와 더불어 오늘은 try catch와 함께 async await이 어떻게 동작하게 되는지를 연구한 내용을 적어보려고 한다

모든것은 코드 위주의 정리가 될 것 같다

---

## Round 0

우선 기본 베이스로 정리해두고 갈 내용은 이렇다

- async : 함수 내에서 무엇이 리턴되든, 해당 내용을 promise 객체로 변환시켜 리턴한다.
- await : 함수 내에서 해당 코드의 마이크로테스크 큐가 완료될때까지 모든 함수블록 내의 코드는 스탑된다.
- try...catch : try 블록 안에 있는 내용을 실행한다. 만약 그 내용 가운데 실패가 되어 Error 객체를 throw 할 시, 이것을 catch블록으로 넘기면서 인자로 그 에러객체를 전달한다.

자, 이렇게만 생각하면 별것아닌 내용같아보인다.
하지만 try... catch와 async await이 결합되면 생각보다 상당히 복잡한 상황이 발생한다

---

## Round 1

간단하게 만들어본 해당 케이스로 4가지의 경우의 수를 확인해보자

```js
async function test() {
  try {
    let result = "unchanged";

    let test = new Promise((resolve, reject) => {
      return resolve("changed");
    }).then((res) => (result = res));

    console.log(test, result);

    return result;
  } catch (e) {
    console.log("ERRER!");
  }
}

console.log(test());
```

자바스크립트 엔진은 test 함수를 먼저 함수객체화하고, 스코프 체인에 따라 console 객체를 글로벌에서 찾은 후, 거기 안에서 프로토타입 체인에 따라 log 메소드를 확인하고 호출을 할 것이다.

그 후 콜스텍에는 인자로 들어오는 테스트 함수를 실행하며 콜스텍에 넣는다.

test 함수는 현재 aysnc가 달려있는 상태다.
자바스크립트 엔진은(지금 환경은 v8) 이제 이 함수 내부에서 무엇이 리턴되든, 그것은 프로미스 객체화하여 리턴해야된다고 인지하고 있다.

블록 안에서 try 구문을 확인한 엔진은 이제 그 내용대로 힙에 저장할 실행 컨텍스트를 만든다. 변수의 초기화 등등등을 한 후 이제 런타임에서 할당과 실행을 시작하는 부분들 중, 비동기 부분을 보도록 하자

```js
let test = new Promise((resolve, reject) => {
  return resolve("changed");
}).then((res) => (result = res));
```

해당 부분은 런타임에서 자바스크립트 엔진이 비동기적으로 처리되야 할 함수호출임을 인지하고 있다. 따라서 해당 내용은 바로 테스크 큐에 전달이 된다.

테스크 큐에 전달된 프라미스 함수의 호출은 콜백을 확인하여 해당 내용을 등록한다. 그런데 그 후, 체이닝으로 연결된 then 메소드 호출을 발견하고 이것을 마이크로 테스크 큐에 저장한다

자, 비동기는 동기적인 부분이 처리가 끝나 콜스텍이 비워지기 전까지는 실행되지 않으므로 대기상태가 된다

```js
console.log(test, result);

return result;
```

그 후, 다시 스코프 체인에 따라 콘솔을 찾고, 로그를 호출한 후, test와 result의 내용을 로그로 찍는다.

그리고 그 후에 result를 리턴하는데, 이때 return되는 결과값은 aysnc에 의하여 프로미스 객체화하여 리턴되게 된다.

따라서 콘솔에 찍히는 값과 result는 이렇다

```js
async function test() {
  try {
    let result = "unchanged";

    let test = new Promise((resolve, reject) => {
      return resolve("changed");
    }).then((res) => (result = res));

    console.log(test, result);

    return result;
  } catch (e) {
    console.log("ERRER!");
  }
}

console.log(test());

/// 결과 ///
Promise { <pending> } unchanged //내부 콘솔 console.log(test, result)
Promise { 'unchanged' } //외부 콘솔 console.log(test())
```

예상했던대로, 해당 내부 순서에 따르면 test는 현재 비동기 호출이 된 상태, 즉 pending 상태의 프로미스 객체만 존재하고, ressult는 바뀌기 전인 unchanged 스트링값이 할당되어 있으므로 이것들이 나오며

리턴되는 값은 result인데, 이 값은 async에 의하여 프로미스 객체화 하기 때문에 외부에서 콘솔로 찍은 결과값은 이 'unchanged'가 [[PromiseResult]] 슬롯에 담긴 형태로 리턴되게 된다.

추가적으로 해당 결과를 통해 알수있는 내용은 비동기적인 함수가 처리가 되는 것은 콜스텍이 비워진 상태, 즉 해당 함수의 return문까지 완전히 끝난 후에 비로소 처리가 이루어진다는 것을 기억해두어야겠다.

---

## Round 2

좋다. 내부에서 비동기적인 처리는 후순위로 밀리기 때문에 해당 결과는 충분히 납득이 가능하다.

그렇다면 마법같이 생각하고 있던 await을 사용하면 어떻게될까.

위에서 정리했듯 async 안에서의 await이 달린 문은 해당 마이크로테스크 큐까지의 처리가 모두 완료되기 전까지는 함수 내부 실행 과정이 중지된다고 알고있다.

그러면

```js
async function test() {
  try {
    let result = "unchanged";

    // await만 추가
    let test = await new Promise((resolve, reject) => {
      return resolve("changed");
    }).then((res) => (result = res));

    console.log(test, result);

    return result;
  } catch (e) {
    console.log("ERRER!");
  }
}
console.log(test());
```

해당 구문은 비동기 함수 처리 앞에 await이 존재하는 상태다.

자 우리는 그렇다면 저 await 뒤에 존재하는 테스크 큐 포함 마이크로테스크 큐가 다 처리되기 전까지는 뒤에 호출이 멈춘다는 것을 이해하고 있다.

결과물은 과연 어떻게 될까

```js
Promise { <pending> } //외부 콘솔
changed changed // 내부콘솔
```

아니, 이상하지 않은가?

어째서 외부 콘솔이 먼저 찍히고 그 다음에 내부콘솔이 찍히고 있는걸까.

이게 바로 await에서 간과하고 있던 가장 큰 문제다.

정확하게 말하면, async ... await 관계를 사용하는 함수에서 리턴되는 결과값으로 뭔가를 이용하려고 할 경우에 발생하는 문제다.

아까도 말했듯, await이 존재한다면 그 뒤에 있는 문들이 다 처리가 되기 전까지는 함수의 실행 처리는 스탑된다.

근데 문제는, async가 걸려있는 함수는 그 무엇보다도 await이 걸려있는 존재를 우선시한다는점이다.

즉, 함수 내에서 return과 await이 걸려있는 문구가 동시에 존재할 경우, 자바스크립트 엔진은 해당 함수에서 await의 뒤에 있는 비동기 처리의 되기 전 형태를 우선시하여 "반 리턴" 하는 기괴한 모습을 보여준다.

여기서 반 리턴이라고 한 이유는, 일반적인 리턴은 후에 있는 처리를 다 무시하지만, await을 통한 리턴은 그냥 그 뒤의 비동기 처리전 형태가 리턴된다 할 뿐이지 뒤에 있는 구문은 리턴을 제외하고는 다 실행된다는 점이다.

따라서, console이 찍히게 되며 이 콘솔 뒤에 있는 내용은 await을 통해 이미 처리가 완료된 내용, 즉 changed가 담긴 상태로 나오게 되는 것이다

```js
changed changed // test, result
//test는 await을 통해 마이크로태스크큐까지 모두 완료되어 그 결과 resolve를 통해 처리 완료된 상태를 내부 슬롯에 등록한 Promise 객체가 할당되어 있고

// result는 마이크로테스크 큐, 즉 then 에 의해 처리된 값이 할당되어 있다.
```

따라서, 여기로부터 도출되는 너무나도 중요한 사실은,

async await으로 처리되는 결과값은 함수 내에서만 유효하고

함수 외부에서 리턴을 통해 무언가를 하려고 한다면 그것은 내가 원하던 결과값을 내지 않는다는 점을 명확하게 이해하고 가야 한다

---

## Round3

자, 그러면 이제 resolve 케이스는 확인했으니 reject 케이스도 봐야 할 것이다.

```js
async function test() {
  try {
    let result = "unchanged";

    // reject만 추가된 상태
    let test = new Promise((resolve, reject) => {
      return reject("changed");
    }).then((res) => (result = res));

    console.log(test, result);

    return result;
  } catch (e) {
    console.log("ERRER!");
  }
}
console.log(test());
```

해당 내용을 살펴보면, 프로미스는 아까처럼 태스크 큐로 넘어가고, then은 마이크로 테스크 큐로 넘어갈 것이다.

그리고 나서 콜스텍이 비워지기 전까지는 처리가 안되고 그 이후가 처리되게 되므로,

내부 콘솔이 먼저 실행되고 result가 리턴이 된 후, 비동기적인 처리가 실행되면서 reject가 처리가 되어 Error("change") 객체가 생성이 된다.

이 비동기함수는 자신의 외부 상황을 렉시컬 스코프를 통해 인지하고 있으므로 즉 try... catch 블록을 파악하고 있으므로 함수 호출이 끝난 이후애도 이 블록 안의 영역에서 돌아가고 있다.

어찌됬든, try 안에서 error 객체가 발생했고 그것이 암묵적으로 throw 되고 있으므로(reject 메소드의 호출로 인해)

catch 블록으로 넘어가 error 메세지를 콘솔로 찍게 된다.

따라서, 결과적으로 터미널에 찍히는 로그는 이와 같다.

```js
Promise { <pending> } unchanged //내부콘솔
Promise { 'unchanged' } // 외부콘솔

UnhandledPromiseRejectionWarning: change ....... //?
```

자 위에 두개는 처음에 예상했던 대로, 비동기가 처리가 되기 전 먼저 다 실행된 결과로 인해 나온 내용이다.

test는 아직 비동기가 처리가 되기전인 상태, 즉 promise 객체의 내부슬롯 [[PromiseState]]에 pending이 부여된 상태로 남아있으니 그게 찍힌것이고

result는 그냥 할당된 스트링 값이 찍혀있는 상태,

그리고 return result로 인해 나오는 'unchanged' 스트링 값이 async로 인해 프로미스 객체화 하여 리턴된 상태다.

근데 그 밑에 저 문구는 왜 뜨고 있는 것일까.

다시금, 비동기적 처리는 함수 내의 동기적인 처리가 끝나 콜스텍이 비워진 이후에 비로소 시작된다고 하였다.

비동기 처리로 인해 발생하는 것은 reject() 즉, 에러 객체가 throw가 되는 상태다.

어제 보았던 에러처리의 파이프라인에 따르면, 비동기적인 프로미스 호출이 이루어질 때 호출되는 콜백 함수의 내부에는 암묵적인 try.. catch가 발생하게 되고 여기서 에러가 throw 된다면 자바스크립트 엔진은 이와 체이닝되어 있는 마이크로테스크 큐, 즉 then, catch,finally 와 같은 메소드 호출들의 큐 상태를 확인해본다.

쭉 이어나가서 확인해봤는데도 이를 처리할 catch 메소드의 호출을 발견하지못했다면 자바스크립트 엔진은 자동으로 eventListner을 통해 'unhandledrection' 을 등록하고 자발적으로 에러를 처리하게 된다. 이떄 처리되는 에러가 바로 저 상위 문구가 되는 것이다.

---

## Round4

그렇다면 마지막으로, await을 붙이고 reject가 되면 어떻게 될까.

```js
async function test() {
  try {
    let result = "unchanged";

    let test = await new Promise((resolve, reject) => {
      return reject("changed");
    }).then((res) => (result = res));

    console.log(test, result);

    return result;
  } catch (e) {
    console.log("ERRER!");
  }
}
console.log(test());
```

아까도 말했듯, await은 정말 이상한 행동을 한다. resolve 될때에 자기 혼자서 멋대로 먼저 "반 리턴" 하던 것이 기억나는가.

여기에서도 역시나, async가 걸려있는 함수는 await 뒤의 처리를 그 무엇보다도 우선시하는 경향을 보인다. 위 내용의 결과물은 이렇다

```js
Promise { <pending> }
ERRER!
```

참 짜증나지만, await은 무조건적으로 해당 내용의 반 리턴을 우선시한다. 즉, 이미 처리가 되지도 않은 상태인 Promise객체를 먼저 리턴하고, 그 뒤에 마이크로테스크큐까지의 처리를 실행한다.

해당 내용을 보면, reject로 인해 Error("ERROR!") 객체가 만들어짐과 동시에 이것이 throw가 된다.

그러면 그 뒤에 문들은 실행되지 않고 바로 try...catch 구문에서 이를 확인하고 에러객체를 인자로 받은 후, 내부 블록을 실행하는 구조인 것이다.

---

## 5. 결과는

결과적으로 봤을 때, aysnc await은 비동기를 다루는 데에 있어서 아주 좋은 기능임은 사실이다.

하지만, 이 비동기적인 처리를 함수 내부에서만 활용하려고 생각해야지 함수 외부에서조차 활용하려고 한다면 크나큰 문제를 발생시킬 수 있으리라는 결과를 얻었다.
