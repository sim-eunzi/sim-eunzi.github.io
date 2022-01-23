---
title: (러닝 리액트) 리액트를 위한 자바스크립트
date: "2022-01-23T01:53:37.121Z"
---

### 2.1 변수 선언하기

(1) const 키워드  
상수(constant)는 변경할 수 없는 변수이다.  
값을 재설정하려고 하면 콘솔 오류가 발생한다.

(2) let 키워드  
자바스크립트도 구문적인 변수 영역 규칙을 지원한다.

var 키워드를 사용해 if/else 블록 안에서 변수를 만들면,  
그 변수의 영역이 블록 안으로만 한정되지 않는다.

```javascript
var topic = "자바스크립트"
if (topic) {
  var topic = "리액트"
  console.log("블록", topic)
}
console.log("글로벌", topic)
```

위 코드를 실행하면 '글로벌 리액트' 라는 값이 출력된다.  
실제로 둘은 같은 변수이기 때문이다. (= 호이스팅)

```javascript
if (topic) {
  let topic = "리액트"
  console.log("블록", topic)
}
```

let 키워드를 사용하면 변수의 영역을 코드 블록 안으로 한정 시켜서,  
글로벌 변수를 보호할 수 있다.

(3) 템플릿 문자열  
템플릿 문자열을 사용하면 문자열 연결 대신 문자열 중간에 변수를 삽입할 수 있다.

```javascript
console.log(lastName + ", " + firstName)
console.log(`${lastName}, ${firstName}`)
```

전통적인 방식은 더하기(+) 기호를 사용해 이어붙이는 방식이지만,  
${} 템플릿을 사용하면 변수를 문자열에 집어넣어 사용할 수 있다.

### 2.2 함수 만들기

(1) 함수 선언  
함수의 선언 또는 정의는 `function` 키워드로 시작하고, 그 뒤에 함수 이름이 온다.

```javascript
function logCompliment() {
  console.log("굳~")
}
```

(2) 함수 표현식  
함수를 만드는 다른 방법으론 함수 표현식이 있다.  
함수 표현식은 이름없는 함수를 만들며, 변수에 값을 대입 할 수 있다.

```javascript
const logCompliment = function () {
  console.log("굳~")
}
logCompliment()
```

함수 선언은 호이스팅 되지만 함수 표현식은 그렇지 않다.

```javascript
hey()

function hey() {
  return "hey~"
}
// 에러없이 잘 실행됨

hey()

const hey = function () {
  return "hey~"
}
// ERROR! hey is not function
```

(3) 디폴트 파라미터

```javascript
function logActivity(name = "은지", activity = "테니스") {
  console.log(`${name}은 ${activity}를 좋아합니다`)
}
```

함수의 인자로 사용할 수 있는 디폴트 값을 사용할 수 있다.

(4) 화살표 함수  
function 키워드 없이 함수를 만들 수 있다. 또 return 을 사용하지 않아도 계산한 값이 자동으로 반환된다.

이름을 인수로 받아 지역 영주를 부르는 함수를 만들어보자

```javascript
const lordify = function (name) {
  return `켄터베리의 ${name}`
}

const lordify = name => `켄터베리의 ${name}`
```

화살표(=>)를 사용하면 한 줄로 모든 정의를 끝낼 수 있다.

(4-1) 화살표 함수로 객체 반환하기

```javascript
const person = (firstName, lastName) => ({
  first: firstName,
  last: lastName,
})

// 괄호를 둘러싸는 걸 잊지말자
```

(4-2) 화살표 함수와 영역

일반 함수는 this를 새로 바인딩한다.

```javascript
const gangwon = {
  reports: ["용평", "평창", "강촌", "강릉", "홍천"],
  print: function (delay = 1000) {
    setTimeout(function () {
      console.log(this.reports.join(","))
    }, delay)
  },
}
gangwon.print()
// Error! Cannot read property 'join'
```

이 오류는 this.reports 의 join 메서드를 호출하려고 시도했기 때문에 발생했다.  
this를 콘솔에 찍어보면, this가 window 객체임을 알 수 있다.

