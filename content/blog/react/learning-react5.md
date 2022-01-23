---
title: (러닝 리액트) 리액트 상태관리
date: "2022-01-23T12:53:37.121Z"
---

앞에서는 컴포넌트 트리를 만들었다.  
컴포넌트 트리는 프로퍼티를 통해 데이터가 흘러갈 수 있는 컴포넌트 계층 구조를 뜻한다.  
리액트 애플리케이션의 **상태**는 데이터에 의해 조종되며 변경될 수 있다.

상태와 프로퍼티는 서로 밀접한 관계를 주고받으며,  
컴포넌트의 상태가 바뀌면 상태도 바뀌어 새로 반영되도록 렌더링된다.

## 6.1 별점 컴포넌트 만들기

자주 쓰이는 별점 컴포넌트를 만들어보자.  
`yarn add react-icons` 를 하고, `StarRating.js` 컴포넌트를 작성해보자.

총 5개나 10개 등 필요한 별점이 있을 수도 있기 때문에 createArray 함수를 통해 총 개수만큼 Star 컴포넌트를 만들어보자

```javascript
import React from "react"
import { FaStar } from "react-icons/fa"

const Star = ({ selected = false }) => (
  <FaStar color={selected ? "red" : "grey"} />
)

const createArray = length => [...Array(length)]

export default function StarRating({ totalStars = 5 }) {
  return createArray(totalStars).map((n, i) => <Star key={i} />)
}
```

## 6.2 useState 훅

이제 StarRating 컴포넌트를 클릭할 수 있게 만들어야 된다.  
사용자는 컴포넌트를 클릭해서 rating 을 변경할 수 있어야 한다.  
리액트 상태에 이 값을 저장하고 변경하자.

상태를 리액트 함수 컴포넌트에 넣을 때는 **훅스**라고 부르는 리액트 기능을 사용한다.  
훅스에는 컴포넌트 트리와 별도로 재사용가능한 코드 로직이 들어가있다.

우리가 제일 먼저 다룰 훅은 상태를 리액트 컴포넌트에 추가하고 싶을 때 사용하는 **useState 훅**이다.

```javascript
import React, { useState } from 'react'
import { FaStar } from 'react-icons/fa'

...

export default function StarRating({ totalStars = 5 }) {
  const [selectedStars] = useState(3)
  return (
    <>
    {
      createArray(totalStars).map((n, i) => (
        <Star key={i} />
      ))
    }
    <p>
      {selectedStars} / {totalStars}
    </p>
    </>
  )
}
```

사용자가 선택한 별점을 저장하는 `selectedStars` 라는 상태변수를 만들어서,  
`useState` 훅을 `StarRating` 컴포넌트에 직접 추가했다.

useState 훅은 배열을 반환하는 호출가능한 함수다.  
이 배열의 **첫번째 값이 우리가 사용하려는 상태변수로, 함수에 전달하는 값은 상태 변수의 디폴트값**이다.

여기서는 selectedStars 가 초기값 3으로 설정된다.

이제 사용자로부터 다른 점수를 얻기 위해서는 사용자가 아무 별이나 클릭할 수 있게 해야한다.

```javascript
const Star = ({ selected = false, onSelect = f => f }) => (
  <FaStar color={selected ? "red" : "grey"} onClick={onSelect} />
)
```

star를 변경해서 onSelect 라는 프로퍼티를 추가한다.  
사용자가 FaStar 컴포넌트를 클릭하면 이 함수를 호출하고, 부모 컴포넌트에게 별이 클릭되었음을 그대로 전달한다.

```javascript
export default function StarRating({ totalStars = 5 }) {
  const [selectedStars, setSelectedStars] = useState(3)
  return (
    <>
      {createArray(totalStars).map((n, i) => (
        <Star
          key={i}
          selected={selectedStars > i}
          onSelect={() => setSelectedStars(i + 1)}
        />
      ))}
      <p>
        {selectedStars} / {totalStars}
      </p>
    </>
  )
}
```

StarRating 컴포넌트의 상태를 바꾸려면, selectedStars 의 값을 바꾸는 함수가 필요하다.  
useState 훅이 반환하는 배열의 두번째 원소는 **상태 값을 변경할 때 쓸 수 있는 함수**다.  
이 경우도 배열을 구조 분해해서 함수에 원하는 이름을 붙일 수 있다.

훅수에서 기억할 가장 중요한 내용은 훅이 걸린 컴포넌트를 렌더러와 연동시킨다는 점이다.  
setSelectedStars 함수를 사용해 selectedStars 의 값을 바꿀 때마다  
StarRating 함수 컴포넌트가 훅에 의해 다시 호출되면서 렌더링이 다시 이뤄진다.

훅에 걸린 데이터가 변경되면 데이터에 대한 훅이 걸린 컴포넌트에 새 값을 전달하면서 컴포넌트를 다시 렌더링해준다.

