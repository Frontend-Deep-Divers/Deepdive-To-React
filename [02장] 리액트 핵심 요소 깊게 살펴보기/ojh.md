# 2.1 JSX 란?

---

- JSX 가 포함된 코드를 아무런 처리 없이 실행하면 에러가 발생한다.
- 자바스크립트 표준이 아닌 페이스북이 임의로 만든 새로운 문법
- 트랜스파일러를 거쳐 비로소 자바스크립트 런타임이 이해할 수 있는 자바스크립트 코드로 변함.
- 💬 요약하면 JSX는 자바스크립트 내부에서 표현하기 까다로웠던 XML 스타일의 트리 구문을 작성하는 데 많은 도움을 주는 새로운 문법이다.

### 2.1.1 JSX의 정의

- JSX 는 기본적으로 **JSXElement, JSXAttributes, JSXChildren, JSXStrings** 라는 4가지 컴포넌트를 기반으로 구성되어 있다.

1️⃣ **JSXElement**

- 가장 기본 요소로, HTML 요소와 비슷한 역할을 한다. 다음 형태 중 하나여야 한다.
  ![image.png](attachment:e90d2053-8d21-43e5-ae89-e5f17ed71c10:image.png)
- 대략 여는 요소, 닫는 요소, 스스로 닫는 요소, 프래그먼트 이렇게 4가지인 것으로 보인다.
- **❗요소명은 대문자만 사용해야 한다. ← 이는 HTML 태그명과 사용자가 만든 컴포넌트 태그명을 구분 짓기 위해서이다.**
  ![image.png](attachment:ae197ce4-f70a-41f8-b9bc-dc816eef334c:image.png)
- **JSXElementName**
  - JSXElement 의 요소 이름으로 쓸 수 있는 것을 의미한다.
  - 가능한 것들
    - JSXIdentifier: JSX 내부에서 사용할 수 있는 식별자.
    - JSXNamespaceName: JSXIdentifier:JSXIdentifier 의 조합.
    - JSXMemberExpression: JSXIdentifier.JSXIdentifier 의 조합.
    ![image.png](attachment:4e6c6928-2e62-4f92-9500-fe27e56c69b8:image.png)

2️⃣ **JSXAttributes**

- JSXElement 에 부여할 수 있는 속성을 의미한다. 단순히 속성을 의미하기에 필수값이 아니고, 존재하지 않아도 에러가 발생하지 X
  ![image.png](attachment:1b108621-b8eb-4c3b-9695-62a512301d1a:image.png)

3️⃣ **JSXChildren**

- JSXElement 의 자식 값을 나타낸다.
- JSX 로 부모 자식 관계를 나타낼 수 있으며, 이 속성이 JSXChildren 이다.
  ![image.png](attachment:ad35a57c-245f-4753-af57-a1199f433355:image.png)

**4️⃣ JSXStrings**

- 이게 뭔지 설명이 잘 이해되지 않음….. 🗣️⚠️

### 2.1.2 JSX 예제

- 아래는 모두 유효한 JSX
  ![image.png](attachment:9c531208-5d88-4a76-b1ab-07555c049fb7:image.png)
  ![image.png](attachment:9ca22b58-5ab4-4c31-bd96-53c6c4be4ee2:image.png)
  ![image.png](attachment:322e167f-c4b4-4c67-bfe6-551881e75c27:image.png)

### 2.1.3 JSX는 어떻게 자바스크립트에서 변환될까?

- 리액트에서 JSX 를 변환하는 @babel/plugin-transform-react-jsx 플러그인이 JSX 구문을 자바스크립트가 이해할 수 있는 형태로 변환한다.
  ![image.png](attachment:622c66b8-e790-4039-aaaf-24a0777af9bf:image.png)
  ![image.png](attachment:95205427-fd95-41a6-baa5-cfab0e1d5a6c:image.png)
- 위처럼 변환된다.
- 리액트 17, 바벨 7.9.0 이후 버전에서 추가된 자동 런타임으로 트랜스파일한 결과는 아래와 같다.
  ![image.png](attachment:ceae5334-a732-46f8-a0dc-71b0da7d3b68:image.png)
- 두 결과물에는 약간의 차이가 있지만 공통점이 있다? 🤔 무슨 말이지?
  - JSXElement 를 첫 번째 인수로 선언해 요소를 정의한다.
  - 옵셔널인 JSXChildren, JSXAttributes, JSXStrings 는 이후 인수로 넘겨주어 처리한다.
- 이 점을 활용하여 아래와 같은 상황에 중복 코드를 최소화 할 수 있어 유용하다고 한다… ← JSXElement 만 다르고, JSXAttributes, JSXStrings는 같은 경우
  ![image.png](attachment:3bbede6c-ac98-432f-9cf8-054af24a43e9:image.png)
- 솔직히 그렇게 좋은 건진 잘 모르겠다…
- JSX 반환값이 결국 React.JSXElement 로 귀결된다는 사실을 파악해 이렇게 리팩토링할 수 있다.

### 2.1.4 정리

- JSX 문법에는 있지만 리액트에서 사용하지 않는 것.
  - JSXNamespaceName
  - JSXMemberExpressio
- **🌟JSX 는 리액트에 종속된 문법이 아니다!! → 따라서 다른 라이브러리에는 위 문법이 사용될 수 있으므로 알아둬야 한다.**
- JSX는 자바스크립트 코드에 HTML 과 같은 트리 구조를 가진 컴포넌트를 표현할 수 있다는 점에서 각광받지만, 인기만 있지는 않다.
- 가독성을 해친다는 의견이 있음. → 따라서 주의해서 사용하자.
- 때에 따라서는 직접 createElement 를 사용해 구성하는 편이 효과적일 수 있다.

# 2.2 가상 DOM 과 리액트 파이버

---

- 리액트의 가장 큰 특징 바로 가상 DOM 이다.
- 가상 DOM 이 무엇인지와 이점과 주의할 점에 대해서 알아보자.

### 2.1.1 DOM과 브라우저 렌더링 과정

![image.png](attachment:2f54d36e-6bc4-4f14-b29e-638f9071080a:image.png)

![image.png](attachment:87e414f5-7384-4433-993f-41b3949960ca:image.png)

![image.png](attachment:069b9b41-591a-4760-8040-91771d646395:image.png)

### 2.2.2 가상 DOM 의 탄생 배경