이 문제를 해결하기 위해 화살표 함수를 사용하면 this의 영역이 제대로 유지된다.

```javascript
print: function(delay=1000) {
  setTimeout(() => {
    console.log(this.reports.join(','))
  }, delay)
}
```

### 2.3 객체와 배열

(1) 구조 분해(destructuring)를 사용한 대입  
구조 분해를 사용하면 객체 안에 필드 값을 원하는 변수에 대입할 수 있다.

```javascript
const sandwich = {
  bread: "더치 크런치",
  meat: "참치",
  cheese: "스위스",
  toppings: ["상추", "토마토", "아보카도"],
}
const { bread, meat } = sandwich
console.log(bread, meat) // 더치 크러치 참치

bread = "마늘"
meat = "칠면조"

console.log(bread, meat) // 마늘 칠면조
console.log(sandwich.bread, sandwich.meat) // 더치 크러치 참치
```

두 변수의 값은 필드 값으로 초기화되지만,  
두 변수를 변경해도 원래의 필드값이 바뀌지 않는다.

객체를 분해해서 함수의 인자로도 넘길 수 있다.  
어떤 사람의 이름을 캔터베리의 영주처럼 표현해주는 함수를 생각해보자.

```javascript
const lordify = regularPerson => {
  console.log(`켄터베리의 ${regularPerson.firstName}`)
}

const regularPerson = {
  firstName: "은지",
  lastName: "심",
}

lordify(regularPerson) // 켄터베리의 은지
```

```javascript
const lordify = ({ firstName }) => {
  console.log(`켄터베리의 ${firstName}`)
}
```

함수의 인자를 객체로 받고, 필요한 값을 구조분해로 가져올 수 있다.

(2) 배열 구조 분해하기

배열을 구조 분해해서 값을 뽑아낼 수도 있다.  
배열의 첫 번째 원소를 변수에 대입하고 싶다고 가정하자.

```javascript
const [firstAnimal] = ["캥거루", "웜뱃", "코알라"]
console.log(firstAnimal) // 캥거루

const [, , thirdAnimal] = ["캥거루", "웜뱃", "코알라"]
console.log(thirdAnimal) // 코알라
```

불필요한 값을 콤마(,)를 사용해 생략하는 리스트 매칭도 사용할 수 있다.

(3) 객체 리터럴 개선  
객체 리터럴 개선은 구조분해의 반대로 볼 수 있다.  
변수를 객체에 필드에 개선할 수 있고, 메서드를 만드는 것도 가능하다.

```javascript
const name = "탈락"
const elevation = 9738

const funHike = { name, elevation }

console.log(funHike) // { name: '탈락', elevation: 9738 }
```

(4) 스프레드 연산자  
스프레드 연산자는 3개의 점(...)으로 이뤄진 연산자이다.  
첫번째 기능은 배열의 내용을 조합할 수 있다.

예를 들어 두 배열이 있다면, 두 배열의 모든 원소가 포함된 세번째 배열을 만들 수 있다.

```javascript
const peaks = ["대청봉", "중청봉", "소청봉"]
const canyons = ["천불동계곡", "가야동계곡"]
const seoraksan = [...peaks, ...canyons]

console.log(seoraksan) // ['대청봉', '중청봉', '소청봉', '천불동계곡', '가야동계곡']
```

Array.reverse 메서드를 사용해 배열을 뒤집어서 마지막 원소만 구하고 싶다.
reverse() 메서드를 사용하면 원본의 배열까지 변경이 되지만,  
스프레드 연산을 사용하면 원본은 변경하지 않고 복사본을 만들어서 뒤집을 수 있다.

```javascript
const peaks = ["대청봉", "중청봉", "소청봉"]
const [last] = [...peaks].reverse()
console.log(last) // 소청봉
```

또 스프레드 연산자를 사용해 배열의 나머지 원소들도 얻을 수 있다.

```javascript
const peaks = ["대청봉", "중청봉", "소청봉"]
const [first, ...rest] = peaks
console.log(rest) // ['중청봉', '소청봉']
```