<img src="./images/06-2-click.gif" />

## 6.3 재사용성을 높이기 위한 리팩터링

StarRating 컴포넌트를 이제 다른 곳에서 활용할 수 있다.  
하지만 좀 더 사용성을 위해 몇 가지를 생각해봐야 한다.

**style 프로퍼티**를 생각해보자.  
이 프로퍼티를 사용하면 CSS 스타일을 엘리먼트에 추가할 수 있다.

```javascript
export default function StarRating({ style={}, totalStars = 5 }) {
  const [selectedStars, setSelectedStars] = useState(3)
  return (
    <div style={{ padding: "5px", ...style }}>
...
```

## 6.4 컴포넌트 트리 안의 상태

모든 컴포넌트에 상태를 넣는 것은 좋은 생각이 아니다.  
상태 데이터가 너무 많은 컴포넌트에 분산되면, 버그를 추적하거나 애플리케이션의 기능을 변경하기가 어려워진다.

애플리케이션의 상태나 어떤 특성의 상태를 한곳에서 관리할 수 있으면 상태를 이해하기가 더 쉬워진다.

제일 먼저 살펴볼 것은 상태를 컴포넌트 트리에 저장하고, 자식 컴포넌트들에게 prop으로 전달하는 방법이다.

`create-react-app` 으로 색 관리자 웹앱을 만들어보자.

`color-data.json` 파일에 3가지 색으로 이뤄진 배열이 있다.  
각 색에는 id, title, color, rating 이 있고, 이 데이터를 표시할 리액트 컴포넌트를 만들어보자.

```
# 폴더 구조

\src
  \components
    - StarRating.js
    - ColorList.js
    - Color.js
  \data
    - color-data.json
  - App.js
  - index.js
```

```javascript
// src/App.js
import React, { useState } from "react"
import colorData from "./data/color-data.json"
import ColorList from "./components/ColorList"

export default function App() {
  const [colors] = useState(colorData)
  return <ColorList colors={colors.colors} />
}
```

최상위 App 컴포넌트는 useState 를 추가해 색의 상태관리를 연결했다.  
App 컴포넌트는 colorData를 colors의 초기 상태로 사용한다.  
App으로부터 colors가 ColorList라는 컴포넌트에게 전달된다.

```javascript
// src/components/ColorList.js

import React from "react"
import Color from "./Color"

export default function ColorList({ colors }) {
  if (!colors.length) return <div>표시할 색이 없습니다</div>
  return (
    <div>
      {colors.map(color => (
        <Color key={color.id} {...color} />
      ))}
    </div>
  )
}
```

ColorList 컴포넌트는 prop으로 App 컴포넌트에게서 색을 전달받는다.  
colors가 비어있으면 메세지를, 배열이 있으면 이 배열에 대해 map을 수행하고,  
세부 정보를 Color 컴포넌트를 만들면서 트리의 아래 방향으로 각 색 정보를 내려보낸다.

```javascript
// src/components/Color.js
import StarRating from "./StarRating"

export default function Color({ title, color, rating }) {
  return (
    <section>
      <h1>{title}</h1>
      <div style={{ height: 50, backgroundColor: color }} />
      <StarRating selectedStars={rating} />
    </section>
  )
}
```

Color 컴포넌트는 title, color, rating 이라는 세 프로퍼티를 받는다.  
각 값은 `<Color {...color} />`를 통해 스프레드 연산자로 전달받는다.  
이 식은 color 객체에 있는 각 필드를 color 객체의 각 키와 똑같은 키를 가지는 프로퍼티로 Color 컴포넌트에 전달한다.

이제 App 컴포넌트에서 데이터를 트리의 아래로 내려보내서 각 데이터에 맞게 표시하는 웹앱을 만들었다.

<img src="./images/06-4.png">

### 6.4.2 상호작용을 컴포넌트 트리 위로 전달하기

지금까지는 부모 컴포넌트에서 자식 컴포넌트에게 데이터를 내려주는 방식으로 colors 배열을 렌더링 했다.

리스트에서 내려온 색의 평점을 변경하면 어떤 일이 생길까?  
colors 데이터는 트리의 루트에 존재한다.

따라서 자식 컴포넌트에 대해 벌어진 상호작용을 수집해서 트리의 위로 올려보내, 상태가 저장된 루트 컴포넌트에 도착하게 해야한다.

```javascript
import { FaTrash } from "react-icons/fa"

export default function Color({ id, title, color, rating, onRemove = f => f }) {
  return (
    <section>
      <h1>{title}</h1>
      <button onClick={() => onRemove(id)}>
        <FaTrash />
      </button>
      <div style={{ height: 50, backgroundColor: color }} />
      <StarRating selectedStars={rating} />
    </section>
  )
}
```