- 렌더링 과정은 복잡하고 많은 비용이 든다.
- 심지어 요즘 대다수 앱은 처음 렌더링 이후 사용자와의 인터랙션을 통해 다양한 정보를 노출한다. → 즉, 렌더링 이후에도 웹 페이지가 변경되는 상황을 고려해야 한다.
- 이러한 렌더링 후 추가 렌더링 작업은 SPA 에서 더욱 많아지게 된다. 물론 부드럽고 자연스러운 사용이 가능하지만 그만큼 DOM 을 관리하는 과정에서 부담할 비용이 커진다.
- 또한 개발자 입장에서도 하나의 페이지에서 DOM 의 여러 가지가 변경되는 시나리오는 개발자가 모두 추적하기에는 너무나 수고스러운 일이며, 개발자는 최종 결과물 하나만 알고 싶을 것입니다.
- 이러한 복합적인 문제를 해결하기 위해 나온 것이 가상 DOM 이다.
- 표시해야 할 DOM 에 대해 메모리에 저장하고 준비가 완료되었을 때 실제 브라우저 DOM 에 반영한다.
- **⚠️ 가상 DOM 에 대해 가지고 있는 한 일반적인 오해는 리액트의 이러한 방식이 DOM 을 관리하는 브라우저보다 빠르다는 사실이다. → 무조건 빠른 것이 아니라 대부분의 상황에서 웬만하는 빠르다는 것.**

### 2.2.3 가상 DOM을 위한 아키텍처, 리액트 파이버

- 리액트는 어떻게 여러 번의 렌더링 과정을 압축해, 가상 DOM 으로 최소한의 렌더링 단위를 만들어 내는 것일까? → **이것이 바로 React Fiber 이다.**

**1️⃣ 리액트 파이버란?**

- **리액트 파이버는 리액트에서 관리하는 평범한 자바스크립트 객체이다.** 재조정자(fiber reconciler)가 관리하는데, 이는 앞서 이야기한 가상 DOM 과 실제 DOM 을 비교해 변경 사항을 수집하며, 만약 이 둘 사이에 차이가 있으면 변경에 관련된 정보를 가지고 있는 파이버를 기준으로 화면에 렌더링을 요청하는 역할을 한다.
- 재조정: 가상 DOM 과 실제 DOM 을 비교하는 작업
- **❗파이버가 하는 일**
  - 작업을 작은 단위로 분할하고 쪼갠 다음, 우선순위를 매긴다.
  - 이러한 작업을 일시 중지하고 나중에 다시 시작할 수 있다.
  - 이전에 했던 작업을 다시 재활용하거나 필요하지 않은 경우 폐기할 수 있다.
- **🌟 이 모든 과정은 비동기로 일어난다.**
- 과거에는 조정 알고리즘으로 이루어져 스택 알고리즘으로 이루어져 있어, 동기적으로 작업이 이루어졌다. 하지만 싱글 스레드라는 점으로 인해 동기 작업은 중단될 수 없고, 다른 작업이 수행되고 싶어도 중단할 수 없어 리액트의 비효율성으로 이어짐.
- 이를 타파하기 위해 스택 조정자 대신 파이버 탄생
- 파이버는 하나의 작업 단위로 구성되어 있음. → 하나의 작업을 처리하고 finishWork() 작업으로 마무리한다. → 이후 이를 커밋해 실제 브라우저 DOM 에 가시적인 변경 사항을 만들어 낸다.

  - **렌더 단계**에서는 리액트는 사용자에게 노출되지 않는 모든 비동기 작업을 수행한다. 이 단계에서 파이버의 작업, 우선순위를 지정하거나 중지시키거나 버리는 작업이 일어난다.
  - **커밋 단계**에서는 DOM에 실제 변경 사항을 반영하기 위한 작업, commitWork() 가 실행되는데, 이 과정은 앞서와 다르게 동기식으로 일어나고 중단될 수도 없다.

- ❗책을 보면 파이버가 단순한 자바스크립트 객체임을 알 수 있다.
- ❗**리액트 요소와 유사해 보이지만 리액트 요소는 렌더링이 발생할 때마다 새롭게 생성되지만 파이버는 가급적이면 재사용된다.**
- 🌟 **파이버는 컴포넌트가 최초로 마운트되는 시점에 생성되어 이후에는 가급적이면 재사용된다.**

- 리액트 파이버의 선언된 주요 속성을 살펴보자.
  - **tag** : 파이버를 만드는 함수 이름인 createFiberFromElement 로부터 파이버는 하나의 element 에 대해 1:1 관계를 가지는 것을 알 수 있다. 이 매칭된 정보를 가진 것이 tag 이다. 1:1 로 연결되는 것은 리액트의 컴포넌트일 수도 HTML 의 DOM 노드일 수도, 혹은 다른 어떤 것일 수도 있다. 아래 코드에서 다음과 같이 확인할 수 있다.
  - 아래에서 HostComponent 가 바로 웹의 div 와 같은 요소를 의미한다.
    ![image.png](attachment:46f2b361-17e4-4dc1-b0c6-30a4b6b40d93:image.png)
  - **stateNode** : 파이버 자체에 대한 참조 정보를 가지고 있으며, 이 참조를 바탕으로 리액트는 파이버와 관련된 상태에 접근한다.
  - **child, sibling, return** : 파이버 간의 개념 관계를 나타내는 속성이다. 리액트 컴포넌트 트리가 형성되는 것과 동일하게 파이버도 트리 형식을 갖게 되는데, 이 트리 형식을 구성하는 데 필요한 정보를 내부 속성에 정의된다. ❗한 가지 리액트 컴포넌트 트리와 다른 점은 children 이 없고 하나의 child 만 존재한다는 점이다.
  - 그렇다면 아래와 같이 여러 개의 자식이 있는 경우는 어떻게 표현하는 지 보자.
    ![image.png](attachment:f3b76b98-b9da-4870-bb8f-8f2b22f0b07f:image.png)
  - **index** : 여러 형제들 (sibling) 사이에서 자신의 위치를 숫자로 표현
  - **pendingProps** : 아직 작업을 미처 처리하지 못한 props
  - **memoizedProps** : pendingProps를 기준으로 렌더링이 완료된 이후에 pendingProps 를 memoizedProps 로 저장해 관리한다.
  - **updateQueue** : 상태 업데이트, 콜백 함수, DOM 업데이트 등 필요한 작업을 담아두는 큐
  - 이 큐는 다음과 같은 구조를 가지고 있다.
    ![image.png](attachment:401cbb94-8df0-471c-a006-0d7c86c10a2f:image.png)
  - **memoizedState** : 함수 컴포넌트의 훅 목록이 저장된다. 여기에는 단순히 useState 뿐만 아니라 모든 훅 리스트가 저장된다.
  - **alternate** : 리액트 파이버 트리와 이어지는 개념으로 리액트의 트리는 두 개인데, 이 alternate 는 반대편의 트리 파이버를 가르킨다.
