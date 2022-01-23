---
title: (러닝 리액트) 자바스크립트를 활용한 함수형 프로그래밍
date: "2022-01-23T09:53:37.121Z"
---

## 3.1 함수형이란 무엇인가?

자바스크립트에서는 함수가 **1급 객체**이기 때문에 함수형 프로그래밍을 지원한다고 말할 수 있다.

**일급객체란 무엇인가?**

1. 변수에 함수를 대입할 수 있고,
2. 함수를 다른 함수에 인자로 넘길 수 있으며,
3. 함수를 반환할 수 있어야 한다.

최근 자바스크립트에서는 함수형 프로그래밍 기법을 풍부하게 해주는 화살표함수, 프라미스, 스프레드 연산자 등의 개선이 추가됐다.

```javascript
var log = function (message) {
  console.log(message)
}
log("함수를 변수에 넣을 수 있다. ")
// 함수를 변수에 넣을 수 있다.

const log = message => {
  console.log(message)
}
// 물론 화살표 함수도 가능!

const obj = {
  message: "함수를 다른값과 마찬가지로 객체에 추가할 수 있다.",
  log(message) {
    console.log(message)
  },
}
obj.message(obj.message)
// 함수를 다른값과 마찬가지로 객체에 추가할 수 있다.

const insideFn = logger => {
  logger("함수를 다른 함수의 인자로 넘길 수 있다.")
}
insideFn(message => console.log(message))
// 함수를 다른 함수의 인자로 넘길 수 있다.

const createScream = logger => message => {
  logger(message.toUpperCase() + "!!!")
}
// 함수가 함수를 반환할 수도 있다.
```

마지막 코드를 보면 함수가 함수를 반환하는 경우를 **고차함수**라고 부른다.

자바스크립트에선 함수가 일등 시민이기 때문에, 함수형 언어라고 말할 수 있고,  
함수를 일반 데이터와 마찬가지로 취급한다.  
그래서 함수를 일반 값과 마찬가지로 저장하거나 읽거나, 애플리케이션에서 흘려보낼 수 있다.

## 3.2 명령형 프로그래밍과 선언적 프로그래밍 비교

함수형 프로그래밍은 선언적 프로그래밍이라는 패러다임의 한가지다.  
달성하는 과정 하나하나를 기술하는 것보다 **필요한 것이 어떤 것인지를 기술하는 것**에 초점을 두고, 애플리케이션의 구조를 세워나가는 프로그래밍 스타일이다.

선언적 프로그래밍의 이해를 위해 명령형 프로그래밍과 비교해보자.

명령형 프로그래밍은 코드로 원하는 결과를 달성해 나가는 과정에만 관심을 두는 프로그래밍 스타일이다.

어떤 문자열을 URL에서 사용할 수 있게 만드는 작업을 비교해보자.

```javascript
const string = "Restaurants in Hanalei"
let urlFriendly = ""

for (var i = 0; i < string.length; i++) {
  if (string[i] === " ") {
    urlFriendly += "-"
  } else {
    urlFriendly += string[i]
  }
}

console.log(urlFriendly) // Restaurants-in-Hanalei
```

이 예제는 문자열의 모든 문자에 대해 루프를 돌면서 공백이 발생할 때마다 공백을 - 로 바꾼다.  
이런 구조의 프로그래밍은 달성하는 방법에만 신경을 쓴다.

이제 같은 문제를 선언적으로 풀어보자.

```javascript
const string = " Restaurants in Hanalei"
const urlFriendly = string.replace(/ /g, "-")

console.log(urlFriendly) // Restaurants-in-Hanalei
```

여기서는 string.replace와 정규식을 사용해서 모든 공백을 하이픈으로 바꾼다.  
바꾸는 자세한 로직은 replace 함수 안에 들어가고, 추상적인 개념을 표현한다.  
선언적 프로그래밍의 코드는 어떤일이 발생해야 하는지에 대해 기술하고, 실제로 그 작업을 처리하는 방법은 추상화를 통해 아랫단에 감춰진다.

