# JSX란

표현하고 싶은 다양한 것들을 트리 구조로 작성해두고, 트랜스파일이라는 과정을 거쳐 ECMAScript가 이해할 수 있는 코드로 변경하는 것이 목표

## 정의

- JSXElement
    - JSX를 구성하는 가장 기본적인 요소
    - JSXOpeningElement, JSXClosingElement, JSXSelfClosingElement, JSXFragment 중 하나의 형태여야함
    - JSXElementName
        - JSXElement의 요소 이름으로 쓸 수 있는 것
        - JSXIdentifier: 자바스크립트의 식별자 규칙과 동일
        - JSXNamespacedName: `JSXIdentifier:JSXIdentifier` 의 조합
        - JSXMemberExpression: `JSXIdentifier.JSXIdentifer` 의 조합
- JSXAttributes
    - JSXElement에 부여할 수 있는 속성
    - JSXSpreadAttributes: 전개 연산자와 동일한 역할 `{…AssignmentExpression}`
    - JSXAttribute: 속성을 나타내는 키와 값으로 짝을 이루어서 표현
        - JSXAttributeName: 속성의 키
        - JSXAttributeValue: 속성의 키에 할당할 수 있는 값
- JSXChildren
    - JSXElement의 자식 값
    - JSXChild
        - JSXChildren을 이루는 기본 단위
        - JSText: { < > } 를 제외한 문자열
        - JSXElement
        - JSXFragment
- JSXStrings

## JSX는 어떻게 자바스크립트에서 변환될까?

`@babel/plugin-transform-react-js` 플러그인이 변환함

```jsx
<div required={true}>Hello World</div>

// to
var Component = React.createElement(
	'div', // JSXElement
	{ required: true }, // JSXAttributes
	'Hello World' // JSXChildren
)

```

# 가상 DOM과 리액트 파이버

## DOM과 브라우저 렌더링 과정

### DOM Document Object Model

웹 페이지에 대한 인터페이스

웹 페이지의 콘텐츠와 구조를 어떻게 보여줄지에 대한 정보를 담고 있다

### 렌더링 과정

1. 요청한 주소를 방문해 HTML 파일을 다운로드
2. 브라우저의 렌더링 엔진은 HTML을 파싱 → DOM Tree를 만듦
3. CSS 파일을 다운로드
4. 렌더링 엔진이 CSS를 파싱 → CSSOM Tree를 만듦
5. 브라우저가 DOM 트리를 순회, `display: none` 같은 요소는 방문하지 않음
6. 5번에서 순회한 노드들을 대상으로 해당 노드에 대한 CSSOM 정보를 찾고 스타일을 적용
    1. layout, reflow: 각 노드가 브라우저 화면의 어느 좌표에 나타나야 하는지 계산, 반드시 페인팅 과정으로 이어짐
    2. painting: layout과정을 거친 노드에 색 등을 입힘

## 가상 DOM 의 등장 배경

레이아웃과 리페인팅의 비용이 높음 - 특히 SPA에서 빈도가 높음

사용자의 인터랙션 하나하나마다 DOM의 변화를 추적하기보다 결과적으로 만들어지는 DOM 결과물만 아는 것이 효율적

따라서, 리액트가 관리하는 가상 DOM이 등장, DOM 계산을 브라우저가 아닌 메모리에서 진행하기 때문에 실제로는 여러 번 발생했을 렌더링 과정을 최소화할 수 있음

일반적인 DOM 관리 방식보다 빠르지는 않음 → 애플리케이션을 개발할 수 있을 만큼의 합리적인 속도

## 리액트 파이버 Fiber

- 리액트의 파이버 재조정자 reconciler가 관리하는 자바스크립트 객체
- 재조정 Reconciliation
    - 리액트에서 어떤 부분을 새롭게 렌더링해야 하는지 가상 DOM과 실제 DOM을 비교하는 작업
- 리액트 파이버의 목표는 애플리케이션에서 발생하는 애니메이션, 레이아웃, 그리고 사용자 인터랙션에 올바른 결과물을 만드는 반응성 문제를 해결하는 것
    - 작업을 작은 단위로 분할한 다음 우선순위를 매김
    - 작업을 일시중지하거나 다시 시작할 수 있음
    - 이전에 했던 작업을 폐기할 수 있음