- 이렇게 생성된 파이버는 state 가 변경되거나 생명주기 메서드가 실행되거나 DOM 의 변경이 필요한 시점 등에 실행된다.
- **🌟 중요한 것은 리액트가 파이버를 처리할 때마다 이러한 작업을 직접 바로 처리하기도 하고 스케줄링 하기도 한다는 것이다. → 즉, 작업을 작은 단위로 나누어서 처리할 수도, 애니메이션과 같이 우선순위가 높은 작업은 가능한 빠르게 처리하거나, 낮은 작업을 연기시키는 등 유연하게 처리한다.**
- 리액트 개발 팀은 이런 말도 했다. 리액트는 사실 가상 DOM 이 아닌 Value UI, 즉 값을 가지고 있는 UI 를 관리하는 라이브러리이다. → 핵심 원칙은 UI 를 문자열, 숫자, 배열과 같은 값으로 관리한다는 것이다. 변수에 이러한 UI 관련 값을 보관하고 리액트의 자바스크립트 코드 흐름에 따라 이를 관리하고, 표현하는 것이 리액트이다.

**2️⃣ 리액트 파이버 트리**

- 파이버 트리는 리액트 내부에서 두 개가 존재한다.
- 하나는 현재 모습을 담은 파이버 트리이고
- 다른 하나는 작업 중인 상태를 나타내는 workInProgress 트리이다.
- ❗**리액트 파이버의 작업이 끝나면 리액트는 단순히 포인트만 변경해 workInProgress 트리를 현재 트리로 바꾸어 버린다. 이러한 기술을 더블 버퍼링이라고 한다.**
- 더블 버퍼링이란? 컴퓨터 그래픽 분야에서 사용하던 용어인데, 그래픽을 통해 화면에 표시되는 것을 그리기 위해서는 내부적으로 처리를 거쳐야 하는데, 이러한 처리를 거치게 되면 미처 사용자에게 다 그리지 못한 모습을 보여주는 경우가 발생하여, 보이지 않는 곳에서 그리고 현재 상태를 새로운 그림으로 바꾸는 기법이다.
  ![image.png](attachment:fb0326c3-a73f-4731-b60d-21823585ff45:image.png)
- 🌟 **리액트에서도 미처 다 그리지 못한 모습을 노출시키지 않기 위해 더블 버퍼링 기법을 쓰는데, 이걸 위해 트리가 두 개 존재하며, 커밋 단계에서 수행된다**.
- 먼저 현재 UI 렌더링을 위해 존재하는 current 를 기준으로 모든 작업이 시작된다. → 만약 업데이트가 발생하면 파이버는 리액트에서 새로 받은 데이터로 새로운 workInProgress 트리를 빌드하기 시작한다. → 이 workProgress 트리를 빌드하는 작업이 끝나면 다음 렌더링에 이 트리를 사용한다. → 그리고 이 workInProgress 트리가 UI 에 최종적으로 렌더링되어 반영이 완료되면 current 가 이 workInProgress 로 변경된다.

**3️⃣ 파이버의 작업 순서**

- 작동 흐름을 살펴보자.
- 먼저 생성 흐름은 아래와 같다.
  ![image.png](attachment:38723403-ade1-4108-bc02-79bb9bc35489:image.png)
- 이를 아래 코드에서 흐름을 봐보자.
  ![image.png](attachment:39915204-1d38-4419-9fdf-f83d74b61780:image.png)
- 수행 순서와 도식 화면이다.
  ![image.png](attachment:26a7cedb-24ed-43c4-b0e6-2b5939573176:image.png)
  ![image.png](attachment:d66285d5-5c28-4c42-a347-88bb29be900e:image.png)
- 이제 여기서 setState 등으로 상태가 업데이트 되면 어떻게 될까?
- 리액트는 앞서 만든 current 트리가 존재하고, setState로 인한 업데이트 요청을 받아 workInProgress 트리를 다시 빌드하기 시작한다.
- 최초 렌더링 시에는 모든 파이버를 새롭게 만들어야 했지만 이제는 파이버가 이미 존재하므로 되도록 새로 생성하지 않고 기존 파이버에서 업데이트된 props 를 받아 파이버 내부에서 처리한다.

**4️⃣ 파이버와 가상 DOM**

- 파이버는 리액트 아키텍처 내부에서 비동기로 이뤄진다. 이런 비동기 작업과 달리, 실제 브라우저 구조인 DOM 에 반영하는 것은 동기적으로 일어나야 하며, 또 처리하는 작업이 많아 화면에 불완전하게 표시될 수 있는 가능성을 높이므로 이러한 작업을 가상에서, 즉 메모리 상에서 먼저 수행하여 최종 결과물만 실제 브라우저 DOM 에 적용하는 것이다.
- ❗놀랍게도 가상 돔은 오직 웹 어플리케이션에만 통용된다. 리액트 파이버는 리액트 네이티브와 같은 브라우저가 아닌 환경에서도 사용할 수 있기 때문에 가상 DOM 과 파이버는 동일한 개념이 아니다.
- ❗리액트와 리액트 네이티브의 렌더러가 서로 다르다 하더라도 내부적으로 파이버를 통해서 조정되는 과정은 동일하기 때문에 재조정자를 사용할 수 있게 된다.

**5️⃣ 정리**

- 가상 DOM 과 파이버는 단순히 브라우저에 DOM 을 변경하는 작업보다 빠르다는 이유만으로 만들어진 것은 아니다.
- 만약 개발자가 직접 DOM 을 수동으로 하나하나 변경해야 한다면, 어떤 값이 바뀌었는지, 그 값에 따라 어떠한 값이 변경됐고 이와 관련된 것들이 무엇인지 파악하기 매우 어려울 것이다.
- 이런 어려움을 리액트 내부의 파이버와 재조정자가 내부적인 알고리즘을 통해 관리해 줌으로써 대규모 웹 애플리케이션을 효율적으로 유지보수하고 관리할 수 있게 된 것이다.
- **🌟 가상 DOM 과 리액트의 핵심은 브라우저의 DOM 을 더욱 빠르게 그리고 반영하는 것이 아니라 바로 값으로 UI 를 표현하는 것이다.** 화면에 표시되는 UI 를 자바스크립트의 문자열, 배열 등과 마찬가지로 값으로 관리하고 이러한 흐름을 효율적으로 관리하기 위한 메커니즘이다.

# 2.3 클래스 컴포넌트와 함수 컴포넌트

---

