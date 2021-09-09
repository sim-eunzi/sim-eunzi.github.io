---
title: 웹 컴포넌트 API
date: "2021-09-09T23:46:37.121Z"
---

# 웹의 구성요소 (component)

오늘날의 주요 프론트엔드 프레임워크의 공통점은 바로 UI 구성을 위한 기본 블록으로 **컴포넌트**를 사용하는 점이다.  
이번 장에서는 최신 브라우저에서 **웹 컴포넌트**라고 하는 네이티브 API를 사용해서 컴포넌트를 작성해보자!

[웹 컴포넌트 자세히 알아보기](https://developer.mozilla.org/ko/docs/Web/Web_Components)

## 웹 컴포넌트 API 기술

아래는 재사용할 수 있는 UI 컴포넌트를 작성할 수 있는 세가지 기술이다.

- **HTML 템플릿**: `<template>` 태그는 렌더링되지는 않지만 자바스크립트 코드에서 동적인 콘텐츠를 만드는데 `스탬프`로 사용되는 경우에 유용하다.
- **사용자 정의요소(Custom elements)**: 이 API를 통해 개발자는 완전한 기능을 갖춘 자신만의 DOM 요소를 작성할 수 있다.
- **섀도우 DOM**: 엘리먼트의 기능을 프라이빗하게 유지할 수 있어, 외부 DOM에 영향을 받지 않아야 하는 경우에 유용하다.  
  **캡슐화**와 관련되어 있으며, 이와 다른 개념인 가상 DOM은 성능과 관련된다.

### 웹 컴포넌트 구현하는 방법

1. `Class 문법`를 사용해 웹 컴포넌트를 명시하는 클래스를 작성한다.
2. `CustomElementRegistry.define()` 메서드를 사용해 새로운 커스텀 엘리먼트를 등록하고, 정의할 엘리먼트 이름, 기능을 명시하는 클래스, 상속받은 엘리먼트를 전달한다.
3. `Element.attachShadow()` 메서드를 사용해 shadow DOM 을 커스텀 엘리먼트에 추가한다. 일반적인 DOM 메서드를 사용해 자식 엘리먼트, 이벤트 리스너 등을 shadow DOM에 추가한다.
4. `<template>과 <slot>`을 사용해 HTML 템플릿을 정의한다. 다시 DOM 메서드를 사용해 템플릿을 클론하고, shadow DOM에 추가한다.
5. 일반적인 HTML 엘리먼트처럼 어디에서든 커스텀 엘리먼트를 사용하면 된다!

### Custom Elements (사용자 정의 요소)

[MDN 문서 보기](https://developer.mozilla.org/ko/docs/Web/Web_Components/Using_custom_elements)

웹 컴포넌트의 핵심요소로, 다음과 같이 사용자가 정의한 html 태그를 작성하는 것이다.  
HTML 요소를 확장하는 자바스크립트의 클래스로,  
태그를 작성할 때는 **대시(-)로 구분된 두 단어 이상의 태그**를 사용해야 한다.

```html
<hello-world />
```

```javascript
export default class HelloWorld extends HTMLElement {
  connectedCallback() {
    window.requestAnimationFrame(() => {
      this.innerHTML = "<div>Hello world</div>"
    })
  }
}
```

`connectedCallback`은 사용자 정의 요소의 **라이프사이클 메서드** 중 하나로,
해당 엘리먼트가 **DOM에 연결될 때 호출**한다.  
(리액트의 `componentDidMount` / 뷰에 `mounted`와 비슷)  
엘리먼트의 콘텐츠를 렌더링하거나, 타이머를 시작하거나 데이터를 가져올 때 유용하다.

구성요소가 DOM에서 삭제될 때는 `disconnectedCallback`이 호출된다.

새로 작성한 위의 엘리먼트를 사용하려면, 브라우저 구성 요소 레지스트리에 추가해야한다.
**이때 `window.customElements` 속성의 `define` 메서드를 사용해준다**

```javascript
import HelloWorld from "./components/Helloworld.js"

window.customElements.define("hello-world", HelloWorld)
```

이제 <hello-world /> 컴포넌트를 사용할 수 있다!

### 컴포넌트의 속성 관리 방법

사용자 구성 요소에 속성을 추가해서 사용하려면, 다른 속성과 동일한 방식으로 관리할 수 있어야한다.

우리가 잘 알고있는 `<input>` 태그의 속성을 설정하는 방법을 먼저 알아보자.

```html
<!-- 1. 마크업에 직접 추가 -->
<input type="text" value="Frameworkless" />
```

```javascript
// 2. setter 를 사용해 value로 조작
input.value = "Frameworkless"

// 3. setAttribute 메서드 사용
input.setAttribute("value", "Frameworkless")
```
