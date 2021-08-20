# ** 진짜 this는 해악이다... **

![javasciprt](https://img.shields.io/badge/javascript-up%20to%20date-yellow)

![typescript](https://img.shields.io/badge/typescript-up%20to%20date-blue)

---

```js
class BlurExample extends React.Component {
  constructor(props) {
    super(props);

    this.state = { isOpen: false };
    this.timeOutId = null;

    this.onClickHandler = this.onClickHandler.bind(this);
    this.onBlurHandler = this.onBlurHandler.bind(this);
    this.onFocusHandler = this.onFocusHandler.bind(this);
  }

  onClickHandler() {
    this.setState((currentState) => ({
      isOpen: !currentState.isOpen,
    }));
  }

  onBlurHandler() {
    this.timeOutId = setTimeout(() => {
      this.setState({
        isOpen: false,
      });
    });
  }

  onFocusHandler() {
    clearTimeout(this.timeOutId);
  }

  render() {
    return (
      <div onBlur={this.onBlurHandler} onFocus={this.onFocusHandler}>
        <button
          onClick={this.onClickHandler}
          aria-haspopup="true"
          aria-expanded={this.state.isOpen}
        >
          Select an option
        </button>
        {this.state.isOpen && (
          <ul>
            <li>Option 1</li>
            <li>Option 2</li>
            <li>Option 3</li>
          </ul>
        )}
      </div>
    );
  }
}
```

위 내용을 pure javascript 입장에서 분석해보자

1. class는 함수이자 객체이다

2. 함수는 코드가 평가되는 컴파일 순간에 이미 식별자를 통한 변수 설정 후에 함수 객체를 만든다

3. 함수 객체는 만들어지면서 그 내부의 암묵적인 프로퍼티로 prototype을 형성하고, 여기에 내용물들을 담는다. 물론 이 시점에 스코프도 만들어져서 [[scope]] 슬롯 안에 담겨있다.
   즉, extends 문법은 prototype 프로퍼티 안의 [[prototype]] 슬롯에 상속받는 대상을 갈아끼워넣는 작업니다.
   (constructor : class 그 자체, methods, render, [[prototype]] : React.component);

4. 해당 컴포넌트가 실행되어 생성될 때에, new 를 통해 빈 객체 인스턴스가 만들어지고 그 주소와 매개변수들이 props에 담겨 전달이 된다.

5. 빈 객체에 [[prototype]] 슬롯에 해당 함수객체의 prototype 프로퍼티가 전달되고, 그 안에 있는 contructor이 호출된다.

6. super은 프로토타입 슬롯에 저장되어있던 함수객체 React.Component를 의미하고, 그 안에 존재하는 this.setState와 같은 내용물들을 그대로 this에 바인딩했던 빈 객체에 집어넣는다.

> 그런데 이 때에, this.onClickHandler = this.onclickHandler.bind(this)를 주목하자
> 여기서 앞쪽의 this는 해당 생성된 인스턴스 객체를 뜻한다. 하지만 할당문 뒤에 있는 this는 class 함수객체를 지칭하고 있고, bind 안에 있는 this는 인스턴스를 칭하고 있다.
> 즉, 인스턴스.onclickHanlder = 클래스.onClickHanlder.bind(인스턴스) 가 되는 것이다.

> 이렇게 바인딩을 하는 이유는, 일반 메소드들은 호출 당시의 환경에서 this가 결정되기 때문이다
> 예를들어, 맨 마지막에 render 메소드에서 리턴되는 값을 살펴보면 마치 클로져처럼 함수의 외부에서 값을 가져오는 것을 볼 수 있다. 여기서 <div onBlur={this.onBlurHandler}> 을 확인해보면,
> div는 어떻게 보면 HtmlDivElement 객체를 상속받아 만들어진 객체다. 저 onBlur는 프로퍼티라고 봐도 무방한데, 이 프로퍼티를 찾기 위해서 자바스크립트 엔진은 프로토타입 체인을 휘져어가며 대상 프로퍼티가 어딨는지를 확인한다. 체인을 따라 깊숙하게 나아가면 EventTarget 이라는 프로토타입 객체의 안에 해당 내용이 존재하고, 이것이 비어있는 상태인데 그것을 this.onBlurHandler을 할당하는 것이라고 보면 된다.
> 자, 그렇다면 결국 div에서 onBlur가 호출된다면 this.onBlurHandler 입장에서 this는 호출을 한 당사자가 되므로 div 노드객체가 될 것이다. 그런데 this.onBlurHandler 안에서 참조하고 있는 this.timneOutId 와 같은 경우, 여기서 this는 div 객체가 되는데 그 객체에 저런 커스터마이징된 프로퍼티따위는 존재하지 않는다. 따라서 undefined 가 되버리는 현상이 일어난다.

> 하지만, 기존에 this.onBlurHanlder.bind(this)를 해두었기 때문에, 해당 함수가 호출이 되면서 this를 바인딩할 시점에 this는 이미 설정되어 있던 this, 즉 인스턴스 그 자체가 되므로 필요한 값을 찾을 수 있게 되는 것이다.

1. 나머지가 다 끝나면, 그 객체의 [[prototype]]슬롯에 아까 클레스에서 받아왔던 프로토타입 프로퍼티를 집어넣는다. 이 프로퍼티의 [[prototype]] 슬롯 안에 존재하는 것이 React.Component이므로 프로토타입 체인을 통한 상속이 이루어지고 있다.