- 함수형 컴포넌트는 0.14 버전 부터 있던 꽤 역사가 깊은 컴포넌트 방식이다.
- 예전에는 stateless functional component 라는 무상태 컴포넌트로 별도의 상태 없이 단순히 렌더링 시키는 것이 목적이었다.
  ![image.png](attachment:e0d5dbda-2e58-4fb6-a8b5-605b9ceb24b9:image.png)
- 단순히 render 만 하는 경우 제한적으로 사용했다. 상태가 필요없는 경우 16.8 버전 부터 훅이 소개되고 이때부터 각광받기 시작했다.

### 2.3.1 클래스 컴포넌트

```jsx
import React from "react";

class SampleComponent extends React.Compoenent {
  render() {
    return <h2>Sample Component</h2>;
  }
}
```

- 기본적으로 클래스 컴포넌트를 만들기 위해서는 클래스를 선언하고 extends로 만들고 싶은 컴포넌트를 extends 해야 한다.
  - React.Component
  - React.PureComponent
- 컴포넌트를 만들 때 주로 쓰이는 props, state 그리고 메서드는 아래와 같이 정의한다.

```tsx
import React from "react";

// props 타입을 선언한다.
interface SampleProps {
  required?: boolean;
  text: string;
}

// state 타입을 선언한다.
interface SampleState {
  count: number;
  isLimited?: boolean;
}

// Component에 제네릭으로 props, state를 순서대로 넣어준다.
class SampleComponent extends React.Component<SampleProps, SampleState> {
  // constructor에서 props를 넘겨주고, state의 기본값을 설정한다.
  private constructor(props: SampleProps) {
    super(props);
    this.state = {
      count: 0,
      isLimited: false,
    };
  }

  // render 내부에서 쓰일 함수를 선언한다.
  private handleClick = () => {
    const newValue = this.state.count + 1;
    this.setState({ count: newValue, isLimited: newValue >= 10 });
  };

  // render에서 이 컴포넌트가 렌더링할 내용을 정의한다.
  public render() {
    const { required, text } = this.props;
    const { count, isLimited } = this.state;

    return (
      <h2>
        Sample Component
        <div>required ? "필수" : "필수아님"</div>
        <div>문자열: {text}</div>
        <div>count: {count}</div>
        <button onClick={this.handleClick} disabled={isLimited}>
          증가
        </button>
      </h2>
    );
  }
}
```

- 부분별로 살펴보자.
  - **constructor () :** 컴포넌트 내부에 이 생성자 함수가 있다면 컴포넌트가 초기화되는 시점에 호출된다. 여기서는 컴포넌트의 state 를 초기화 할 수 있다. 그리고 여기에 선언돼 있는 super 컴포넌트를 만들면서 상속받은 상위 컴포넌트에 접근할 수 있게 도와준다.
  - 간혹 contructor 가 없는데 state 를 초기화 하는 경우가 있는데 이는 ES2022 에 추가된 클래스 필드 덕분에 가능한 문법이다.
    ![image.png](attachment:5bff4a7a-00cc-4a14-ae68-88585237cfa0:image.png)
  - **props** : 함수에 인수를 넣는 것과 비슷하게, 컴포넌트에 특정 속성을 전달하는 용도로 쓰인다.
  - **state** : 클래스 컴포넌트 내부에서 관리하는 값을 의미하며, **값은 항상 객체여야 한다!! 값에 변경이 생길 때마다 리렌더링이 발생한다.**
  - 메서드 : 렌더링 함수 내부에서 사용되는 함수이며, 보통 DOM 에서 발생하는 이벤트와 함께 사용된다. 방식은 크게 3가지가 있다.
    - **constructor 에서 this 바인딩을 하는 방법** → bind 함수를 활용해 this 를 바인딩 해줘야 한다.
    - **화살표 함수를 사용하는 방법** → 이를 통한 this 바인딩
    - **렌더링 함수 내부에서 함수를 새롭게 만들어서 전달하는 방법** → 그러나 이 방법은 렌더링이 일어날 때마다 새로운 함수를 생성해 할당하게 되므로 최적화가 어렵기 때문에 지양하도록 하자.

**1️⃣ 클래스 컴포넌트의 생명주기 메서드**

- 생명주기 (life cycle) 메서드가 실행되는 시점은 크게 3가지이다.
  - mount : 컴포넌트가 마운팅 (생성) 되는 시점
  - update : 내용이 변경되는 시점
  - unmount : 컴포넌트가 더 이상 존재하지 않는 시점
