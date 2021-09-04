---
title: 프로젝트를 진행하면서 좋은 코드에 대한 고찰..
date: "2021-09-03T23:53:37.121Z"
---

# 🤯 🤯 🤯

지금 회사에서는 Vue 2.6 버전을 사용해서 개발을 진행하고 있다.  
외주사가 막 짠 코드에 .. 신규타입을 덧붙이면서 촉박한 시간안에 기존 구조를 해치치않고,  
확장성있고 잘 동작하는 코드를 작성하는 건 무진장 어려운 일이다.

또 나 혼자 하는 일이 아니라 같이 일하시는 신입 개발자분과 함께 하는 일이라  
코드를 작성하면서 서로 납득될 수 있는 좋은 코드와 구조에 대해 고민을 많이 하고 있다..

짧은 경험이지만 그래도 내가 고민한 내용을 적어보고, 성장한 내가 봤을때 이게 맞는 접근인가!!  
되돌아 보기 위해 이 글을 작성한다.

---

<br />

프로덕트를 개발하려면 우리는 수많은 요구사항을 마주하게 된다.  
스타트업에서 근무하는 나는 바쁜 일정과 시시때때로 바뀌는 요구사항을 처리하다보면 나쁜코드를 종종 만들었었다

당장의 개발기간을 줄이기 위해 나쁜 코드를 뒤로하지만,,  
 이는 프로그램에 대한 신뢰도도 줄어들고, 결국 서비스를 망하게 하는 지름길이 된다.

그래서 우리는 뒤로 미루지말고!  
코드를 작성할 때 변수 이름을 개선하고, 조금 긴 함수를 분할하고,  
중복을 제거하는 정리를 바로바로 하는 습관을 들이는 게 좋다!

아래는 보통 사람들이 말하는 좋은 코드의 공통점을 정리한 내용이다.

**(1) 논리가 단순하고 직접적이다.**  
**(2) 의존성은 최대한 줄인다.**  
**(3) 중복되는 코드를 줄이고, 추상화한다.**

이렇게만 보면 심플하지만 막상 프로젝트에 적용하려면 고민이 많아진다..  
조금 더 구체적으로 적용할 수 있는 방법을 생각해보자!

---

#### (1) 함수의 부수 효과를 일으키지 말자!

거의 모든 프로그램의 가장 기본적인 단위가 되는 함수이다. 최소한 작게! 한 가지 일만 집중할 수 있게 작성하자.  
함수 외부의 값을 바꾸는 동작은 부수효과를 제어하기 어려울 뿐더러 결과를 예측하기 힘들게 한다.

```javascript
function checkLoginUser(email, password) {
  User user = UserService.findByEmailAndPassword(email, password)
  if(user) {
    Session.initalize()
    loginUser = user
    return true
  }
  return false
}
```

위 함수에서 부수 효과는 `Session.initalize(); loginUser = user`이다.  
`checkLoginUser` 함수 이름만 보면 로그인 유저를 체크하는 기능을 예상할텐데,  
세션 정보를 초기화 시키고, 함수 외부의 loginUser 변수에 새로운 user 가 할당될 위험에 처한다.

함수의 입출력으로 함수 내부의 동작을 수정하고 파악하기 어렵기때문에 이와 같은 코드는 피하는게 좋다!

---

#### (2) UI 렌더링과 비즈니스 로직을 분리하자

프론트엔드에서 API로 전송받은 받은 수많은 response를 화면에 출력하는 경우를 생각해보자.

```json
"course": {
  "name": "AI데이터와 사회",
  "price": 120000.0,
  "classStartDate": 1630773260004,
  "classEndDate": 1630972800000,
  "classTypeCd": '01'
  ...
}
```

위 데이터는 수업정보(course)에 대한 상세 정보를 조회한 JSON 이다.  
classStartDate, classEndDate 는 화면에 표시될 때 날짜에 대한 포맷팅이 필요해보인다.