함수의 인자를 스프레드 연산자를 사용헤 배열로 모을 수 있다.  
이런식으로 함수의 파라미터 정의에서 쓰일 때는 레스트 파라미터라고 부른다

```javascript
function directions(...args) {
  console.log(args) // ['서울','경기','강원','대구','경북']
}
directions("서울", "경기", "강원", "대구", "경북")
```

### 2.4 비동기 자바스크립트

현대 웹에서는 비동기 작업을 수행할 필요가 있다.  
완료가 될 때까지 기다려야하는 경우가 있기 때문이다.  
자바스크립트에서 비동기 처리를 쉽게 할 수 있는 방법에 대해 알아보자

(1) 단순한 프라미스와 fetch

REST API에 요청을 보내는 일은 간단한 데이터를 받아오려 해도 20줄이 넘는 코드를 작성해야 했는데 ,,  
`fetch()` 함수가 등장하면서 간단하게 작성할 수 있다.

randomuser.me API로부터 데이터를 가져와보자.  
이 API는 가짜 멤버에 대한 주소, 이름, 전화번호,, 등의 정보가 있다.  
fetch는 유일한 인수로 URL만 받는다.

```javascript
console.log(fetch("https://api.randomuser.me/?nat=US&results=1"))

// Promise {
//   __proto__: Promise {
//     constructor: ƒ Promise(),
//     then: ƒ then(),
//     catch: ƒ catch(),
//     finally: ƒ finally()
//   }
// }
```

콘솔 로그를 보면 대기중인 **프라미스**를 볼 수 있다.  
프라미스는 자바스크립트에서 비동기 동작을 잘 처리해주도록 도와준다.

대기중인 프라미스는 데이터가 도착하기 전의 상태를 표현하는데,  
`then()`이라는 함수를 대기중인 프라미스에 연쇄 호출해야 한다.  
이 함수는 콜백 함수를 인수로 받으며, 앞에 있는 연산(프라미스)가 성공해야 콜백이 호출된다.

=> fetch()는 데이터를 받아오고, then()은 데이터가 도착하면 데이터를 조작하는 일을 한다.

우리는 데이터의 응답을 JSON으로 바꾸고 싶다.

```javascript
fetch("https://api.randomuser.me/?nat=US&results=1").then(res =>
  console.log(res.json())
)
```

then은 프라미스가 정상적으로 완료되면 콜백함수를 한 번만 호출하는데,  
이 콜백함수가 반환하는 값은 그 다음에 오는 then 함수의 콜백에 전달되는 인자가 된다.  
따라서 성공적으로 처리된 프라미스들을 처리하기 위해 then 함수를 연쇄적으로 호출 할 수있다.

```javascript
fetch("https://api.randomuser.me/?nat=US&results=1")
  .then(res => res.json())
  .then(json => json.results)
  .then(console.log)
  .catch(console.error)
```

(2) async / await

비동기 프라미스를 처리하는 다른 방법으론 async 함수를 만드는 것이다.  
async 코드를 사용하면 전통적인 동기방식 코드와 비슷하기 때문에 일부 개발자들이 선호한다.

then 함수를 연쇄 호출해 프라미스의 결과를 기다리는 대신,  
async는 프라미스 다음에 있는 코드를 실행하기 전에 끝날 때까지 기다리라고 명령할 수 있다.

`randomuser.me` API에 대한 요청을 async 함수를 사용해 만들어보자

```javascript
const getFakePerson = async () => {
  let res = await fetch("https://api.randomuser.me/?nat=US&results=1")
  let { results } = res.json()
  console.log(results)
}
getFakePerson()
```

getFakePerson 함수 앞에 async 키워드가 붙어있어 비동기 함수로 만들어준다.  
프라미스 호출 앞에 await 키워드를 붙이면, 프라미스가 완료될 때까지 기다렸다가 함수가 진행된다.