- 이 시점을 기준으로 생명주기 메서드를 살펴보자.
  ### render ()
  - 컴포넌트가 UI 를 렌더링하기 위해서 쓰인다.
  - 🌟 **마운트와 업데이트, 두 시점에서 일어난다.**
  - **❗항상 부수 효과가 없어야 한다. → 따라서 render 함수 내부에서 state 를 직접 업데이트하는 this.setState 를 호출해서는 안 된다.**
  - state 를 변경하는 일은 클래스 컴포넌트의 메서드나 다른 생명주기 메서드 내부에서 발생해야 한다.
  - 따라서 최대한 깔끔하고 간결하게 작성하는 것이 좋다.
  ### componentDidMount()
  - 클래스 컴포넌트가 마운트되고 준비되면 그 다음으로 호출되는 생명주기 메서드이다.
  - 컴포넌트가 마운트되고 즉시 실행된다.
  - **❗이 함수 내부에서는 this.setState() 로 state 값을 변경하는 것은 가능하다.**
  - state 가 변경되고 그 즉시 다시 한 번 렌더링을 시도하는데, 이 작업은 브라우저가 실제로 UI 를 업데이트 하기 전에 실행되어 사용자가 변경되는 것을 눈치챌 수 없게 만든다.
  - ❗**componentDidMount 는 만능이 아니고 성능 문제를 일으킬 수 있다는 것에 주의하자.**
  - 일반적으로 state 를 다루는 것을 생성자에서 하는 것이 좋다.
  - 이곳에서는 생성자 함수에서는 할 수 없는 것, API 호출 후 업데이트, DOM 에 의존적인 작업 등을 하기 위해서다.
  ### componentDidUpdate()
  - 컴포넌트 업데이트가 일어난 이후 바로 실행된다.
  - 일반적으로 state 나 props 의 변화에 따라 DOM 을 업데이트하는 등에 쓰인다.
  - ❗**여기서도 this.setState 를 사용할 수 있는데, 적절한 조건문으로 감싸지 않는다면 this.setState 가 계속해서 호출될 수 있어, 성능적으로 매우 좋지 못하다.**
    ![image.png](attachment:9e1849fe-2edf-497c-bd47-7ab291aa035d:image.png)
  ### componentWillUnmount()
  - 컴포넌트가 언마운트되거나 더 이상 사용되지 않기 직전에 호출된다.
  - 메모리 누수나 불필요한 작동을 막기 위한 클린업 함수를 호출하기 위한 최적의 위치다.
  - ❗**이 메서드 내에서는 this.setState 를 호출할 수 없다.**
    ![image.png](attachment:618e1955-6c2c-4068-8342-308cee5488cb:image.png)
  - 위 예제와 같이 이벤트를 지우거나, API 호출을 취소하거나 setInterval, setTimeout 으로 생성된 타이머를 지우는 등의 작업을 하는 데 유용하다.
  ### shouldComponentUpdate()
  - state 나 props 의 변경으로 리액트 컴포넌트가 다시 리렌더링되는 것을 막고 싶다면 사용한다.
  - 기본적으로 this.setState 가 호출되면 컴포넌트를 리렌더링을 일으킨다. → 이 생명주기 메서드를 사용하면 컴포넌트에 영향을 받지 않는 변화에 대해 정의할 수 있다.
  - **❗일반적으로 state 의 변화에 따라 컴포넌트가 리렌더링되는 것은 굉장히 자연스러운 일이므로 이 메서드를 사용하는 것은 특정한 성능 최적화 상황에서만 고려해야 한다.**
  - 이해가 잘 안됐는데, 렌더링을 최저화하기 위한 함수로 true 인 경우만 리렌더링하고 false 인 경우 리렌더링을 막는 것으로 보인다.
  ### Componet 와 PureComponenet 의 차이
  - coponent 는 상태가 바뀌면 바로 렌더링 수행
  - pureComponent 는 얕은 복사를 수행해서 다른 경우에만 렌더링 수행
  - 따라서 어떤 곳에 PureComponent 를 사용하는 것이 좋은지 고민하고 개발하는 것이 필요해 보인다.
    ![image.png](attachment:98eb5e28-fbc7-4f8e-bbac-f88eab4e849b:image.png)
  ### static getDerivedStateFromProps()
  - 가장 최근 도입된 생명주기 메서드 중 하나, componentWillReceiveProps 를 대체할 수 있는 메서드.
  - render() 를 호출하기 직전에 호출된다.
  - ❗**statice 으로 선언되어 있어 this 에 접근할 수 없다는 것이 특징이다.**
  - 여기서 반환하는 객체의 내용이 모두 state 로 들어가게 된다.
    ![image.png](attachment:37da780a-5d38-49f8-b25c-6a51cc7654a2:image.png)
  ### getSnapShotBeforeUpdate()
  - 최근에 도입된 생명주기 메서드 중 하나, componentWillUpdate() 를 대체할 수 있는 메서드이다.
  - 이는 DOM 이 업데이트 되기 직전에 호출되고 이 반환값은 componentDidUpdate 로 전달된다.
  - DOM 에 렌더링되기 전에 윈도우 크기를 조절하거나 스크롤 위치를 조정하는 등의 작업을 처리하는 데 유용하다.
    ![image.png](attachment:65052f2f-9a40-4630-8bc7-e497f9b9b7c5:image.png)
  ### 지금까지 언급한 생명주기 메서드 정리
  ![image.png](attachment:8067503c-f482-42ec-b7b2-66e2147bdc5d:image.png)
  ### getDerivedStateFromError()
  - 🚨 **이 메서드와 뒤이어 소개할 메서드인 componentDidCatch 메서드는 에러 상황에서 실행되는 메서드이다.**
  - 이 둘과 getSnapshotBeforeUpdate 는 아직 훅으로 구현되어 있지 않기에 필요한 경우 반드시 클래스 컴포넌트를 사용해야 한다.
  - static 메서드며, error 를 인수로 받는다. 하위 컴포넌트에서 발생한 에러이다.
  - **❗반드시 state 값을 반환해야 하는데, 실행 시점 때문이다. → 하위 컴포넌트에서 에러가 발생했을 경우에 어떻게 자식 리액트 컴포넌트를 렌더링할지 결정하는 용도로 제공되는 메서드이기 때문에 반드시 미리 정의해 둔 state 값을 반환해야 한다.**
  - 또한 부수효과를 발생시켜서도 안된다. 여기서 부수 효과란 에러에 따른 상태 state 를 반환하는 것 외의 모든 작업을 의미한다.
    ```tsx
    import React, { PropsWithChildren } from "react";

    type Props = PropsWithChildren<{}>;
    type State = { hasError: boolean; errorMessage: string };

    export default class ErrorBoundary extends React.Component<Props, State> {
      constructor(props: Props) {
        super(props);
        this.state = {
          hasError: false,
          errorMessage: "",
        };
      }

      static getDerivedStateFromError(error: Error) {
        return {
          hasError: true,
          errorMessage: error.toString(),
        };
      }

      render() {
        // 에러가 발생한 경우에 해당하는 JSX
        if (this.state.hasError) {
          return (
            <div>
              <h1>예기치 못한 에러가 발생했습니다.</h1>
              <p>{this.state.errorMessage}</p>
            </div>
          );
        }

        // 일반적인 상황의 JSX
        return this.props.children;
      }
    }
    ```
  ### componentDidCatch
  - 자식 컴포넌트에서 에러가 발생했을 때 실행되며, getDerivedStateError 에서 에러를 잡고 state 를 결정한 이후에 실행된다.
  - 인수로 error 와 에러 정보 info 이다.
    ```tsx
    import React, { ErrorInfo, PropsWithChildren } from "react";

    type Props = PropsWithChildren<{}>;
    type State = { hasError: boolean; errorMessage: string };

    export default class ErrorBoundary extends React.Component<Props, State> {
      constructor(props: Props) {
        super(props);
        this.state = {
          hasError: false,
          errorMessage: "",
        };
      }

      static getDerivedStateFromError(error: Error) {
        return {
          hasError: true,
          errorMessage: error.toString(),
        };
      }

      // componentDidCatch를 추가했다.
      componentDidCatch(error: Error, info: ErrorInfo) {
        console.log(error);
        console.log(info);
      }

      render() {
        // 에러가 발생했을 경우에 렌더링할 JSX
        if (this.state.hasError) {
          return (
            <div>
              <h1>에러가 발생했습니다.</h1>
              <p>{this.state.errorMessage}</p>
            </div>
          );
        }

        // 일반적인 상황의 JSX
        return this.props.children;
      }
    }
    ```
    ![image.png](attachment:948dd4b5-5d9d-436b-ab13-72496deeddac:image.png)
  - 🌟 **componentDidCatch 에서 앞서 getDerivedStateFromError() 에서 하지 못했던 부수 효과를 수행할 수 있다. 이는 render 단계에서 실행되는 getDerivedStateFromError 와 달리 componentDidCatch 는 커밋 단계에서 실행되기 때문이다!!**
  - 위 두 메서드를 사용하여 ErrorBoundary 를 만든다.
  - 하지만 모든 에러를 잡아 낼 수 있는 것은 아니다. 경계 외부에서 발생하는 에러는 또 다른 에러 바운더리를 찾아갈 것이며 만약 찾지 못하면 에러가 throw 된다.
  - 반대로 여러 개를 선언함으로서 에러 처리를 다르게 적용도 할 수 있다는 의미이다.
    ![image.png](attachment:e0698635-292a-43e4-99aa-0239f2523dee:image.png)
  ### 🚨 에러 바운더리에서 주의 할 점
  ![image.png](attachment:a334aab0-c776-4ada-ac20-3c63032957f0:image.png)

