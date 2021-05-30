![javasciprt](https://img.shields.io/badge/javascript-up%20to%20date-yellow)
![typescript](https://img.shields.io/badge/typescript-up%20to%20date-blue)

## **React-router-dom**

<br>

react router dom 은 리엑트로 개발을 할 때에 페이지 이동을 관리하는데 아주 좋은 수단입니다.

근데 사용을 하면서 이게 어떤 원리로 진행이 되는지를 궁금하여 이것저것 찾아보다가 발견한 내용을 제 나름대로 이해하고자 해석하여 개인적 의견으로 내놓은 것을 적어둡니다.

---

## 1. BrowserRouter

react-router-dom 은 크게 2개의 라우터 형태가 있는데 그것은

> 1.browserRouter

> 2.hashRouter

입니다.

react-router-dom 을 이용하면서 가장 최상단에 있는 컴포넌트로, 외부로부터 window.history 객체를 받아와 그것을 자식에게 전달하는 역할을 합니다.

(~~사용하면서느끼지만 루트의 구분을 '/' 로 하느냐, '#' 로 하느냐의 차이도 만들어주기도 합니다~~)

구조가 절대 똑같지 않겠지만 제 나름대로 정리와 이해를 위해 이렇게 씁니다.

```js
//somewhere.js
//해당 메소드로 만들어진 히스토리 객체는
// 각종 API 들이 담겨있는, 참조값이 변하지 않을 객체

export const history = windowHistory.createBrowserHistory();

export function BrowserRouter(children) {
  return <Router history={history} children={children} />;
}
```

```js
//import history
//import RouterContext

export function Router (history,children){
    //BrowserRouter로부터 받아오는 history의 location을 지역상태로 저장.
    const [location,setLocation] = useState(history.location);

    const match = {
       location 정보를 기반으로
       path : "ex) /test/:uid",
       url : "ex) /test/exactId ",
       isExact : boolean, //완전매칭파악
       params : {uid:"exactId"}
    }

    //해당 정보를 context API 로 자식에게 전달
    const context = {
        history,
        location,
        match
    }

   //location 변동시 이를 감지하고 재할당
   history.listen((changedLocation) =>
   setLocation(changedLocation));

    //provider은 가지고 있는 value prop을 상위 RouterContext에 저장하는 것으로 보인다.

    //이에 따라 저장된 context 정보를 consumer의 prop으로 전달하여 이용할 수 있도록 만든다.

    //
   return <RouterContext.Provider value={context}>{children}</RouterContext.Provider>
```

```js
/* RouterContext = {
    ...,
    Provider : (value) =>{ 'return something'}
    Consumer : (value) =>{'return something'}
} */

export const RouterContext = React.createContext(defaultValue);

//RouterContext.Provider 의 자식 컴포넌트로 또다른 context를 만들어 전달한다. 이때 이 컴포넌트에 value로는 window.createHistory()로 만들어졌던 기본 객체를 참조하며, 자식으로는 BrowserRouter이 갖고 있던 children 정보를 확인한다.
export const RouterHistoryContext = react.createContext(defaultValue);
```

```js
/* RouterContext = {
    ...,
    Provider : (value) => {},
    Consumber : (value) =>{}
} */

//Switch
function Switch (children) {
    return <RouterContext.Consumer>
        {value => 해당 value는 router에서 전달받는 context값으로서, 이 안의 location 정보와 부모 Switch 컴포넌트가 받는 자식들이 보유하고 있는 props 안의 path 정보를 비교하여 매치되는 컴포넌트를 리턴한다.
        이 때, 해당 컴포넌트의 props에다가 computedMatch를 전달한다.
        computedMatch는 각종 context의 정보들이 담겨 있다.
        }
    </RouterContext.Consumer>
}
// Route 은 prop에 path와 component를 기본으로 받으며,
// 해당 path prop에 지정된 값과,
//consumer이 상위에서부터 받아들이고 있는 context 내부에 설정된 path와 일치할 경우 component로 받은 값을 리턴한다.
function Route (path,component,만약 switch에서 전달받았을 경우 locaion, computedMatch){
    return <RouterContext.Consumer>{value => 만약 path와 consumer이 보유한 value 즉 context의 path 정보가 일치하면 component를 반환하는 식이 들어감. 해당 컴포넌트는 match,location,history 객체를 prop으로 들고간다.}</RouterContext.Consumer>
}

function Link ({textContent}) {
    const blockedNavigationAnchor = (e,value)=>{
        e.preventDefault();
        value 즉 context의 정보 내에 있는 history 객체의 내용을 이용해 history.push('url') 로 정보를 바꾼다.


    }

    return <RouterContext.Consumer>
        {value => <a onClick={(e) => blockedNavigationAnchor(e,value)}>{textContent}</a>}
    </RouterContext.Consumer>
    }
    return
}
```
