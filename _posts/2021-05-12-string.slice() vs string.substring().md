# 네번째 포스팅

![javasciprt](https://img.shields.io/badge/javascript-up%20to%20date-yellow)

**string.slice() vs string.substring()**

---

## Slice()

기본형 :

```typescript
    slice(start?: number, end?: number): string;
}
```

숫자로 주어지는 "start" parameter, "end" parameter;

```javascript
//not include end (start <= num < end)
const string = "string";

const test0 = string.slice(2); // "ring"
const test1 = string.slice(0, 2); //"st"

//no "start = end"
const test2 = string.slice(2, 2); // ""
//no "less start than end"
const test3 = string.slice(2, 1); // ""

const test4 = string.slice(2, -2); //"ri"
const test5 = string.slice(-5, -1); //"trin"

//no "minus start with plus end"
const test6 = string.slice(-2, 1); // ""
```

---

## substring()

기본형:

```typescript
    substring(start?: number, end?: number): string;
}
```

substring은 기본적으로 slice와 같지만 한가지 특이한 공통점을 기억하면 된다.

그것은 바로, 음수 인덱스는 0으로 바뀐다는 점이다. 따라서

```javascript
const test1 = string.substring(1); // "tring"
const test2 = string.substring(-1); // "string"
const test3 = string.substring(2, 4); // "ri"
const test4 = string.substring(1, 1); // ""
const test5 = string.substring(-1, 2); // "st"
const test6 = string.substring(-2, 1); // "s"
const test7 = string.substring(1, -3); // "s"
const test8 = string.substring(1, 0); // "s"
```

보면, substring(-1) 은 substring(0)이 되어 처음부터 끝까지,

substring(-1,2) 는 substring(0,2) 가 되어 "st"

substring(1,-3) 은 substring(1,0) 처럼 여겨, 1번째 순서 미포함 뒤로 0번째까지 반환하여 "s"

> slice와 마찬가지로 start 포함, end 미포함이다