**2️⃣ 클래스 컴포넌트의 한계**

- 어떤 문제점 때문에 함수형 컴포넌트에 훅을 도입한 새로운 패러다임을 만든 것일까?
- 추측
  - **데이터의 흐름을 추적하기 어렵다.**
  - **애플리케이션 내부 로직의 재사용이 어렵다.**
  - **기능이 많아질수록 컴포넌트의 크기가 커진다.**
  - **클래스는 함수에 비해 상대적으로 어렵다.**
  - **코드 크기를 최적화하기 어렵다. →** 사용되지 않는 메서드가 트리 셰이킹 되지 않는 것을 확인할 수 있다. 클래스 컴포넌트는 번들링 최적화하기에 분리하다.
  - **핫 리로딩을 하는 데 상대적으로 분리하다. →** 핫 리로딩이란 코드에 변경 사항이 발생했을 때 앱을 다시 시작하지 않고 변경 사항을 빠르게 적용시키는 기법을 말한다. → 클래스 컴포넌트를 최초 렌더링 시에 instance 를 새로 생성하고 그 내부에서 관리되기 때문에 render 를 수정하게 되면 새로 만들기 때문에 값이 초기화된다. → ❗❗**이에 반해 함수 컴포넌트는 state 를 함수가 아닌 클로저에 저장해 두기 때문에 함수가 다시 실행돼도 state 를 잃지 않고 보여줄 수 있는 것이다.**
- 이러한 여러 한계를 극복하기 위해 훅이 출시되었다!

### 2.3.2 함수 컴포넌트

- 16.8 버전 훅이 나오기 전까지 무상태 컴포넌트를 구현하기 위한 하나의 수단에 불과했다.
  ```tsx
  import { useState } from "react";

  type SampleProps = {
    required?: boolean;
    text: string;
  };

  export function SampleComponent({ required, text }: SampleProps) {
    const [count, setCount] = useState<number>(0);
    const [isLimited, setIsLimited] = useState<boolean>(false);

    function handleClick() {
      const newValue = count + 1;
      setCount(newValue);
      setIsLimited(newValue >= 10);
    }

    return (
      <h2>
        Sample Component
        <div>{required ? "필수" : "필수 아님"}</div>
        <div>문자: {text}</div>
        <div>count: {count}</div>
        <button onClick={handleClick} disabled={isLimited}>
          증가
        </button>
      </h2>
    );
  }
  ```
- 정말 간결해졌다….
- this 바인딩 조심할 필요도 없고, 그냥 너무 편하다.

### 2.3.3 함수 컴포넌트 vs 클래스 컴포넌트

- 차이점을 살펴보자.

**1️⃣ 생명주기 메서드의 부재**

- 가장 눈에 띄는 차이점.
- useEffect 메서드를 사용해 componentDidMount, componentDidUpdate, componentWillUnmount 를 비슷하게 구현할 수 있다.
- 단 비슷할 뿐 똑같은 건 아니다.

**2️⃣ 함수 컴포넌트와 렌더링된 값**

- 함수 컴포넌트는 렌더링된 값을 고정하고, 클래스 컴포넌트는 그렇지 못하다.

```tsx
import React from "react";

interface Props {
  user: string;
}

// 함수 컴포넌트로 구현한 setTimeout 예제
export function FunctionalComponent(props: Props) {
  const showMessage = () => {
    alert("Hello " + props.user);
  };

  const handleClick = () => {
    setTimeout(showMessage, 3000);
  };

  return <button onClick={handleClick}>Follow</button>;
}

// 클래스 컴포넌트로 구현한 setTimeout 예제
export class ClassComponent extends React.Component<Props, {}> {
  private showMessage = () => {
    alert("Hello " + this.props.user);
  };

  private handleClick = () => {
    setTimeout(this.showMessage, 3000);
  };

  public render() {
    return <button onClick={this.handleClick}>Follow</button>;
  }
}
```

- 위 두 코드는 동일하게 작동할 것 같지만 아니다.
- ❗**만약 props 를 중간에 변경하게 되면 함수형 컴포넌트는 이전의 값을, 클래스형 컴포넌트는 최신 값을 보여준다.**
- 이 이유는 클래스형 컴포넌트는 props 의 값을 언제나 this 에서 가져오기 때문이다. this 는 변경 가능한 값이다. 그렇기에 변경된 값을 읽을 수 있다.
- state 도 마찬가지!!
- 함수 컴포넌트는 렌더링이 일어날 때마다 그 순간의 값인 props 와 state 를 기준으로 렌더되고, 변경되면 그 값을 기준으로 함수가 호출된다.
- 반면 클래스 컴포넌트는 시간의 흐름에 따라 변화하는 this 를 기준으로 렌더링이 일어난다.

**3️⃣ 클래스 컴포넌트를 공부해야 할까?**

- 일단 사라질 계획은 없다.

### 2.3.4 정리

- 역사를 잘 알아봤습니다~~~~
- 클래스 컴포넌트는 리액트에 능숙해졌을 때 익혀보자.

# 2.4 렌더링은 어떻게 일어나는가?

---

- 리액트도 브라우저와 마찬가지로 렌더링 작업을 위한 자체적인 렌더링 프로세스가 있으며, 이를 이해하는 것이 곧 리액트를 이해하는 첫걸음이다.

### 2.4.1 리액트의 렌더링이란?

