![javasciprt](https://img.shields.io/badge/javascript-up%20to%20date-yellow)
![typescript](https://img.shields.io/badge/typescript-up%20to%20date-blue)

## **nth-child vs nth-of-type**

<br>
예전 css에 집중을 못하고 있었을 때 사용하면서 '아 씨 왜 저게 선택되는거야.. 그냥 그런건가...' 하고 넘어갔던 내용인데

오늘 그 이유를 깨닫고 나의 과거의 바보스러움을 질책하며 쓰게 됩니다.

간단하게 말하자면, nth-child()는 해당 범위 내에서 () 안의 번째에 있는 요소에게 스타일을 적용하고,

nth-of-type() 은 해당 지정된 타입을 리스트화하여 그 중에서 () 안의 것을 선택합니다.

---

## 1. nth-child()

```css
// 현재 구조가
<div>
    <p>1</p>
    <div>2</div>
    <p>3</p>
    <p>4</p>
    <span>5</span>
</div>

이런 상황이면,

1. div > p:nth-child(1)
= div 안에 있는 자식들 가운데, (1) 번째에 있는 녀석이 <p> 태그면 속성을 적용합니다

2. div > p:nth-child(2)
= 따라서, 이것은 적용되지 않습니다. 왜냐면 자식들 5명 중에 (2)번째에 있는 녀석이 <p> 면 적용하는 것인데, 2번째는 <div> 이기 때문입니다.
```

---

## 2. nth-of-type

반대로, nth-of-type은 자식들 가운데 해당 element에 대한 리스트를 따로 설정하여, 그 가운데에 () 번째 요소를 적용합니다.

```css
// 현재 구조가
<div>
    <p>1</p>
    <div>2</div>
    <p>3</p>
    <p>4</p>
    <span>5</span>
</div>

이런 상황이면,

1. div > p:nth-of-type(1)
= div 안에 있는 자식들 가운데, <p> 의 리스트를 따로 모아 그 가운데에서 1번째를 선택합니다. 따라서

2. div > p:nth-child(5)
= 따라서, 이것은 적용되지 않습니다. 왜냐면 div의 안에 있는 자식들 가운데, p 의 리스트를 따로 모아 그 중에서 5번째를 선택하는 것이지만, p는 3개밖에 없기 때문입니다.
```
