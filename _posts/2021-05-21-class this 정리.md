# 일곱째 포스팅

![javasciprt](https://img.shields.io/badge/javascript-up%20to%20date-yellow)
![typescript](https://img.shields.io/badge/typescript-up%20to%20date-blue)

**자꾸 클래스형 객체에 대해 헷갈려해서 실전 핵심을 적어놓는다. 까먹지 말자 미래의 나야**

> 조만간 카테고리를 작성하는 방법을 배워서 따로 정리를 할 예정이다

---

## 1. instance vs object listeral

```ts
const objLiteral = { name: "literal" };

class MakingInstance {
  name;
  constructor(name: string) {
    this.name = name;
  }
}

const objInstance = new MakingInstance();
```

Object listeral은 항상 코딩하면서 보게 되는 이젠 익숙한 그것이다.

각 포함하는 property들은 속성값으로 writable, readable, enumerable의 숨겨진 값들을 내포하고 있으며, 이는 원시 객체형인 **Object.defineProperty(타겟, proptery 이름, {value:any, writable: boolean, readable: booelan, enumerable: boolean})** 형태로 존재한다.

Object listeral({}) 형태로 만들 경우, 각 property의 속성값들은 모두 true 상태이고, 저것을 원시 객체형에서 만들어서 조작할 수 있지만 사용 빈도는 드물다.

반면, class형 객체도 마찬가지로 객체를 new 라는 프로토타입에서부터 받아오는 고유 메소드를 통해 생성이 가능하다. literal 형태와 가장 큰 차이점이라고 한다면,

**this** 와 **재활용성** 이라 할 수 있겠다.

참고로 이해하기를, class 생성자는 설계도면, instance는 그 설계도면을 가지고 제작한 실체 라고 생각하면 된다.

---

## 2. This

이 포스팅의 주 이유이다. 지금 도대체 몇번째 까먹고 헷갈려하는지 모르겠는데, 이제 여기서 깔끔정리하고 이용하자.

```ts
let checkWindow = true

function targetWindow1 () {
    return this;
} // undefined

function targetWindow2 () {
    return this.checkWindow;
}// true

const testObj =  {
    name : 'test2';
    calling : function(){
        return this.name;
    };
}

testObj.calling() // 불려지면서 '.'으로 연결되어 있는 앞놈을 this로 딴다. 따라서 'test2'를 내뱉는다. 객체의 메소드로 불리고 있다는 의미다.

const sharing = testObj.calling;
sharing() // 이 때에는 단지 sharing 변수는 메모리에 저장되어 있는, calling이 칭하고 있는 function값을 참조할 뿐이다. 따라서 sharing은 결국 function () {return this.name}일 뿐이며 이 케이스는 역시 위와 마찬가지로 호출 시 undefined가 되는 것이다.

class Test3 {
    title3;

    constructor(calling2 : string){
        this.title3 = title3;
    }
}

const test3 = new Test3("hello3");

console.log(test3.title3); // new로 인스턴스 생성시, this는 해당 생성된 객체를 참조하는 대상이 되므로 this === test3 가 된다.


function functionVsArrow() {
    this.name = "a1"
    return {
        name : "b1",
        call : function() {
            console.log(this.name);
        },
        callArrow : () =>{
            console.log(this.name);
        }
    }
}

const finalTest = new functionVsArrow();

finalTest.call() // a1
finalTest.callArrow() // b1
```

애로우 함수는 인스턴스로 제작되기 전 기본 원형을 this로 갖고 간다.

따라서, 인스턴스로 구현하기 전 기존의 생성자 형태에서 내장하고 있던 변수들을 지칭하고 싶을 때에는 애로우 function으로 정의내리면 좋을 것 같다.

참고로, class 생성자에서 애로우 함수를 사용해봤을 때엔 원하는 대로 목적이 달성되지 않았으므로 굳이 매소드를 화살표 함수로 구현하고자 한다면, 함수형 생성자에서 쓰도록 하자. 구글링해보니 비추천이라는 말이 조금 있는 걸 보면...
