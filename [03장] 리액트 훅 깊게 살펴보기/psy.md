# 리액트의 모든 훅 파헤치기

## useState

함수형 컴포넌트 내부에서 상태를 정의하고 관리

함수형 컴포넌트는 매번 함수를 실행해 렌더링이 일어나고, 함수 내부의 값은 함수가 실행될 때마다 초기화된다

```jsx
// 실제 구현과는 다름 -> 작동 방식만 흉내냄

// 뭔가 정확하게 알 수 없는 리액트의 동작을 주관하는 객체
const MyReact = function() {
	// State를 비롯한 어떤 데이터든 저장해두는 전역 객체 성격의 객체
  const global = {};
  let index = 0;

  function useState(initialState) {
    if(!global.states) {
      global.states = [];
    }

    const currentState = global.states[index] || initialState;
    global.states[index] = currentState;

    const setState = (function() {
      const currentIndex = index;

      return function(value) {
        global.states[currentIndex] = value;
        // 렌더링하는 함수
      }
    })();

    index++;

    return [currentState, setState];
  }
}
```

### 게으른 초기화

useState의 인수로 특정한 값을 넘기는 함수를 인수로 넣어줄 수 있음

useState의 초깃값이 복잡하거나 무거운 연산을 포함하고 있을 때 사용

함수형 컴포넌트는 렌더링될 때 마다 다시 실행된다. 즉, useState의 인수로 포함된 표현식도 매번 다시 실행된다. 하지만 인수를 함수로 넘기면 초깃값이 없을 때만 실행되므로 연산을 줄일 수 있음

## useEffect

컴포넌트의 여러 값들을 활용해 동기적으로 부수 효과를 만드는 훅

렌더링될 때마다 의존성 배열에 있는 값들을 보면서 이전과 다른 게 하나라도 있으면 실행되는 평범한 함수

### 클린업 함수

useEffect의 콜백 함수에서 반환된 함수는 다음 렌더링이 끝났을 때 실행됨 

useEffect는 콜백이 실행될 때 마다 이전의 클린업 함수가 존재한다면 그 클린업 함수를 실행한 뒤에 콜백을 실행함 → 생명주기 메서드와 다름

### 의존성 배열

의존성 배열이 빈 배열이면 최초 렌더링 직후에 실행된 다음부터는 더 이상 실행되지 않음

의존성 배열이 undefined면 렌더링할 때마다 실행됨

함수형 컴포넌트 내에서 그냥 실행되는 코드는 렌더링 과정 중에 실행되기 때문에 서버 사이드 렌더링 등의 렌더링 과정에서 성능을 떨어뜨림

반면에 useEffect 내에서 실행되는 코드는 렌더링 후에 실행되는 것이 보장되기 때문에 window에 접근할 수 있고, 서버 사이드 렌더링 성능도 떨어지지 않음

컴포넌트가 언마운트 될 때에도 실행됨

### 구현

의존성 배열의 이전 값과 현재 값의 비교는 얕은 비교를 수행 → 하나라도 변경 사항이 있다면 콜백 실행

### 주의사항

- `eslint-disable-line react-hooks/exhaustive-deps` 주석 최대한 자제
- useEffect의 첫 번째 인수에 함수명을 부여
- 거대한 useEffect를 만들지 말 것
- 불필요한 외부 함수를 만들지 말 것 → useEffect 내부에서 즉시 실행 함수로 처리
    
    <aside>
    ⚠️
    
    useEffect의 콜백 인수로 비동기 함수를 넣을 수 없는 이유
    useEffect의 인수로 비동기 함수가 사용되면 이전 state 기반의 결과가 더 늦게 끝나는 현상이 발생할 수 있음 → useEffect의 경쟁 상태
    이벤트 리스너를 처리하듯 내부 비동기 함수에 대해서도 클린업을 해주는 것이 좋음
    
    </aside>
    

## useMemo

비용이 큰 연산에 대한 결과를 메모이제이션

값을 연산하는 데 비용이 많이 든다면 사용

## useCallback