또 코드 자체가 어떤 일이 벌어질지에 대해 설명하기 때문에 좀 더 추론하기가 쉽다.

문서 객체 모델(DOM)을 만드는 과정을 살펴보자.  
명령형 방식을 택하면 다음과 같이 구축하게 된다.

```javascript
var target = document.getElementById("target")
var wrapper = document.createElement("div")
var headline = document.createElement.("h1")

wrapper.id = "welcome"
headline.innerText = "Hello World"

wrapper.appendChild(headline)
target.appendChild(wrapper)
```

이 코드는 엘리먼트를 만들고, 설정하고 문서에 추가하는 각 단계에 중심한다.  
이런식으로 만줄이 넘는 코드를 변경하거나 기능을 추가하려면 아주 어려운 일이 될 것 이다.

리액트 컴포넌트를 이용해 DOM을 선언적으로 구성하는 방법을 살펴보자.

```javascript
const { render } = ReactDOM

const Welcome = () => (
  <div id="wrapper">
    <h1>Hello World</h1>
  </div>
)

render(<Welcome />, document.getElementById("target"))
```

리액트는 선언적이다. Welcome 컴포넌트는 렌더링할 DOM에 대해 기술 한다. render 함수는 컴포넌트에 있는 지시에 딸하 DOM을 만든다.  
실제로 DOM이 만들어지는 과정에 대해서는 추상화를 통해 사라지지만, target이라는 id를 가진 엘리먼트 안에 렌더링 하고 싶다는 프로그래머의 의도가 더 명확히 드러난다.

## 3.3 함수형 프로그래밍의 개념

### (1) 불변성 (immutability)

함수형 프로그래밍에서는 데이터가 변할 수 없다. 불변성 데이터는 결코 바뀌지 않는다는 뜻이다.

불변성 데이터는 **원본 데이터 구조를 변경하는 대신, 그 데이터의 복사본을 만들어 그 중 일부를 변경하는 방식**이다.

불변성이 어떻게 작동하는지 이해하기 위해 데이터를 변경한다는 것이 어떤 의미인지 살펴보자.

```javascript
let color_lawn = {
  title: "잔디",
  color: "#00FF00",
  rating: 0,
}
```

색에 평점을 매기는 함수를 만들어, 넘겨받은 color 객체의 rating을 변경하자.

```javascript
function rateColor(color, rating) {
  color.rating = rating
  return color
}

console.log(rateColor(color_lawn, 5).rating) // 5
console.log(color_lawn.rating) // 5
```

자바스크립트에서 함수의 인자는 실제 데이터에 대한 참조이다.  
rateColor 함수에서 color의 값 자체를 바꾸면 원본 color_lawn 객체의 값도 바뀐다.

```javascript
let rateColor = function (color, rating) {
  return Object.assign({}, color, { rating: rating })
}

// const rateColor = (color, rating) => {
//   ...color,
//   rating
// }

console.log(rateColor(color_lawn, 5).rating) // 5
console.log(color_lawn.rating) // 0
```

rateColor 의 함수를 Object.assign 을 사용해서 만들면 원본에 해가 없이 복사본을 사용하여 만들 수 있다.  
스프레드 연산자를 사용해도 똑같은 결과를 볼 수 있다.

색의 이름으로 이뤄진 배열을 생각해보자.  
그리고 배열에 Array.push 함수를 사용해 색을 추가하는 함수를 작성한다.

```javascript
let list = [{ title: "과격한 빨강" }, { title: "잔디" }, { title: "파티 핑크" }]

const addColor = function (title, colors) {
  colors.push({ title: title })
  return colors
}

console.log(addColor("화려한 녹색", list).length) // 4
console.log(list.length) // 4
```