- 과거에는 모든 조정 알고리즘이 스택을 기반으로 동기적으로 이뤄졌으나, 파이버를 활용한 조정 알고리즘은 모든 과정이 비동기로 일어남
- 파이버는 하나의 작업 단위로 구성됨 → 작업을 하나씩 처리한 후 `finishedWork()` 라는 작업을 마무리 → 작업을 커밋해서 실제 DOM에 변경사항을 만듦
    - 렌더 단계: 사용자에게 노출되지 않는 모든 비동기 작업 수행. 이 단계에서 파이버의 작업(우선순위 지정, 작업 중지/폐기) 등의 작업이 일어남
    - 커밋 단계: DOM에 실제 변경 사항을 반영, `commitWork()` 가 실행되는데, 이 과정은 동기식으로 일어나고 중단될 수 없음
- 파이버는 컴포넌트가 최초로 마운트되는 시점에 생성된 후 가급적 재사용됨

### 리액트 파이버의 주요 속성

- tag
    - 파이버는 하나의 element에 하나가 생성되는 1:1 관계를 가짐 → 이 때 매칭된 정보가 바로 tag
    - 리액트의 컴포넌트, HTML의 DOM 노드 또는 다른 것일 수 있음
- stateNode
    - 파이버 자체에 대한 참조 정보
- child, sibling, return
    - 파이버 트리 형식을 구성하는 데 필요한 정보
- index
    - sibling 사이에서 자신의 위치
- pendingProps
    - 아직 작업을 처리하지 못한 props
- memoizedProps
    - 렌더링이 완료된 이후에 pendingProps를 memoizedProps로 저장해 관리
- updateQueue
    - 상태 업데이트, 콜백 함수, DOM 업데이트 등 필요한 작업을 담아두는 큐
- memoizedState
    - 함수형 컴포넌트의 훅 목록 (useState, useEffect 등)
- alternate
    - 반대편 트리 파이버를 가리킴

### 리액트 파이버 트리

현재 모습을 담은 파이버 트리와 작업 중인 상태를 나타내는 workInProgress 트리가 있음

더블 버퍼링을 통해 리액트 파이버의 작업이 끝나면 단순히 포인터만 변경해서 workInProgress 트리를 현재 트리로 바꿈

### 파이버의 작업 순서

```jsx
<A1>
	<B1>Hello</B1>
	<B2>
		<C1>
			<D1 />
			<D2 />
		</C1>
	</B2>
	<B3 />
</A1>

```

1. A1.beginWork()
2. B1.beginWork()
3. B1.completeWork()
4. B2.beginWork()
5. C1.beginWork()
6. D1.beginWork()
7. D1.completeWork()
8. D2.beginWork()
9. D2.completeWork()
10. C1.completeWork()
11. B2.completeWork()
12. B3.beginWork()
13. B3.completeWork()
14. A1.completeWork()
15. commitWork()

## 파이버 정리

가상 DOM과 파이버는 단순히 브라우저의 DOM을 직접 변경하는 것보다 빨라서 만들어진 게 아니다

개발자가 직접 DOM을 하나하나 변경해야 한다면 어떤 값이 바뀌었는지, 그 값에 따라 또 어떤 값이 변경되는지 등을 파악하기가 매우 어려움 → 파이버와 재조정자가 내부 알고리즘을 통해 관리함으로써 애플리케이션을 효율적으로 유지보수할 수 있음

가상 DOM과 리액트의 핵심: 값으로 UI를 표현하는 것

# 클래스형 컴포넌트와 함수형 컴포넌트

훅이 등장한 이후 부터 함수형 컴포넌트가 쓰이기 시작함 → 보일러플레이트 코드가 줄어듬

## 클래스형 컴포넌트

```jsx
interface Props { ... }
interface State { ... }

class SampleComponent extends React.Component<Props, State> {
	private constructor(props: Props) {
		super(props);
		this.state = { ... }
	}

	private handleClick = () => {
		...
	}

	public render() { ... }
}

```

- constructor
    - 컴포넌트가 초기화 되는 시점에 호출됨
    - ES2022부터는 클래스 필드로 State 초기화 가능
- 메서드
    - 렌더링 함수 내부에서 사용되는 함수, 만드는 방식은 크게 3가지
        - constructor에서 this 바인딩
            
            ```jsx
            contructor() {
            	this.handleClick = this.handleClick.bind(this)
            }
            
            ```
            
        - 화살표 함수
        - 렌더링 함수 내부에서 함수를 새로 만들어 전달

### 생명주기 메서드

- 생명주기 메서드가 실행되는 시점
    - 마운트
    - 업데이트
    - 언마운트
