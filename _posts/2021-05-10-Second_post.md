# 두번째 포스팅

항상 느끼는 거지만 기본 개념이라도 언제나 다시금 공부해야 할 것이 있고, 배워야 할 것이 많다고 생각이 드는 날...

</br>

---

# 변수를 숫자로 바꾸는 방법

방법은 크게 세가지가 있다

1. Number()
2. parseInt()
3. parseFloat()

> **도대체 뭔차인겨...**

<br>

## 첫째. "_Number"_

Number() 은 어떤 주어진 내용에 대해 전체적으로 확인을 해서 숫자가 아닌 녀석이 존재한다면 곧바로 "NaN" 을 후려박는다.

추가로,

```javascript
console.log(Number("")); //0
console.log(Number(null));//0
console.log(Number(undefined));//NaN
console.log(Number({});//NaN
console.log(Number([]);//0
console.log(Number(true));//1
console.log(Number(false));//0
```

라는 특이한 형태를 보인다.

<br>

## 둘째. _parseInt()_

요놈도 안에 문자를 넣으면 숫자로 돌려주긴 한다.<br>근데 충요한 것은, 요놈은 처음부터 시작해서 **숫자같이 생긴놈이 끝나는 지점** 까지의 **_정수_** 를 반환한다

<br>
<br>
따라서,

```javascript
console.log(parseInt("2")); //2
console.log(parseInt("2.3")); //2
console.log(parseInt("2.3asb")); //2
```

그러나, 맨 앞에부터 숫자가 아니게 생긴 게 오면 NaN(not a number)을 내뱉는다. 참고로, string 형태로 집어넣어도 NaN이다

```javascript
console.log(parseInt(""));//NaN
console.log(parseInt("{}"));//NaN
console.log(parseInt("[]"));//NaN
console.log(parseInt("a334");//NaN
console.log(parseInt([]);//NaN
console.log(parseInt({});//NaN
console.log(parseInt(null);//NaN
console.log(parseInt(undefined);//NaN
```

# 셋째. _parseFloat()_

이녀석은 parseInt()와 행동은 똑같은데 차이가 있다. 그것은 실수(real number) 까지 내뱉는다는 점이다.
실수... 수학적으로 말하면 유리수니 무리수니 합쳐진거다! 라는데 그냥 소숫점 들러붙어있는거까지 나온다고 생각하면 된다.
</br>그리고 역시 처음에 숫자같이 생기지 않은 놈이 오면
NaN 을 내뱉는다.

---

오늘은 이정도로 포스팅하고
</br>
굳이 벨로그 냅두고 깃블로그 하는것도 웃기긴 하지만, 내 개인적으로 나중에 링크도 커스터마이징해서 달고싶고, 여러 기능들도 만들어서 달아보고 하고싶은 욕심이 있어서 깃블로그를 선택하게 되었다. 시간이 지나면 지날수록 발전하는 깃블로그의 모습을 보며 뿌듯해할지도 모르겠다.헤헤
