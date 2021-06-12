# **React 구상원칙**

![javasciprt](https://img.shields.io/badge/javascript-up%20to%20date-yellow)

![typescript](https://img.shields.io/badge/typescript-up%20to%20date-blue)

---

어렴풋하게 인지하고 있던 this에 대해서 다시금 정리할 수 있었습니다.

화살표 함수는 무조건 자신의 상위 스코프가 가리키는 This를 이용하므로 상위 스코프를 가진 부모가 화살표 함수로 이루어진 메소드면 안되는거군요...

ㄷ ㄷ...... 상위 화살표 함수 메소드가 블록을 형성하면서 this를 정의내릴 당시 그 위에 상위가 일반 객체니 this가 undefined 네요...
심지어 무조건 상위에 강제된다 라는 성격때문에 call()이나 apply()도 죄다 무시해버리는군요... 또 하나를 배웁니다.
