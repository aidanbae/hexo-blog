---
title: "[Javascript] Array 배열에 대한 이해 - part2"
catalog: true
date: 2017-12-06 16:49:38
subtitle: "자바스크립트 배열 자유자재로 다루기"
header-img:
tags:
---

# 자바스크립트 Array part2

저번시간에는 Array에 내장되어있는 함수 map, filter, forEach를 살펴보았습니다.
이번엔 find, findIndex, reduce, sort를 살펴보겠습니다.

뭔가 자료의 신뢰성을 위해서 딱딱하게 블로그를 해야한다지만 저는 그냥 진심으로 다가가기위해
막쓰겠습니다. 배보다 배꼽이 크면 안되니깐요 (횡설수설)

## reduce
---
이녀석은 기존의 것들과 달리 꽤나 까탈스러운 녀석입니다.
그만큼 배웠을 때, 강력함을 누릴 수 있습니다. *중간에 포기하지마세요~ ^-^*
reduce 메서드는 왼쪽에서 오른쪽으로 이동하면서 배열의 각 요소마다 누적 계산값과 함께 함수를 적용해
하나의 값으로 줄입니다.
`누적 계산값`이라는 단어가 포인트!

```
arr.reduce(callback[, initialValue])
```
우선 파라미터부터 알아볼까요

첫번째 인자 `callback(accumulator, currentValue, currentIndex, array)`
두번째 인자 `initialValue // optional`

콜백함수의 인자가 독특하네요. accumulator는 축적자 라는 뜻을 가지고있습니다.
축적해서 쌓이는 값이라고 생각하면 편하겠네요.
즉 첫번째 인자인 콜백함수는 축적된 값과 현재의 값으로 무언가를 하는 함수! 라고생각하시면 편합니다.

간단하게 모든 배열의 요소들을 순차적으로 누적해가면서 더하는 함수를 만들어보겠습니다
```
var numbers = [1, 2, 3, 4, 5]
numbers.reduce(function(accumulator, currentValue, currentIndex, array) {
  return accumulator + currentValue;
});

// 결과값 15
```
두번째인자를 생략했기때문에 첫 accumulator에 들어가는 값은 numbers배열의 첫번째 요소인 1이됩니다.
currentValue는 그다음 요소인 2가 되겠네요.

이런식으로 콜백함수는 여기서 총 4번호출됩니다.
1과 2를 합치고 그다음에는 해당결과값 3과 그다음요소인 3을 합칩니다.
즉, 배열을 왼쪽부터 오른쪽으로 돌면서 해당 콜백함수를 호출해 결과값을 누적해나갑니다.
스웩;; 멋지죠?

```javascript
// 화살표함수로 간지터지게 한줄작성
numbers.reduce( (prev, curr) => prev + curr );
```

### 비교를 통해 배열 내 가장 큰 수 구하기
```javascript
// 정석이라면
var array = [0,1,2,9,4,5,6,7]
var accumulator = 0;
for (var i = 0; i < array.length; i ++) {
  if (i == 0) { accumulator = array[i]; continue }
  if (accumulator <= array[i]) accumulator = array[i];
}
console.log(accumulator) // 9
```
```javascript
// 오늘 배운 리듀스를 활용해봅니다.
var array = [0,1,2,9,4,5,6,7]
array.reduce(function(a, b) {return Math.max(a, b);})
```
```javascript
// 참고용, 더 짧게 spread operator를 사용해봅시다ㅎㅎ 아름답죠
var max = Math.max(...array);
```

너무 멋지고 강력합니다 ㅠㅠ 날 가져요
[spread operator](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Operators/Spread_operator)에 대해서는 나중에 한번더 공부해보는 시간을 가지겠습니다.

## findIndex
---
도대체 fruits라는 배열에 사과는 몇번째 요소일까??
배열에 대해 특정 요소의 인덱스를 탐색하고 싶을때 어떻게 하시나요
저같은 경우 indexOf를 자주 사용합니다.
하지만 배열의 요소가 객체로 이루어졌을때는 indexOf만으로는 탐색할 수 없습니다.
그때 사용되는 findIndex입니다.

```
arr.findIndex(callback[, thisArg])
```
첫번째 인자 `callback(item, index, array)`
두번째 인자 `thisArg` // optional

주의할점
> 콜백이 진리값을 반환하지 않거나, 배열의 길이가 0인경우 -1을 반환합니다.
> findIndex는 0번째 부터 length-1까지의 인덱스요소에 대해 콜백함수를 순차적으로 실행합니다.
> true값을 반환하는 요소가 있을 경우 순차실행을 중지하고 해당 요소의 인덱스를 반환합니다.

```javascript
// MDN javascript Array.prototpye.findIndex 예제
function isBigEnough(element) {
  return element >= 15;
}

[12, 5, 8, 130, 44].findIndex(isBigEnough);
// 15보다 큰 요소는 130이므로 130의 index인 3이 결과값으로 도출됩니다.
```

## find
---
인덱스가 아닌 그녀석 그자체를 찾고싶습니다.
그렇다면 이녀석! find를 사용하시면됩니다.

```
arr.find(callback[, thisArg])
```
첫번째 인자 `callback(item, index, array)`
두번째 인자 `thisArg` // optional

사용법은 findIndex와 같습니다.
예제만 살펴보고 패스!

```javascript
var commentList = [
  {id : 1, child_count: 2, message: '댓글좀 곱게답시다'},
  {id : 2, child_count: 3, message: '꼰대짓좀 그만해요'},
  {id : 3, child_count: 0, message: '사랑해요'}
]

// id값이 3인 녀석의 child_count를 증가시키자!
commentId = 3
commentList.find(c => c.id === commentId).child_count++
// {id: 3, child_count: 1, message: "사랑해요"}
// 해당 객체의 child_count가 0에서 1로 증가되었음을 확인!
```