원본 배열에도 새로운 원소를 추가한다. Array.push 함수가 불변성이 아니기 때문이다.  
원래의 list를 변경하지 않고 유지하기 위해선 Array.concat을 사용해야한다.

```javascript
const addColor = (title, array) => array.concat({ title })

// const addColor = (title, list) => [...list, {title}]

console.log(addColor("화려한 녹색", list).length) // 4
console.log(list.length) // 3
```

Array.concat은 그 객체를 원래의 배열을 복사한 새로운 배열 뒤에 추가한다.  
배열 스프레드 연산자를 사용해 배열을 복사해서 사용할 수도 있다.

이렇게 함수를 작성할 때는 객체 원본의 불변성을 지켜주는 방법을 사용하자

### (2) 순수함수 (Pure Function)

순수함수는 파라미터에 의해서만 반환값이 결정되는 함수를 말한다.  
최소한 하나 이상의 인수를 받고, 인자가 같으면 항상 같은 값이나 함수를 반환한다.

순수함수에는 부수효과(side effect)가 없다.

```javascript
const frederick = {
  name: "frederick Douglass",
  canRead: false,
  canWrite: false,
}

function selfEducate() {
  frederick.canRead = true
  frederick.canWrite = true
  return frederick
}

selfEducate()
console.log(frederick) // { name: 'frederick Douglass', canRead: true, canWrite: true}
```

`selfEducate` 함수는 순수하지 않다. 함수를 인자로 취하지 않고, 값을 반환하거나 함수를 반환하지도 않는다.  
`selfEducate`를 호출하면 코드 전체의 일부분이 바뀐다. 즉 부수효과가 발생한다.

이제 `selfEducate`가 파라미터를 받게 만들자

```javascript
const frederick = {
  name: "frederick Douglass",
  canRead: false,
  canWrite: false,
}

const selfEducate = person => {
  person.canRead = true
  person.canWrite = true
  return person
}

console.log(selfEducate(frederick)) // { name: 'frederick Douglass', canRead: true, canWrite: true}
console.log(frederick) // { name: 'frederick Douglass', canRead: true, canWrite: true}
```

파라미터를 받긴 하지만, 이 함수도 순수하지 않다. 부수효과가 있기 때문이다.  
이 함수를 호출하면 인수로 넘긴 객체의 필드가 바뀐다.  
이 함수로 전달된 객체를 불변 데이터로 취급하면 순수함수를 만들 수 있을 것이다.

```javascript
const selfEducate = (person) => ({
  ...person,
  person.canRead = true
  person.canWrite = true
})
```

마침내 selfEducate가 순수함수가 됐다.  
이 함수는 전달받은 person으로부터 새로운 값을 계산한다.

이제 DOM을 변경하는 순수하지 않은 함수를 살펴보자.

```javascript
function Header(text) {
  let h1 = document.createElement("h1")
  h1.innerText = text
  docuemnt.body.appendChild(h1)
}

Header("Header() caused side effects")
```

Header 함수는 인자로 받은 텍스트를 머릿글에 넣고 만들어준다.  
이 함수는 함수나 값을 반환하지 않으며 DOM을 변경하는 부수효과를 발생시킨다.

리액트는 UI를 순수함수로 표현한다.  
엘리먼트를 만드는 일만 책임지며, DOM을 변경하는 책임은 애플리케이션의 다른 부분이 담당해야한다.

```javascript
const Header = props => <h1>{props.title}</h1>
```

순수 함수는 함수형 프로그래밍의 또 다른 핵심 개념이다.  
순수 함수를 사용하면 애플리케이션의 상태에 영향을 미치지 않기 때문에 코딩이 편해진다.  
함수를 사용할 때 다음 세가지 규칙을 따르면 순수 함수를 만들 수 있다.

1. 순수 함수는 파라미터를 최소 하나 이상 받아야한다.
2. 순수 함수는 값이나 다른 함수를 반환해야 한다.
3. 순수 함수는 인자나 함수 밖에 있는 다른 변수를 변경하거나, 입출력을 수행해서는 안된다.