(3) 프라미스 만들기  
비동기 요청을 하면 발생할 수 있는 경우는 2가지다. 잘 동작하거나 오류가 생기거나.  
요청 성공이나 실패에도 다양한 유형이 있는데, 예를들어 성공할 때까지 여러 번 다른 방법으로 데이터를 가져오려고 시도할 수도 있다.  
또 여러 유형의 오류를 받을 수도 있다.

getPeople 함수는 새로운 프라미스를 반환한다.  
프라미스는 API에 요청을 보내고, 프라미스가 성공하면 데이터를 읽어오고 실패하면 오류를 발생시킨다.

```javascript
const getPeople = count =>
  new Promise((resolves, rejects) => {
    const api = `https://api.randomuser.me/?nat=US&result=${count}`
    const request = new XMLHttpRequest()
    request.open("GET", api)
    request.onload = () =>
      request.status == 200
        ? resolves(JSON.parse(request.response).results)
        : reject(Error(request.statusText))
    request.onerror = err => rejects(err)
    request.send()
  })
```

이 코드로 프라미스가 만들어졌다.  
getPeople 함수를 사용하면서 원하는 회원수를 전달하면 프라미스를 사용해 API 호출할 수 있다.

```javascript
getPeople(5)
  .then(members => console.log(members))
  .catch(error => console.error(`getPeople failed: ${error.message}`))
```

프라미스를 사용하면 비동기 요청을 쉽게 처리할 수 있다.

### 2.5 클래스

ES2015 이전에는 공식적으로 클래스 문법이 없었다.  
클래스가 도입되면서 전통적인 객체지향 언어와 비슷한 구문을 제공하면서 흥분한 개발자들이 많았다.

자바스크립트는 **프로토타입을 사용한 상속**이라 불리는 방법을 사용하는데,  
이 기법은 객체지향처럼 느껴지는 구조를 만들어내기 위한 기법이다.

```javascript
function Vacation(destination, length) {
  this.destination = destination
  this.length = length
}

Vacation.prototype.print = function () {
  console.log(this.destination + "는 " + this.length + "일 걸립니다.")
}

const maui = new Vacation("마우이", 7)
maui.print() // 마우이는 7일 걸립니다
```

이 코드는 객체지향 언어와 비슷한 느낌을 만들어낸다.  
Vacation에는 프로퍼티들과 메서드가 있다.  
maui 인스턴스는 프로토타입을 통해 print 메서드를 상속받는다.

좀 더 표준과 가까운 방식을 위해 클래스 선언이 추가되었는데,  
이 또한 구문적 편의만 제공했지 동작하는 방법은 기존처럼 작동괸다.

```javascript
class Vacation {
  constructor(destination, length) {
    this.destination = destination
    this.length = length
  }

  print() {
    console.log(this.destination + "는 " + this.length + "일 걸립니다.")
  }
}
```

클래스를 만들 때는 보통 첫 글자를 대문자로 표시하고,  
클래스를 만들면 new 키워드를 사용해 새로운 인스턴스를 만들 수 있다.

### 2.6 ES6 모듈

모듈은 다른 자바스크립트 파일에서 이름 충돌이 없이 쉽게 불러 활용할 수 있는 재사용 가능한 코드 조각을 말한다.

자바스크립트는 모듈을 한 모듈당 하나의 별도의 파일로 저장한다.  
모듈을 만들고 외부에 익스포트 하는 방법은

한 모듈에서 여러 자바스크립트를 외부에 노출시키는 방법과,  
한 모듈에 하나의 자바스크립트 객체를 노출시키는 방법이 있다.

```javascript
export const print(message) => log(message, new Date())
export const log(message, timestamp) =>
  console.log(`${timestamp.toString()}: ${message}`)
```

위 파일에 있는 다음 모듈은 두 함수를 외부로 노출시킨다.  
export를 사용해 다른 모듈에서 활용하도록 이름을 외부에 익스포트 할 수 있다.

```javascript
const freel = new Expendition("프릴 산", 2, ["식수", "간식"])
export default freel
```

`export default`는 모듈에서 단 하나의 이름만을 외부로 익스포트한다.