- `render()`
    - 컴포넌트가 UI를 렌더링할 때 ( 마운트, 업데이트 )
    - 항상 순수해야하고 사이드이펙트가 없어야 함 → `this.setState` 금지
- `componentDidMount()`
    - 컴포넌트가 마운트되고 준비된 즉시 실행
    - `this.setState()` 가능 → 즉시 리렌더링되는데, 이 작업은 브라우저가 실제로 UI를 업데이트하기 전에 실행되어 사용자는 눈치챌 수 없음
    - 성능 문제를 일으킬 수 있음 → API 호출 후 State 업데이트 또는 DOM에 의존적인 작업 용도로만 사용
- `componentDidUpdate()`
    - 컴포넌트 업데이트 이후 즉시 실행
    - `this.setState()` 가능하지만 무한 호출 가능성 있음
- `componentWillAmount()`
    - 언마운트 직전에 호출
    - 메모리 누수나 불필요한 작동을 막기 위한 클린업 함수 호출 용도
    - `this.setState()` 금지
- `shouldComponentUpdate()`
    - state나 props의 변경으로 컴포넌트가 리렌더링되는 것을 막고 싶을 때 사용
    - return 값이 `false`면 리렌더링하지 않음
    - 컴포넌트가 `Component`를 `extends`하면 `setState()` 가 호출될 때 마다 리렌더되지만, `PureComponent`를 `extends`하면 state와 props에 대해 `shallowEqual()`을 수행함 → `Component` 를 `extends` 했다면, 해당 메서드를 재정의해서 성능 최적화 가능
- `static getDerivedStateFromProps(nextProps, prevState)`
    - `render()`를 호출하기 직전에 호출
    - 반환되는 객체의 내용은 모두 state로 들어감, `null` 반환 시 아무 영향 없음
    - 다음에 올 props를 바탕으로 현재의 state를 변경하고 싶을 때 사용
- `getSnapShotBeforeUpdate()`
    - DOM이 업데이트 되기 직전에 호출
    - 반환된 값은 `componentDidUpdate`로 전달
    - 해당 메서드부터 밑의 메서드들은 훅으로 구현되어 있지 않음
- `static getDerivedStateFromError()`
    - 자식 컴포넌트에서 에러가 발생했을 때 호출되는 에러 메서드 ( render 단계에서 호출 )
    - 에러 발생시 처리할 state 값을 반환해야 함
    - state 반환 이외의 모든 작업은 `componentDidCatch` 에서 처리
- `componentDidCatch()`
    - 자식 컴포넌트에서 에러가 발생했을 때, `getDerivedStateFromError` 이후에 호출 ( commit 단계에서 호출 )
    - 개발 모드에서는 에러가 발생하면 window까지 전파되지만, 프로덕션 모드에서는 해당 메서드로 잡히지 않은 에러만 window까지 전파됨
        
        ```jsx
        useEffect(() => {
        	function handleError() { ... }
        
        	// 개발 모드에서는 모든 에러에 대해 실행됨
        	window.addEventListener('error', handleError);
        }, [])
        
        ```
        

### 클래스형 컴포넌트의 한계

- 데이터의 흐름을 추적하기 어려움
    - 메서드가 많고 순서대로 작성되지 않을 수 있음
- 로직의 재사용이 어려움
- 컴포넌트 내부 로직이 많아질수록 크기가 커짐
- 자바스크립트 클래스의 어려움
- 코드 크기 최적화의 어려움
- 핫리로딩이 상대적으로 불리함

## 함수형 컴포넌트 vs 클래스형 컴포넌트

### 생명주기 메서드의 부재

`useEffect`를 활용해 `componentDidMount`, `componentDidUpdate`, `componentWillAmount`를 비슷하게 구현 가능 → 똑같지는 않음

### 함수형 컴포넌트의 렌더링 된 값

함수형 컴포넌트는 렌더링된 값을 고정하고, 클래스형 컴포넌트는 그렇지 못함

```jsx
// 3초 뒤 props를 출력하는 코드
handleClick = () => {
	setTimeout(..., 3000)
}

```

클래스형 컴포넌트의 경우 3초 내에 props가 변경되면 변경된 props를 출력하고, 함수형 컴포넌트는 변경되기 전 props를 출력함

클래스 내에서 `this`가 가리키는 객체는 컴포넌트의 인스턴스이고, 인스턴스의 멤버는 변경 가능한 값이기 때문에 생명주기 메서드가 변경된 props를 읽을 수 있음

# 렌더링

## 리엑트의 렌더링