- 렌더링은 브라우저에서도 사용되는 용어이므로 두 가지를 혼동해서는 안된다.
- **🌟 리액트의 렌더링이란 리액트 애플리케이션 트리 안에 있는 모든 컴포넌트들이 현재 자신들이 가지고 있는 props 와 state 의 값을 기반으로 어떻게 UI 를 구성하고 이를 바탕으로 어떤 DOM 결과를 브라우저에 제공할 것인지 계산하는 일련의 과정을 의미한다.**
- 만약 props 나 state 같은 상태값이 없다면 JSX 에 기반해 렌더링이 일어나게 된다.

### 2.4.2 리액트의 렌더링이 일어나는 이유

- 렌더링이 언제 발생하는 지가 중요
- 발생 시나리오
  1. **최초 렌더링**
  2. **리렌더링 -** 리렌더링이 발생하는 경우
     - **클래스 컴포넌트의 setState 가 실행되는 경우**
     - **클래스 컴포넌트의 forceUpdate 가 실행되는 경우**
     - **함수 컴포넌트의 useState 의 setter 가 실행되는 경우 → 상태가 업데이트 되는 경우**
     - **함수 컴포넌트의 useReduceer 의 dispatch 가 실행되는 경우 → 상태가 업데이트 되는 경우**
     - **컴포넌트의 key props 가 변경되는 경우 → key 의 변화는 리렌더링을 야기한다. 원래 목적은 key 가 같으면 불필요한 렌더링을 하지 않기 위함. → 🙀 리렌더링이 발생하면 current 트리와 workInProgress 트리 사이에서 어떠한 컴포넌트가 변경이 있었는지 구분해야 하는데, 이 두 트리 사이에서 같은 컴포넌트인지 구분하는 값이 바로 key 이다. → ❗만약 key 가 없다면 단순히 파이버 내부의 sibling 인덱스만을 기준으로 판단하게 된다.**
       ![image.png](attachment:da5e2bd1-b70d-47ad-81cc-7474ea6b324b:image.png)
     - **props 가 변경되는 경우**
     - **부모 컴포넌트가 렌더링될 경우**
- 위의 시나리오에 한해서만 렌더링이 발생한다.
- 단순한 변수는 변경되도 렌더링 되지 않는다.
- 이것도 재밌는 사실
  ![image.png](attachment:a92476ec-55d5-4e1b-9caa-cd10d9ec542c:image.png)

### 2.4.3 리액트의 렌더링 프로세스

- 어떤 과정을 거쳐 렌더링이 수행되는지 살펴보자.
- **렌더링 프로세스가 시작되면 리액트는 컴포넌트의 루트에서부터 아래쪽으로 내려가면서 업데이트가 필요하다고 지정되어 있는 모든 컴포넌트를 찾는다.**
- **만약 업데이트가 필요하다고 지정돼 있는 컴포넌트를 발견하면 클래스 컴포넌트의 경우에는 클래스 내부의 render 함수를 실행하게 되고, 함수 컴포넌트의 경우에는 FunctionComponenet 자체를 호출하여 결과에 저장한다.**
- **리액트의 결과물을 JSX 문법으로 되어 있고, react.createElement 를 호출하는 구문으로 변환된다.**
  ![image.png](attachment:1727e55f-2773-4f14-acf8-dca46fa99b33:image.png)
- 렌더링 프로세스가 실행되면서 각 컴포넌트의 렌더링 결과물을 수집한 다음, 리액트의 새로운 트리인 가상 DOM 과 비교해 실제 DOM 에 반영하기 위한 모든 변경 사항을 차례차례 수집한다.
- 이렇게 계산하는 과정이 리액트 파이버의 재조정이다. 이 과정이 끝나면 모든 변경 사항을 하나의 동기 시쿼스로 DOM 에 적용해 변경된 결과물이 보이게 된다.
- 주목해야 할 점을 렌더와 커밋 단계로 실행된다는 점이다.

### 2.4.4 렌더와 커밋

- **렌더 단계** : 컴포넌트를 렌더링하고 변경 사항을 계산하는 모든 작업을 의미한다. 즉, 렌더링 프로세스에서 컴포넌트를 실행해 이 결과와 이전 가상 DOM 을 비교하는 과정을 거쳐 변경이 필요한 컴포넌트를 체크하는 단계이다. → **크게 type, props , key 를 비교한다. 이 중 변경된 것이 있으면 변경이 필요한 컴포넌트로 체크한다.**
- **커밋 단계** : 렌더 단계의 변경 사항을 실제 DOM 에 적용해 사용자에게 보여주는 과정을 말한다. → **이 단계가 끝나야 브라우저의 렌더링이 발생한다.**
- 😒 리액트가 먼저 DOM 을 커밋 단계에서 업데이트한다면 이렇게 만들어진 모든 DOM 노드 및 인스턴스를 가리키도록 리액트 내부의 참조를 업데이트한다. componentDidmount, componentDidUpdate 메서드 호출, useLayoutEffect 훅 호출??
- **❗여기서 중요한 부분 렌더링이 일어난다고 해서 무조건 DOM 업데이트가 일어나는 것은 아니다. → 변경 사항을 계산했는데 아무런 변경 사항이 감지되지 않는다면 커밋 단계는 생략될 수 있다.**
- 즉, 렌더링은 가시적인 업데이트가 일어나지 않아도 발생할 수 있지만, 변경 사항이 없다면 DOM 업데이트가 일어나지 않을 수 있다.
- 사실 그림이 더 이해가 안됨.
  ![image.png](attachment:7a762bfb-938a-441f-9ee4-ac4ea2528e24:image.png)
- 이런 두 과정은 리액트 렌더링은 항상 동기적으로 작동했다. 그렇기에 렌더링 과정이 길어질수록 애플리케이션 성능 저하로 이루어졌다.
- 어찌보면 당연한 것……
- **❗그러다가 리액트 18버전에 의도적으로 우선순위로 컴포넌트를 렌더링해 최적화할 수 있는 비동기 렌더링 → 동시성 렌더링이 도입되었다!**
- 동시성 렌더링은 렌더링 중 렌더 단계가 비동기로 작동해 특정 렌더링의 우선순위를 낮추거나, 필요하다면 중단하거나 재시작하거나 포기할 수 있다. 이를 통해 브라우저의 동기 작업을 차단하지 않고 백그라운드에서 새로운 리액트 트리를 준비할 수 있으므로 매끄러운 사용자 경험을 누릴 수 있다.

### 2.4.5 일반적인 렌더링 시나리오 살펴보기

