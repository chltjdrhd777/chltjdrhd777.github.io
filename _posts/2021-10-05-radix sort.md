# **라딕스 솔트의 정리**

![javasciprt](https://img.shields.io/badge/javascript-up%20to%20date-yellow)

![typescript](https://img.shields.io/badge/typescript-up%20to%20date-blue)

---

```js
function radixSort(arr) {
  // todo: 여기에 코드를 작성합니다.
  // counting sort => 해당 배열 내의 숫자에 대한 갯수를 카운팅 한 후, 그것들을 숫자를 인덱스로 하는 새로운 배열에 삽입하고 갯수만큼을 더해줘서 해당 숫자가 시작되는 인덱스를 설정한다.
  // ex, [0,1,5,3,3,4,1,2]
  //     0 1 2 3 4 5
  // 갯수 [1,2,1,2,1,1] => 인덱스 기준에 따른 계산 => [1,3,4,6,7,8]
  // 그리고 기존 배열을 하나씩 비교해가면서 새로 만든 또다른 배열에 넣어주고, 계산된 인덱스 배열에서 하나씩 빼간다.

  // counting sort의 단점은, 만약에 저 중간에 느닷없이 100이 껴있거나 하는 경우, 의미없이 100까지 중간이 비어있는 100까지의 배열을 만들어야 하는 메모리의 낭비가 생긴다.
  // 따라서 counting sort는 설령 O(n)임에도, 적은 수의 배열을 정렬할때 유의미하다.

  // 반면 radix sort는 먼저 정렬기준에따른 테이블 0~9까지를 설정하고, 그 위에다가
  // 1의자리에 대한 구분 => 10의자리에 따른 재구분 => 100의자리에 따른 재구분의 형식으로
  // 하나하나씩 단계별로 정렬해가는 방법이다.

  // ! 일반적으로는 카운팅소트의 단점때문에 필요한 상황이 아니면 radix나 quick을 이용하는게 일반적이라고 하는데
  // ! 해당 레퍼런스에서 이 카운팅소트를 응용하여 radix sort에 사용한것을 보고 배워가고자 한다.

  /////////////////////// sudo //////////////////////////////
  // 1. 해당 배열에서 양수와 음수부분을 나눈다
  // 2. 양수의 부분에 radix sort를 한다
  // 3. 음수의 부분을 절대값으로 만들고 radix sort를 한다
  // 음수부분을 reverse후 음수를 붙인 뒤에 양수부분 정리된 부분과 합쳐서 리턴한다

  //1. 음수와 양수부분을 따로 나눈다
  let minus = [];
  let plus = [];
  arr.forEach((e) => {
    if (e < 0) minus.push(e * -1);
    else plus.push(e);
  });

  //2. 음수 부분의 radix sort를 구한다.
  //1의 자리(radix)에 대해서 구하고,
  //10의 자리에서 구하고
  //그렇게 구해가다가 결국 radix가 배열중에서 가장 큰 수의 자리수를 넘어가는 순간 종료
  let max = Math.max(...minus);
  let radix = 1;
  while (Math.trunc(max / radix) > 0) {
    minus = countingSort(minus, radix);
    radix *= 10;
  }

  let max2 = Math.max(...plus);
  radix = 1;
  while (Math.trunc(max2 / radix) > 0) {
    plus = countingSort(plus, radix);
    radix *= 10;
  }

  return minus
    .reverse()
    .map((e) => e * -1)
    .concat(plus);
}

function countingSort(targets, radix) {
  // [10, 11] 이 왔고, radix는 1의자리수가 왔음
  const count = Array(10).fill(0);
  const output = Array(targets.length).fill(0);

  // 현재 1의자리 기준으로, 그 수의 갯수를 카운팅한다
  targets.forEach((e) => {
    const idx = Math.trunc(e / radix) % 10;
    count[idx]++; // [1,1,0,0,.....,0] 즉, 현재 1의자리수에 0이 1개, 1이 1개가 왔다는 소리
  });

  // 해당 배열을 누적갯수로 치환한다
  count.reduce((acc, cur, idx) => {
    count[idx] = acc + cur;
    return acc + cur; // count는 [1,2,2,2,2,....,2]가 된다.
  });

  //뒤로부터 순회한다
  let i = targets.length - 1;
  while (i >= 0) {
    const idx = Math.trunc(targets[i] / radix) % 10; // 11 => 1;
    output[count[idx] - 1] = targets[i];
    count[idx] -= 1;
    i--;
  }

  return output;
}
```