인수로 넘겨받은 콜백 자체를 메모이제이션

useMemo로 구현 가능

## useRef

useState와 동일하게 컴포넌트 내부에서 렌더링이 일어나도 변경 가능한 상태값을 저장

current로 값에 접근 또는 변경 가능

값이 변하더라도 렌더링을 유발하지 않음

함수 외부에서 값을 사용한다면

- 컴포넌트가 렌더링 되지 않아도 메모리를 차지함
- 컴포넌트 여러 개가 같은 값을 참조함

일반적으로 DOM에 접근하고 싶을 때 사용

### usePrevious

```jsx
function usePrevious(value) {
	const ref = useRef();
	
	// 렌더링이 끝난 뒤에 실행되기 때문에,
	// 렌더링 된 시점엔 이전의 value 값을 유지
	useEffect(() => {
		ref.current = value;
	}, [value]);
	
	return ref.current;
}
```

## useContext

### Context란?

Prop 내려주는 과정을 줄이기 위한 개념

```jsx
const MyContext = createContext();

function ContextProvider({ children, text }) {
	return (
		<MyContext.Provider value={{ hello: text }}>{children}</MyContext.Provider>
	)
}

function useMyContext() {
	const context = useContext(MyContext);
	
	if(context === undefined) {
		throw new Error('Provider 외부에서 실행할 수 없음')
	}
}

function Component() {
	const { hello } = useMyContext();
	
	return <>{hello}</>
}

function ParentComponent() {
	return (
		<ContextProvider text="React">
			<Component />
		</ContextProvider>
	)
}
```

useContext를 사용하는 컴포넌트는 재활용이 어려워짐

useContext는 상태 관리 API가 아니라 상태 주입 API

- 어떠한 상태를 기반으로 다른 상태를 만들어 낼 수 없음
- 상태 변화를 최적화할 수 없음 → 부모 컴포넌트가 리렌더링 되면 하위 컴포넌트도 전부 리렌더링됨 → React.memo로 최적화 가능

## useReducer

redux처럼 action을 Reducer에게 넘겨줘서 상태를 변경하는 훅

복잡한 형태의 state를 사전에 정의된 dispatcher로먼 수정할 수 있게 만듦

state에 대한 접근은 컴포넌트에서만, 업데이트하는 방법에 대한 정의는 컴포넌트 외부에

## useImperativeHandle

부모에게서 넘겨받은 ref를 원하는 대로 수정할 수 있는 훅

```jsx
const Input = forwardRef((props, ref) => {
	useImperativeHandle(
		ref,
		() => ({ alert: () => alert(props.value) }),
		[props.value],
	);
	
	...
});

function App() {
	const inputRef = useRef();
	
	function handleClick() {
		inputRef.current.alert();
	}
	
	...
}
```

## useLayoutEffect

useEffect와 유사하게 동작하지만, DOM의 변경(리액트의 렌더링) 후에 동기적으로 발생

1. 리액트의 렌더링
2. useLayoutEffect → 동기적으로 발생하기 때문에 성능 저하
3. 브라우저에 반영
4. useEffect

## useDebugValue

개발 과정에서 사용하는, 디버깅하고 싶은 정보를 리액트 개발자 도구에서 보기 위한 훅

훅 내부에서만 가능, 컴포넌트 레벨에서는 불가능

## 훅의 규칙

1. 최상위에서만 훅을 호출해야 함 ⇒ 19버전 use()는 예외라는데 아닌듯
2. 함수형 컴포넌트 또는 사용자 정의 훅에서만 호출 가능

훅에 대한 정보 저장은 링크드 리스트의 형태로 구현되어 있기 때문에, 순서에 큰 영향을 받음 → 이전 값과의 비교와 실행이 가능해짐

## 사용자 정의 훅과 고차 컴포넌트

단순히 리액트에서 제공하는 훅으로만 공통 로직을 격리할 수 있다면 사용자 정의 훅을 사용

컴포넌트가 반환하는 렌더링 결과물에까지 영향을 미쳐야 할 때는 고차 컴포넌트 사용 → 최소한으로 사용
