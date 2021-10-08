# **싱글톤 패턴의 장점**

![javasciprt](https://img.shields.io/badge/javascript-up%20to%20date-yellow)

![typescript](https://img.shields.io/badge/typescript-up%20to%20date-blue)

---

오늘 프로젝트 내용을 다시 살펴보다가 누군가가 만든 구조형태에 class SingletonBase라는 패턴을 보게되었다. 당시에는 이게 무슨 소린지 이해가 안되서 그냥 넘어갔던 내용인데, 오늘 복습하다 보니 아하 포인트가 떠올라서 작성한다

# what is extends in class

클래스는 다른 클래스를 상속할 수 있는 기능인 extends라는 문을 가지고 있다. 이 extends의 기능을 정확하게 알아야만 싱글톤이 어떤 장점을 가지고 있는지를 이해할 수 있다.

일단 예시로 두가지의 자료를 만들어본다

```js
class SingletonBase {
  static instance;
  constructor() {
    if (!SingletonBase.instance) {
      SingletonBase.instance = this;
    } else {
      console.log("    already has instance.");
      console.log("    return existing instance.");
    }

    return SingletonBase.instance;
  }
}

class Derived {
  constructor() {
    super();
    this.config = {
      host: "localhost",
      user: process.env.DATABASE_USERNAME || "root",
      password: process.env.DATABASE_PASSWORD || "",
      database: process.env.DATABASE_NAME || "learnmysql",
    };
  }
}

const singleton = new Drived();
```

사실 저 간단해보이는 두가지의 형태에서 생각할 내용이 엄청나게 많다.

우선 일반적으로 new라는 문을 받으면, 자바스크립트 엔진은 메모리에 저장할 임시객체를 하나 만든다.(이 객체를 누가만드는지는 밑에서 설명한다)

그리고 나서 이 객체를 Derived 안에 전달한다.

하지만, 이 클래스는 컴파일 때에 토큰으로 분해되는 당시에 extends 문구가 확인된다. 따라서 하는 행동이 살짝 달라지므로 그것을 분석한 내용을 담아본다.

---

생성자 함수이자 객체인 class Derived는 이미 컴파일 시점에서, 즉 이 해당 클래스를 해석하여 객체로 만들 때에 이미 prototype이라는 프로퍼티를 암묵적으로 생성하고, 그 안에 contructor와 내부에 설정한 메소드들, 그리고 prototpye 슬롯에 [[Object.prototype]]라는 빌트인 객체의 프로토타입 프로퍼티를 할당함을 이해했을 것이다.

즉 간단히 말해, 저 class Derived 안에는 이미

```js
{
  prototype : {
    method1,
    method2,
    ...
    [[Object.prototype]]
  }
}
```

형태의 프로토타입 프로퍼티가 평가시점에 존재한다.

자 그럼 런타임 시점이 되어 이제 new 를통해 임시 인스턴스가 전달되었다. 그런데 여기서 중요한점은, new를 호출한 상대가 바로 Derived 클래스가 아닌 그 안에 extends 로 이미 컴파일로 토큰 파악 시점에 확인되었던 수퍼클래스인 SingleToneBase 클래스가 위임해서 실행하고 있다는 점이다.

(이것 자체는 크게 중요한 내용은 아니다.

어차피 new의 실행은 위임하고 있지만 정작 new.target이나 this를 살펴보면 그 대상은 Derived를 바라보고 있기 때문이다.
다만 처리 과정이 달라진다는 것을 이해하기 위하여 알고있어야한다.
)

자 그럼 이제 자바스크립트 엔진은 이 대상이 수퍼클래스가 존재한다는 것을 확인했으므로, constructor의 super을 실행하여 포커싱이 수퍼클래스인 싱글톤 클래스로 넘어간다

이 싱글톤 클래스는 일반 클래스때와 같이, new를 통해 임시 인스턴스를 만들었고, 이 인스턴스에 this를 바인딩한다. (실행컨텍스트와, 변수 호이스팅, [[outerReference]]를 prototype chain에 설정하는 것은 이미 했을것이고)

자 그럼 이 싱글톤 클래스 내에서 가리키는 this 는 new를 통해 만들어진 임시 인스턴스 객체이고, 싱글톤 클래스는 constructor을 통해 이 this를 초기화하는 행위를 할 것이다.

그리고 나서 이 인스턴스의 [[prototype]] 슬롯에 들어갈 내용을 결정하는데 원래대로라면 본인, 즉 SingletonBase 클래스가 평가시에 가지고 있던 SingletonBase.prototype 객체를 넣었을것이다
(이 객체는 원래 하던것처럼 constructor, 그리고 자신들의 메소드, [[Object.prototype]]) 으로 초기화된 상태일것이고말이다.)

근데, 여기서 하나의 트릭은, new.target은 Derived를 가리키고 있다는 점이다. 따라서, 해당 프로토타입 슬롯에 들어가게 되는 것은 사실 Derived.prototype이며, 더 신기한것은 이 Derived.prototype 에 원래 존재했던 프로토타입 슬롯 [[Object.prototype]]이 수퍼클래스 자신의 SingletonBase.prototype으로 교체된다.

즉 정리하자면, 현 단계까지의 인스턴스의 모습은

```js
{
  propBySingleton1, // 싱글톤 객체에서 constructor로 인해초기되는 프로퍼티를 뜻함
    propBySingleton2[[Derived.prototype]]; // 이 내부에 prototype 슬롯에는, SingletonBase.prototype이 넣어져 있다.
}
```

조금 말이 어려워졌는데, 결국 하고싶은말은 extends되는 싱글톤 클래스는 자신의 내부상황을 prototype프로퍼티에 저장한 후, 그것을 인스턴스를 초기화하면서 constructor로 내용을 담아가지고 마지막에 [[prototype]] 슬롯에다가 미리 new.target으로 연결되어서 받아온 Derived의.prototype 객체의 [[prototype]]슬롯에다가 자기 자신의 환경을 주입하여 프로토타입 체인을 완성시킨다.

그 후 이 인스턴스, this를 암묵적으로 리턴하나

싱글톤 패턴은 그 전에 static으로 해당 자기 자신의 constructor까지 초기화가 완료된 인스턴스를 할당한다.

이 행동이 싱글톤패턴에서 제일 중요한데,

```js
constructor() {
    if (!SingletonBase.instance) {
      SingletonBase.instance = this;
    } else {
      console.log("    already has instance.");
      console.log("    return existing instance.");
    }

    return SingletonBase.instance;
  }
```

class에서 static이란 컴파일 시점에 평가되면서 클래스도 함수이자 결국엔 객체라는 특성을 응용, 암묵적으로 존재하는 protype 프로퍼티처럼 끼워지는 프로퍼티라는 것을 알고 있을 것이다.(나야)

즉, static은 이미 컴파일 시점부터 존재해왔고, 이 자바스크립트 파일이 러닝이 되는게 끝나는 순간 없어지게 될 내용이다.

하지만 계속 돌아가고 있다는 가정하에, 이 static 부분에 현재 까지 초기화된 인스턴스를 넣는다는 의미는 결국,

자바스크립트 엔진에게 이렇게 고하는 것과 같다

"야, 이 싱글톤 객체, 지금까지 constructor로 초기화한 내용들 다 담고있고, 서브클래스가 평가시점에 가지고 있던 prototype 정보 갖고있고, 거기에 나 수퍼클래스의 prototype 내용도 체이닝으로 담고 있어.

그러니까 만약에 또 이 클래스로 인스턴스 하려고 하면 이 과정 또다시 하지말고, 그냥 서브클래스 constructor로 초기화하는 내용만 이어서 해.
"

라는것이다!!!!!!

이게 제일 중요한 내용이다.

싱글톤은 결국, 불필요하게 평가하고 초기화하는 일련의 과정을 싹 지우고

그 이후 저장되어 있던 인스턴스, this를 암묵적으로 리턴하는데

만약

```js
return SingletonBase.instance;
```

부분이 constructor에 없었다면, 해당 수퍼클래스는 암묵적으로 생성한 새로운 this를 리턴할것이다.

하지만, return으로 객체가 리턴된다면 이 암묵적인 this를 리턴하지 않고 해당 return문의 우측에 있는 내용을 리턴한다.

그렇다면, 우리는 이미 저장되어 있는 인스턴스를 갖고 있으므로 이것을 리턴하게 되는 것이다.

어썸하지않은가

그럼 이제 이 리턴되는 인스턴스가 서브 클래스에게 토스되고, 그 서브 클래스의 this에 바인딩된다.

그럼 이 바인딩된 this에 서브클래스의 constructor가 실행되어 나머지 부분을 초기화시키고 그 결과물인 인스턴스 this를 리턴하게 되는 것이다.

## 자 기나긴 장정이 끝났다.

## 그래서 결론은?

싱글톤 패턴을 이용하면 서브클래스에서 초기화 하는 부분만 가지고 계속해서 평가할 필요없이 기존의 인스턴스를 가지고 재활용을 할 수 있다!