수업 상세 정보를 화면에 출력하는 Vue.js 컴포넌트를 구현해보자.

```javascript
<template>
  <div v-if="response">
    <h1> {{ response.name }} </h1>
    <p> {{ response.price }} </p>
    <span>수업 시작 날짜: {{ formatMillisecondsToDate(reponse.classStartDate) }} </span>
    <span>수업 종료 날짜: {{ formatMillisecondsToDate(reponse.classEndDate) }} </span>
    <span>수업 타입: {{ classType }} </span>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  name: 'CourseDetailPage',
  data() {
    return {
      response: null
    }
  },
  computed: {
    classType() {
      if(this.response.classTypeCd == '00') {
        return '전공 필수'
      } else if(this.response.classTypeCd == '01') {
        return '전공 선택'
      } else if(this.response.classTypeCd == '02') {
        return '교양 필수'
      } else {
        return '교양'
      }
    }
  },
  async created() {
    await fetchCourseDetail()
  },
  methods: {
    async fetchCourseDetail() {
      this.response = axios.get('/course/123')
    },
    formatMillisecondsToDate(date) {
      return new Date(date).toLocaleString()
    }
  }
}
</script>
```

지금 당장은 위 컴포넌트를 봐도 아무런 위화감이 없어보인다.  
하지만 계산 로직이 길어지고, 여러 컴포넌트에 공통으로 쓰이는 데이터라면 코드가 매우 길어질 것이다.

그래서 우리는 비즈니스 로직 코드와 UI를 분리시킬 필요가 있다.  
전체 조직을 구성하는 요소가 분리되면, 각 하는 일이 뚜렷하게 부각되고, 계산 로직을 중복해 사용하지 않는다.

더 나아가 테스트 할 때도 UI에 대한 컴포넌트 테스트는 별도로,  
비즈니스 로직에 대한 유닛 테스트도 따로 작성할 수 있어 테스트하기 쉬워진다.

```javascript
// models/Course.js

export class Course {
  constructor({ name, price, classStartDate, classEndDate, classTypeCd }) {
    this.name = name
    this.price = price
    this.startDate = classStartDate
    this.endDate = classEndDate
    this.classTypeCd = classTypeCd
  }

  get formatStartDate() {
    return this.formatMillisecondsToDate(this.startDate)
  }

  get formatEndDate() {
    return this.formatMillisecondsToDate(this.endDate)
  }

  get classType() {
    if (this.classTypeCd == "00") {
      return "전공 필수"
    } else if (this.classTypeCd == "01") {
      return "전공 선택"
    } else if (this.classTypeCd == "02") {
      return "교양 필수"
    } else {
      return "교양"
    }
  }

  formatMillisecondsToDate(date) {
    return new Date(date).toLocaleString()
  }
}
```

```javascript
<template>
  <div v-if="response">
    <h1> {{ course.name }} </h1>
    <p> {{ course.price }} </p>
    <span>수업 시작 날짜: {{ course.formatStartDate }} </span>
    <span>수업 종료 날짜: {{ course.formatEndDate }} </span>
    <span>수업 타입: {{ course.classType }} </span>
  </div>
</template>

<script>
import axios from 'axios'
import { Course } from './models/Course.js'

export default {
  name: 'CourseDetailPage',
  data() {
    return {
      course: null
    }
  },
  async created() {
    await fetchCourseDetail()
  },
  methods: {
    async fetchCourseDetail() {
      const { data } = await axios.get('/course/123')
      this.course = new Course({data})
    }
  }
}
</script>
```

아래는 같은 코드를 UI와 비즈니스 로직을 분리한 것이다.  
이제 UI와 관련된 내용은 해당 컴포넌트의 template 만 신경쓰고,  
데이터와 관련된 내용은 비즈니스 로직에만 신경쓰면 개발하기 훨씬 쉬워진다!