컴포넌트들이 현재 자신들이 가지고 있는 props와 state의 값을 기반으로 어떻게 UI를 구성하고 이를 바탕으로 어떤 DOM 결과를 브라우저에 제공할 것인지 계산하는 일련의 과정

## 리엑트 렌더링이 발생하는 시점

1. 최초 렌더링
    1. 처음 애플리케이션 진입
2. 리렌더링
    1. 클래스형 컴포넌트의 `setState` 실행
    2. 클래스형 컴포넌트의 `forceUpdate` 실행
    3. 함수형 컴포넌트의 `setState` 실행
    4. 함수형 컴포넌트의 `useReducer()`의 두 번째 배열 요소인 `dispatch` 실행
    5. 컴포넌트의 `key` props가 변경
    6. props가 변경
    7. 부모 컴포넌트가 렌더링

## 리엑트의 렌더링 프로세스

1. 루트 컴포넌트부터 차례대로 업데이트가 필요하다고 지정되어 있는 컴포넌트를 탐색
2. 클래스형 컴포넌트라면 `render()`를, 함수형 컴포넌트라면 그 자체를 호출한 뒤 결과물을 저장
3. 가상 DOM과 비교해 실제 DOM에 반영하기 위한 모든 변경 사항을 수집 → Reconciliation
4. 모든 변경 사항을 하나의 동기 시퀀스로 DOM에 적용

## 렌더와 커밋

### 렌더

컴포넌트를 렌더링하고 변경 사항을 계산하는 모든 작업

컴포넌트를 실행한 결과와 이전 가상 DOM을 비교하는 과정을 거쳐 변경이 필요한 컴포넌트를 체크하는 단계

type, props, key를 비교해서 하나라도 변한 것이 있으면 변경이 필요한 컴포넌트로 체크

### 커밋

변경 사항을 실제 DOM에 적용 → 브라우저의 렌더링

클래스형 컴포넌트의 `componentDidMount`, `componentDidUpdate` 호출

함수형 컴포넌트의 `useLayoutEffect` 훅 호출

렌더링을 수행했으나 커밋 단계까지 수행되지 않았다면 생략될 수 있음 → 리엑트 렌더링이 반드시 DOM 업데이트로 이어지지 않음

위 두 단계로 이루어진 리액트의 렌더링은 항상 동기식으로 작동했다 → 리액트 18부터 도입된 동시성 렌더링을 통해 사용자 경험 향상

## 일반적인 렌더링 시나리오

```jsx
const { useState } = require("react");

function A() {
  return (
    <div>
      <B />
    </div>
  );
}

function B() {
  const [counter, setCounter] = useState(0);

  return (
    <>
      <C number={counter} />
      <button onClick={() => setCounter((prev) => prev + 1)}>+</button>
    </>
  );
}

function C({ number }) {
  return (
    <>
      <D />
    </>
  );
}

function D() {
  return <>Hello</>;
}

```

1. B 컴포넌트의 `setState` 호출
2. B 컴포넌트의 리렌더링 작업이 렌더링 큐에 들어감
3. 트리 최상단에서부터 렌더링 필요 여부 검사
4. A 컴포넌트는 리렌더링이 필요하지 않으므로 작업하지 않음
5. B 컴포넌트는 리렌더링이 필요하므로 리렌더링
6. C 컴포넌트는 props가 수정되었으므로 리렌더링
7. D 컴포넌트는 리렌더링이 필요하다고 체크되진 않았지만 부모 컴포넌트가 렌더링되므로 리렌더링 → 단 DOM 업데이트로 이어지진 않음

D에 `memo`를 추가하면 리렌더링 대상이 되지 않음

# 메모이제이션

메모이제이션도 비용이 들기 때문에 가벼운 작업은 메모리에서 다시 꺼내오는 것보다 매번 수행해 결과를 반환하는 것이 빠를 수 있음

일단 애플리케이션을 만든 후 어떻게 렌더링이 일어나는 지 확인하고 필요한 곳에서만 최적화

vs

메모이제이션을 하지 않았을 때보다 했을 때 더 많은 이점을 누릴 수 있다

위험 비용이 더 크기 때문에 확신이 없다면 가능한 한 모든 곳에 메모이제이션을 활용한 최적화를 하는 것이 좋다

=

시간적 여유가 있다면 메모이제이션을 지양하고 성능 분석 후 최적화 적용,

시간적 여유가 없다면 메모이제이션 적극 활용

props로 넘겨주는 변수들은 `useMemo`, `useCallback` 처리 등