- 예제를 바탕으로 살펴보자
  ```tsx
  import { useState } from "react";

  export default function A() {
    return (
      <div className="App">
        <h1>Hello React!</h1>
        <B />
      </div>
    );
  }

  function B() {
    const [counter, setCounter] = useState(0);

    function handleButtonClick() {
      setCounter((previous) => previous + 1);
    }

    return (
      <>
        <label>
          <C number={counter} />
        </label>
        <button onClick={handleButtonClick}>+</button>
      </>
    );
  }

  function C({ number }) {
    return (
      <div>
        {number} <D />
      </div>
    );
  }

  function D() {
    return <>리액트 재밌다!</>;
  }
  ```
- 여기서 B 의 버튼을 눌러보자.
- 순서
  ![image.png](attachment:a9b6e0eb-2f0a-4540-8bf1-85ec7d899ec8:image.png)
- 렌더링을 피하는 조치가 있지 않은 한 하위 컴포넌트에 영향을 미친다. 부모가 변경됐다면 props 가 변경됐는지와 상관없이 무조건 자식 컴포넌트도 리렌더링된다.
- **❗그렇다면 memo 를 사용한다면! → 당연히 렌더링이 일어나지 않는다. → 🌟 정확히는 렌더 단계에서 비교를 거쳤지만 memo 로 선언하여 props 가 변경되지 않았기 때문에 렌더링이 생략되어 커밋 단계도 생략된 것이다.**

### 2.4.6 정리

- 렌더링 시나리오를 정확히 이해하여 컴포넌트의 트리 구조를 개선하거나 불필요한 렌더링 횟수를 줄임으로서 성능 좋은 리액트 웹 애플리케이션을 만들어보자!

# 2.5 컴포넌트와 함수의 무거운 연산을 기억해 두는 메모이제이션

---

- 메모이제이션 최적화는 리액트 커뮤니티 오랜 논쟁 주제 중 하나이다.

### 2.5.1 주장 1 : 섣부른 최적화는 독이다. 꼭 필요한 곳에서만 메모이제이션을 추가하자

- 결국 메모이제이션도 비용이 드는 작업이기에 신중해야 한다.
- 애초에 개발자에게 선택권을 쥐어줬다는 점에서 메모이제이션이 은탄환은 아니라는 의미이다.
- 섣불리 최적화 하는 것은 옳지 못하며, 메모이제이션을 활용한 최적화는 신중을 가해야 한다.
- 미리 개발자가 렌더링이 많이 될 부분을 예측해 메모이제이션하는 섣부른 최적화는 옳지 못하다.
- 필요한 곳에서만 최적화 하는 것이 옳다.

### 2.5.2 주장 2 : 렌더링 과정의 비용은 비싸다. 모조리 메모이제이션해 버리자

- 먼저 두 주장에서 공통으로 깔고 가는 전제는 다음과 같다. → **❗일부 컴포넌트에서는 메모이제이션을 하는 것이 성능에 도움이 된다. 섣부른 최적화인지 여부에는 관계없이, 만약 해당 컴포넌트가 렌더링이 자주 일어나며 그 렌더링 사이에 비싼 연산이 포함되어 있고, 심지어 자식 컴포넌트 또한 많다면 memo 나 다른 메모이제이션을 사용하는 것이 이점이 있을 때가 분명히 있다.**
- 두 가지 선택권이 있다.
  - memo를 컴포넌트의 사용에 따라 잘 살펴보고 일부에만 적용하는 방법
  - memo를 일단 그냥 다 적용하는 방법
- 첫 번째 주장이 이상적이긴 한데…. 그것에 시간이 쏟을 시간이 생각보다는 많지 않다.
- 잘못된 memo 로 지불해야 하는 비용은 얕은 비교가 발생하면서 지불해야 하는 비용이다. 어차리 이전 결과물을 저장하기 때문에 추가로 지불할 비용은 props 에 대한 얕은 비교 뿐이다.
- 반면에 memo를 사용하지 않으면 어떤가?
  - 렌더링을 함으로써 발생하는 비용
  - 컴포넌트 내부의 복잡한 로직의 재실행
  - 위 두 가지 모두가 모든 자식 컴포넌트에서 반복적으로 일어남
  - 리액트가 구 트리와 신규 트리를 비교
- 얼핏 봐도 많다…

- memo 이후 useMemo 와 useCallback 에 대해서도 살펴보자.
- ❗**리액트에서 객체는 메모이제이션과 같은 조치가 없다면 렌더링 될 때 마다 재생성되서 참조가 달라지게 된다. 이걸 useEffect 의 의존성 배열에 넣으면 어떻게 될까?**
  ```tsx
  function useMath(number: number) {
    const [double, setDouble] = useState(0);
    const [triple, setTriple] = useState(0);

    useEffect(() => {
      setDouble(number * 2);
      setTriple(number * 3);
    }, [number]);

    return { double, triple };
  }

  export default function App() {
    const [counter, setCounter] = useState(0);
    const value = useMath(10);

    useEffect(() => {
      console.log(value.double, value.triple);
    }, [value]); // 값이 실제로 변한 건 없는데 계속해서 console.log가 출력된다.

    function handleClick() {
      setCounter((prev) => prev + 1);
    }

    return (
      <>
        <h1>{counter}</h1>
        <button onClick={handleClick}>+</button>
      </>
    );
  }
  ```
- 인수로 넘겨주는 number 값이 변하지 않는 이상 같은 값을 가지고 있어야 하는데도 계속해서 호출이 되는 것을 볼 수 있다.
  ```tsx
  function useMath(number: number) {
    const [double, setDouble] = useState(0);
    const [triple, setTriple] = useState(0);

    useEffect(() => {
      setDouble(number * 2);
      setTriple(number * 3);
    }, [number]);

    return useMemo(() => ({ double, triple }), [double, triple]);
  }
  ```
- 그렇기에 이런식으로 useMemo 를 사용하면 같은 결과물을 가질 수 있고, 사용하는 곳에서 참조 투명성도 유지할 수 있게 된다.
- **🌟 즉 메모이제이션은 자신의 리렌더링 뿐 아니라 사용하는 쪽에서도 변하지 않는 고정값을 사용할 수 있다는 믿음을 줄 수 있다.**
- 이곳에서는 빠뜨렸을 때 치러야 할 위험 비용이 더 크기 때문에 최적화에 대해 확신이 없다면 다 최적화 하자는 의견이다.

### 2.5.3 결론 및 정리

- 두 의견 모두 메모이제이션이 리액트 애플리케이션에서 할 수 있는 최적화라는 것에는 동의
- 그렇다면 정답은 무엇일까? → 정답은 없다.
- 🗨️ **다만 사견입니다.**
- 공부한다면 꼼꼼히 확인하며 경험하기 → 1번
- 현업에서 사용하거나 시간적 여유가 없다면 의심스러운 곳에는 적용시켜 보기 → 2번