color 컴포넌트 안에 사용자가 삭제할 수 있는 버튼을 만들고,  
버튼에 onClick 핸들러를 추가해 onRemove 함수 프로퍼티를 호출할 수 있다.

이런 방식이 뛰어난 이유는 Color 컴포넌트를 **순수 컴포넌트로 유지**할 수 있기 때문이다.
Color 컴포넌트에는 상태가 없기 때문에 다른 애플리케이션에서 자유롭게 재사용할 수 있다.  
그리고 이벤트를 처리할 책임은 이제 부모 컴포넌트의 몫이 된다.

```javascript
export default function ColorList({ colors = [], onRemoveColor = f => f }) {
  if (!colors.length) return <div>표시할 색이 없습니다</div>

  return (
    <div>
      {colors.map(color => (
        <Color key={color.id} {...color} onRemove={onRemoveColor} />
      ))}
    </div>
  )
}
```

## 6.5 폼 만들기

우리는 리액트를 사용해 수없이 많은 폼 컨포넌트를 만들게 될 것이다.  
DOM 에서 사용할 수 있는 HTML 폼 엘리먼트 모두는 리액트 엘리먼트로도 제공된다.

```html
<form>
  <input type="text" placeholder="color title ..." required />
  <input type="color" required />
  <button>ADD</button>
</form>
```

### 6.5.1 참조 사용하기

리액트에서 폼 컴포넌트를 만들어야 할 때는 몇 가지 패턴을 사용할 수 있다.  
그 중 **참조(ref) 기능을 사용해 직접 DOM 에 접근하는 방법**이 포함된다.

리액트에서 참조는 컴포넌트의 생명주기 값을 저장하는 객체다.  
리액트에서는 참조를 제공할 때 쓸 수 있는 `useRef` 훅을 제공한다.

```javascript
import React, { useRef } from "react"

export default function AddColorForm({ onNewColor = f => f }) {
  const txtTitle = useRef()
  const hexColor = useRef()

  const submit = e => {
    e.preventDefault()
    const title = txtTitle.current.value
    const color = hexColor.current.value
    onNewColor(title, color)
    txtTitle.current.value = ""
    hexColor.current.value = ""
  }

  return (
    <form onSubmit={submit}>
      <input ref={txtTitle} type="text" placeholder="color title..." required />
      <input ref={hexColor} type="color" required />
      <button>ADD</button>
    </form>
  )
}
```

useRef 훅을 사용해 2가지 참조를 만들었다.  
input 태그에 ref 퍼티를 사용하면 참조의 값을 직접 JSX에서 설정할 수 있다.

사용자가 ADD 버튼으로 클릭해 폼을 제출하면 submit 함수를 호출한다.  
HTML 폼을 제출 할 때 디폴트 동작은 현재 URL로 폼 엘리먼트에 저장된 값이 본문에 있는 POST 요청을 보내는 것이다.  
우리는 이런 디폴트 동작을 막기 위해서 `e.preventDefault()` 코드를 작성한다

참조를 사용하면서 DOM 노드의 값을 직접 설정하면서 노드의 속성을 변경했다.  
이런 코드는 명령형 코드로, 제어되지 않는 컴포넌트가 된다.

### 6.5.2 제어가 되는 컴포넌트

```javascript
export default function AddColorForm({ onNewColor = f => f }) {
  const [title, setTitle] = useState("")
  const [color, setColor] = useState("#000000")

  const submit = e => {
    e.preventDefault()
    onNewColor(title, color)
    setTitle("")
    setColor("")
  }

  return (
    <form onSubmit={submit}>
      <input
        value={title}
        onChange={event => setTitle(event.target.value)}
        type="text"
        placeholder="color title..."
        required
      />
      <input
        value={color}
        onChange={event => setColor(event.target.value)}
        type="color"
        required
      />
      <button>ADD</button>
    </form>
  )
}
```

제어가 되는 컴포넌트는 title, color 의 값을 리액트 상태를 통해 저장한다.  
title, color 변수를 만들고, 상태를 변경할 `setTitle`, `setColor` 함수를 정의한다.

현 시점에서 값을 변경할 수 있는 방법은 input 엘리먼트에서 문자를 입력할 때마다 상태 변수를 변경하는 것 뿐이다.  
`onChange={e => setTitle(e.target.value)}` 를 사용해, 이벤트가 발생하면 현재 엘리먼트의 value를 알 수 있다.

### 6.5.3 커스텀 훅 만들기

제어가 되는 폼 컴포넌트를 만들 때 필요한 세부 사항을 커스텀 훅으로 묶을 수 있다.  
폼 입력을 만들 때 필요한 중복을 추상화해 없애주는 우리만의 `useInput` 훅을 만들 수 있다.

```javascript
import { useState } from "react"

export const useInput = initialValue => {
  const [value, setValue] = useState(initialValue)
  return [
    { value, onChange: e => setValue(e.target.value) },
    () => setValue(initialValue),
  ]
}
```
