
## 왜 지금, 이 간단한 문법을 정리할까?
> 🍏 리액트에서 당연한듯 구조분해 할당을 써왔지만, 딱 리액트에서 쓰는 정도만 알았기에 정리 해본다.
> 🍎 실제로 많이 쓰이지만, 나는 안 쓴다. 그래서 정리를 하고 나면 자주 쓰겠지 하는 마음에 정리한다.

## 구조 분해 할당이란?
- MDN 정의에 따르면 배열이나 객체의 속성을 해체하여 그 값을 개별 변수에 담을 수 있게 하는 JavaScript 표현식
- js 뿐만 아니라 C언어에도 있는 문법인 것 같다

예시를 살펴보자.

## 기본값
MDN에 따르면 변수에 기본값을 할당하면, 분해한 값이 undefined일 때 그 값을 대신 사용한다고 한다.


```js
let x,y

[x=1, y=2] = [1] 
console.log(x) // 1
console.log(y) // 2

```

위의 경우 배열에 1이라는 값이 들어갔기 때문에 x는 1이다.
null이나 0이 들어가는 경우는 어떻게 될까?

```js
let x,y

[x=1, y=2] = [null] 
console.log(x) // null
console.log(y) // 2
```
null 대신 0을 넣어도 x는 0이 나온다.
빈 배열을 넣었을 때도 0이 나올줄 알았는데, x의 값이 1로 잘 나온다.
분해한 값이 undefined일때 그 값을 대신 사용한다는 말이 이 말인가보다.


## 객체에서 변수값 재할당
```js
const basket = {
  shineMusket : 12000,
  tangerine: 8000
}
```

basket 객체 안의 `tangerine`이라는 property를 `grapeFruit`으로 바꾸고 싶음

#### 구조분해 할당 없이 변수명 재할당
```js
const grapeFruit = basket.tangerine
```
걍 새로운 변수명에 basket의 tangerine과 같은 값을 대입해준다.
그러면 구조 분해 할당 없이 재할당 가능쓰!

#### 구조분해 할당을 이용해 변수명 재할당
```js
const { tangerine: grapeFruit } = basket

console.log(grapeFruit) //8000
console.log(basket) // let basket = { shineMusket : 12000, tangerine: 8000}
```

basket이라는 원본 객체의 값은 변하지 않지만, `grapeFruit` 변수에 basket.tangerine과 동일한 값을 적용했다!


### 흔하디 흔한 리액트 객체 구조 분해 할당 예시
```jsx
// 생략
this.state = {
  like: 0,
  follow: 0
}
// 생략

const { like, follow } = this.state;
```

props를 받아오거나 클래스형 컴포넌트에서의 state 값을 쓸 때 구조 분해 할당을 이용해주면
반복되는 당연한 단어들을 줄여줘서 좀 더 편리하다

## 배열에서 변수값 재할당
```js
const cafeMenu = ['iceLatte', 'iceDolceLatte']
```
cafeMenu 배열에서 `myPick`이라는 변수에 `iceLatte`를, `yourPick`이라는 변수에 `iceDolceLatte`를 할당하고 싶을때

#### 구조분해 할당 없이 변수명 재할당
```js
const myPick = cafeMenu[0];
const yourPick = cafeMenu[1];
```

걍 새로운 변수명에 cafeMenu 배열 안의 값을 대입해준다.
그러면 구조 분해 할당 없이 재할당 가능쓰!

#### 구조분해 할당을 이용해 변수명 재할당
```js
const [myPick,yourPick] = cafeMenu
console.log(myPick) // 'iceLatte'
console.log(yourPick) // 'iceDolceLatte'
console.log(cafeMenu) // ['iceLatte', 'iceDolceLatte']
```

cafeMenu라는 원본 배열 값의 변화 없이 새로운 변수에 basket.tangerine과 동일한 값을 적용했다!
이걸 실제로 어디다 쓰긴 쓸까 의문이었는데 쓰이더라...!

### 로그인 로직에서의 배열 구조 분해 할당 예시

```js
// 생략
  const [shouldBeBearer, token] = req.headers.authorization.split(' ');
// 생략
```

로그인 시 나오는 token은 
`Bearer hbTeF4m9hwXiojVHl3OobCOpunP-Rcas` <- 길어서 좀 자름
이런식으로 `Bearer` 띄어쓰고 토큰값이 string 형태로 온다.
그래서 띄어쓰기 단위로 split해준 다음에 Bearer와 token을 분리해준다.
그러면 앞에 Bearer는 필요 없으니까 뒤의 token값만 가져다 쓰면 짱짱 편리!

## rest가 들어갈 경우
MDN 설명에 따르면.. 배열을 구조 분해할 경우, 나머지 구문을 이용해 분해하고 남은 부분을 하나의 변수에 할당할 수 있다고 한다.
예시를 보자!

```js
const [a, ...maybe] = ['보건', '보건교사다', '나를아느냐', '나는안은영']
console.log(a) // '보건'
console.log(maybe) // [ '보건교사다', '나를아느냐', '나는안은영' ]
```

## 나름 심화 - for of 문에서 구조 분해 할당
Object.entries를 이용해 객체에서 각각의 key, value를 뽑아내려고 할 때

``` js
const user = {
  userName: 'dooreplay',
  level: 6445
}

for(let [key, val] of Object.entries(user)) {
  console.log(`${key} : ${val}`)
}

// 'userName : dooreplay'
// 'level : 6445'
```
이렇게 할 경우 콘솔을 찍어보면 `userName`과 `level` 정보가 차례로 찍힌다.
근데 user가 여러명일 경우에는 어떻게 될까?

```js
const userInfo = [{
  userName: 'dooreplay',
  level: 6445
},{
  userName: 'OSPark',
  level: 7630
},{
  userName: 'soooni',
  level: 1245
}]
```

이 경우에는 객체 구조 분해 할당을 이용해준다.

```js
const showUserInfo = arr => {
  for(let {userName: n, level: l} of arr) {
  console.log(`캔디크러쉬 사용자 정보를 알려드립니다. 사용자 이름은 ${n}이고, 레벨은 ${l} 입니다`)
  }
}

showUserInfo(userInfo)
```

콘솔에 깔끔하게 잘 나오고 있다. 굿굿

> TMI : 내 캔디크러쉬 사가 실제 레벨은 2020-10-26 오전 1:19 기준 실제로 6445 이다.
> 박옥숙님과 1위를 앞다투다 이제 박옥숙님의 레벨이 넘사가 되었다. 언젠가는 다시 따라 잡을거다.
> 캔디크러쉬 사가 전국 1등으로 정복 전에 리덕스 사가 정복이 먼저!


이렇게 정리 해놨으니까, 이제 쓰는 문법만 쓰지 않고 구조 분해 할당 자주 쓰겠지..?