### (3) 데이터 변환

데이터가 변경 불가능하다면 애플리케이션에서 어떻게 무언가를 바꿀 수 있을까?  
함수형 프로그래밍은 함수를 사용해 원본을 변경한 복사본을 만들어낸다.  
한 데이터를 변환해서 다른 데이터를 만들어 낼 수 있는 핵심 함수가 있다.  
`Array.map`과 `Array.reduce`가 바로 두 함수다.

고등학교 명단이 들어 있는 배열을 생각해보자.

```javascript
const schools = ["Yorktown", "Washington & Lee", "Wakefield"]
console.log(schools.join(","))
// "Yorktown, Washington & Lee, Wakefield"
```

`Array.join` 함수를 사용하면 콤마로 각 학교를 구분한 문자열을 얻을 수 있다.  
join은 배열의 모든 원소를 인자로 받은 구분자로 연결한 문자열을 반환하고, 원래의 배열엔 영향을 주지 않는다.  
join이 제공하는 추상화를 사용하면 문자열을 실제로 어떻게 만드는지에 대해서는 신경 쓰지 않아도 된다.

'W'로 시작하는 학교만 들어있는 새로운 배열을 만들고 싶다면 Array.filter 메서드를 사용하면 된다.

```javascript
const wSchool = schools.filter(school => school[0] === "W")
console.log(wSchool)
// ["Washington & Lee", "Wakefield"]
```

`Array.filter`는 원본 배열로부터 새로운 배열을 만들어내는 자바스크립트 배열 내장 함수이다.  
이 함수는 술어(predicate)를 유일한 인자로 받는다.  
술어는 Boolean 값, 즉 true나 false를 반환하는 함수를 뜻한다.

Array.filter는 배열에 있는 모든 원소를 하나씩 사용해 이 술어를 호출한다.  
filter는 술어에 배열의 원소를 인자로 전달하고, 반환값이 true이면 해당 원소를 새로운 배열에 넣는다.

배열에서 원소를 제거해야 할 필요가 있다면 `Array.pop`이나 `Array.splice` 보다는 `Array.filter`를 사용하자.  
Array.filter는 순수 함수이다.

cutSchool 함수는 특정 학교의 이름을 제외한 새로운 배열을 반환한다.

```javascript
const cutSchool = (cut, list) => list.filter(school => school !== cut)

console.log(cutSchool("Washington & Lee", schools).join(","))
// "Yorktown", "Wakefield"

console.log(schools.join(","))
// "Yorktown, Washington & Lee, Wakefield"
```

`Array.map` 함수는 술어가 아니라 변환 함수를 인자로 받는다.  
그 함수를 배열의 모든 원소에 적용해서 반환반은 값으로 이뤄진 새 배열을 반환한다.

```javascript
const highSchools = schools.map(school => `${school} High School`)
console.log(highSchools.join("\n"))
// Yorktown High School
// Washington & Lee High School
// Wakefield High School

console.log(schools.join("\n"))
// Yorktown
// Washington & Lee
// Wakefield
```

이 경우 map 함수는 각 학교 이름 뒤에 'High School'을 추가한다.  
이때 원본 schools 배열은 아무 변화가 없다.

```javascript
const highSchools = schools.map(school => ({ name: school }))
console.log(highSchools)

// [{ name: "Yorktown" }, { name: "Washington & Lee"}, { name: "Wakefield" }]
```

해당 코드는 문자열의 배열에서 문자열의 배열을 만들었다.  
map 함수는 객체, 값, 배열, 다른 함수 등 모든 자바스크립트 타입의 값으로 이뤄진 배열을 만들 수 있다.

객체를 배열로 변환하고 싶을 때는 Array.map과 Object.keys를 함께 사용하면 된다.  
Object.keys는 어떤 객체의 키로 이뤄진 배열을 반환하는 메서드다.
