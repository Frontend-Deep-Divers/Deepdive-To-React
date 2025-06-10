# 3장 리액트 훅 깊게 살펴보기

# 3.1 리액트의 모든 훅 파헤치기

## 3.1.1 useState

함수 컴포넌트 내부에서 상태를 정의하고, 상태를 관리할 수 있게 해주는 훅입니다.

`useState`는 내부적으로 클로저를 이용하고 있습니다. 이를 통해 `useState`의 실행이 종료된 이후에도 지역변수인 state를 계속 참조할 수 있습니다.

### useState 구현

대략적으로 흉내 낸 코드는 다음과 같습니다.

```js
const MyReact = (function () {
  const global = {};
  let index = 0;

  function useState(initialState) {
    if (!global.states) {
      // 앱 전체의 states 배열 초기화
      // 최초 접근이라면 빈 배열로 초기화
      global.states = [];
    }
  }

  // states 정보를 조회해서 현재 상태값이 있는지 확인
  // 없으면 초깃값으로 설정
  const currentState = global.states[index] || initialState;
  global.states[index] = currentState;

  // 즉시 실행 함수로 setter를 생성
  const setState = (function () {
    // 현재 index를 클로저로 생성해서 동일 index에 접근 가능
    let currentIndex = index;
    return function (value) {
      global.states[currentIndex] = value;
      // 컴포넌트 렌더링 시작
    };
  })();
  // useState를 사용할 때마다 index를 하나씩 추가하며 index는 setState에서 사용
  // 하나의 state마다 index가 할당되어 index가 배열의 값(gloabal.states)을 가리킴
  index = index + 1;

  return [currentState, setState];

  function Component() {
    const [value, setValue] = useState(0);
    // ...
  }
})();
```

실제 구현체와는 다르며, 실제로는 `useReducer`를 이용해 구현돼 있습니다.

### 게으른 초기화

`useState`의 인수로 특정한 값을 넘기는 함수를 인수로 넣어 게으른 초기화를 할 수 있습니다.

```js
const [count, setCount] = useState(() => {
  Number.parseInt(window.localStorage.getItem(cacheKey));
});
```

`useState`의 초깃값이 복잡하거나 무거운 연산을 포함하고 있을 때 사용할 수 있습니다. 함수는 state가 처음 만들어질 때만 사용되며 리렌더링이 발생하면 함수의 실행이 무시됩니다.

`localStorage`나 `sessionStorage`에 대한 접근, `map`, `filter`, `find` 같은 배열에 대한 접근 등 실행 비용이 많이 드는 경우에 유용합니다.

## 3.1.2 useEffect

생명주기 메서드와 비슷한 동작을 하지만 대체하기 위해 만들어진 훅은 아닙니다.

`useEffect`는 앱 내 컴포넌트의 여러 값들을 활용해 동기적으로 부수 효과를 만드는 메커니즘입니다.

`useEffect`는 첫번째 인수로 콜백 함수, 두 번째 인수로 의존성 배열을 받습니다.

`useEffect`는 단순히 렌더링할 때마다 의존성에 있는 값을 보면서 의존성의 값이 변경되어 있으면 부수 효과를 실행하는 평범한 함수입니다.

### 클린업 함수의 목적

```js
useEffect(() => {
  // 이펙트 실행 시 로직

  return () => {
    // 클린업 함수
    // 컴포넌트 언마운트 시 또는 다음 useEffect 실행 직전에 호출
  };
}, []);
```

`useEffect` 내에서 반환되는 함수이며, 이벤트를 등록하고 지울 때 사용해야 하는 함수입니다.

클린업 함수는 이전 state를 참조해 실행됩니다. `useEffect`는 콜백이 실행될 때마다 이전의 클린업 함수가 존재한다면 그 클린업 함수를 실행한 뒤에 콜백을 실행합니다. 이벤트 추가 전, 이전에 등록했던 이벤트를 삭제하는 코드를 클린업 함수에 추가합니다.

클린업 함수는 언마운트와 다르게 의존성 변화가 있었을 당시 이전 상태를 청소해 주는 개념에 가깝습니다.

### 의존성 배열

🤔 `useEffect`는 의존성 배열이 없는 경우, 매 렌더링마다 실행되는데 그럼 `useEffect` 없이 써도 되지 않을까?

`useEffect`는 서버 사이드 렌더링 관점에서 클라이언트 사이드의 실행을 보장하며, 그러므로 `useEffect` 내부에서 `window` 객체의 접근에 의존하는 코드를 사용해도 됩니다. 또한 `useEffect`는 컴포넌트 렌더링이 끝나고 실행되지만, 없이 쓰는 경우 그렇지 않기에 무거운 작업의 경우 렌더링을 방해할 수 있습니다.

```js
const width = window.innerWidth; // 서버에는 window가 없으므로 실행 불가!
```

### useEffect 구현

`useEffect`의 대략적인 구현을 살펴봅시다.

```js
const MyReact = (function () {
  const global = {};
  let index = 0;

  function useEffect(callback, dependencies) {
    const hooks = global.hooks;

    // 이전 훅 정보 확인
    let previousDependencies = hooks[index];

    // 변경됐는지 확인
    // 이전 값이 있으면 얕은 비교로 변경 확인
    // 없으면 변경으로 간주해 실행
    let isDependenciesChanged = previousDependencies
      ? dependencies.some(
          (value, idx) => !Object.is(value, previousDependencies[idx])
        )
      : true;

    // 변경이 일어나면 콜백 함수 실행
    if (isDependenciesChanged) {
      callback();

      // 다음 훅을 대비해 index 추가
      index++;

      // 현재 의존성을 훅에 다시 저장
      hooks[index] = dependencies;
    }

    return { useEffect };
  }
})();
```

리액트는 값을 비교할 때, `Object.is`를 기반으로 얕은 비교를 수행합니다.

⚠️ `useEffect`를 사용할 때 주의할 점

1. 반드시 의존성 배열로 전달한 값의 변경에 의해 실행되게 할 것(콜백 내수에서 의존성 배열에 포함돼 있지 않은 값을 사용하지 말 것): `useEffect`의 사용 의도와도 적합하지 않으며 버그의 위험성을 안게 됩니다.
2. 콜백 함수로 기명 함수를 넘겨줄 것: `useEffect`의 목적을 파악하기 용이합니다.
3. 최대한 간결하게 가볍게 유지할 것: 부수 효과 실행을 담당하므로 앱 성능에 악영향을 미칠 수 있습니다. 예측가능성을 위해 의존성 배열에 여러 변수가 들어간다면 `useCallback`이나 `useMemo` 등 정제한 내용만 `useEffect`에 담아두는 것이 좋습니다.
4. 불필요한 외부함수를 줄일 것: `useEffect` 내에서 사용할 부수 효과라면 내부에서 만들어서 정의하면 간결하게 유지할 수 있습니다.

🤔 `useEffect`에서 콜백으로 비동기 함수를 사용하기 어려운 이유?

바뀐 상태보다 이전 상태가 응답이 더 빨리 온다면 이전 상태 기반으로 결과가 나올 수도 있습니다. 경쟁 상태(race condition)이 발생할 위험이 있기 때문입니다.

즉시 실행 비동기 함수를 만들어 사용할 수도 있습니다. 다만 함수가 생성되고 실행되는 것을 반복하므로 클린업 함수에서 이전 비동기 함수에 대한 처리를 해줘야 합니다. (`AbortController`를 사용하는 등)

```js
useEffect(() => {
  const controller = new AbortController();

  const fetchData = async () => {
    try {
      const res = await fetch("/api/data", { signal: controller.signal });
      const json = await res.json();
      console.log(json);
    } catch (err) {
      if (err.name === "AbortError") {
        console.log("요청 중단됨");
      }
    }
  };

  fetchData();

  return () => {
    controller.abort(); // 클린업에서 중단
  };
}, []);
```

## 3.1.3 useMemo

비용이 큰 연산에 대한 결과를 저장해두고 이 값을 반환하는 훅입니다.

인수로 어떠한 값을 반환하는 생성 함수와 해당 함수가 의존하는 값의 배열을 받습니다.

## 3.1.4 useCallback

`useMemo`와 다르게 인수로 넘겨받은 콜백 자체를 기억합니다. `useCallback`은 특정 함수를 새로 만들지 않고 재사용하는 훅입니다.

`useMemo`와의 유일한 차이는 메모이제이션 대상이 값이냐 함수냐일 뿐입니다.

## 3.1.5 useRef

`useRef`는 `useState`와 동일하게 컴포넌트 내부의 렌더링이 일어나도 변경 가능한 상태값을 저장합니다.

그러나 구별되는 두 가지 차이점이 있습니다.

1. `useRef`는 반환값인 객체 내부에 있는 `current`로 접근 또는 변경할 수 있다.
2. `useRef`는 그 값이 변하더라도 렌더링을 발생시키지 않는다.

`useRef`는 컴포넌트가 렌더링될 때만 생성되며, 컴포넌트 인스턴스가 여러 개라도 각각 별개의 값을 바라봅니다.

일반적으로 DOM에 접근할 때 가장 많이 사용됩니다.

렌더링을 발생시키지 않고 원하는 상태값을 저장할 수 있다는 특징을 활용해, `useState`의 이전 값을 저장하거나 하는 등의 기능을 구현할 때 유용합니다.

### useRef 구현

리액트에서의 구현은 다르지만 Preact에서의 구현에서 힌트를 얻을 수 있습니다.

```js
export function useRef(initialValue) {
  currentHook = 5; // 컴포넌트 내부의 훅 순서
  return useMemo(() => {
    current: initialValue;
  }, []);
}
```

객체 값을 변경해도 객체를 가리키는 주소가 변경되지 않는 JS의 특징을 이용한 방식입니다.

## 3.1.6 useContext

컨텍스트(Context)는 props drilling을 방지하기 위한 개념입니다. 명시적인 props 전달없이 하위 컴포넌트에서 값을 사용할 수 있습니다.

```jsx
const Context = createContext<{ hello: string } | undefined>(undefined);

// 부모 컴포넌트
<>
  <Context.Provider value={{ hello: 'react '}}>
    <ChildComponent />
  </Context.Provider>
</>

...
// 자식 컴포넌트
const value = useContext(Context);
```

`useContext`는 가장 가까운 `Provider`의 값을 가져올 수 있습니다.

다수의 `Provider`나 `useContext`를 사용할 때 별도 함수로 감싸서 사용하는 것이 타입 추론이나 상위 에 `Provider`가 없는 경우에도 사전에 쉽게 찾기 쉽습니다.

⚠️ `useContext`를 사용할 때 주의할점

1. `useContext`를 사용하는 컴포넌트를 최소화할 것
2. 재사용되지 않을 컴포넌트에만 사용할 것: `Provider`에 의존성을 가지게 되므로 아무 곳에나 재사용하기 어려워집니다.
3. 상태 관리 API가 아니라는 것을 명심할 것: 상태를 기반으로 다른 상태를 만들거나, 상태 변화를 최적화할 수 없으므로 상태 관리 라이브러리가 아닙니다.

💡 `useContext`는 단순히 상태를 주입하는 것 이외에는 어떤 기능도 하지 않음

## 3.1.7 useReducer

`useState`와 다르게 좀 더 복잡한 상태값을 미리 정의해 둔 시나리오에 따라 관리할 수 있습니다.

- 반환값
  - `state`: 상태 값을 의미
  - `dispatcher`: `state`를 업데이트하는 함수로, 인수로 상태를 변경할 수 있는 `action`을 넘겨준다.
- 인수
  - 1. `reducer`: 기본 `action`을 정의하는 함수
  - 2. `initialState`: 초깃값을 의미
  - 3. `init`(옵션): 초깃값을 지연 생성시키고 싶을 때 사용하는 함수, 넘겨줄 경우, 게으른 초기화를 발생시킨다.

`useReducer`는 상태 접근은 컴포넌트에서만, 상태 업데이트 방법은 컴포넌트 외부로 분리하고 미리 정의한 `dispatcher`에 의해서만 가능하게 합니다. 복잡한 상태 업데이트 시나리오를 일목요연하게 파악할 수 있는 장점이 있습니다.

```jsx
// useReducer가 사용할 state를 정의
type State = {
  count: number,
};

// state의 변화를 발생시킬 action의 타입과 넘겨줄 값(payload)을 정의
// 각 type과 payload값은 별개의 타입으로 명료도 얻으며, 굳이 객체를
// 다같이 타입화된 네이밍이 가장 명료함
type Action = { type: "up" | "down" | "reset", payload?: State };

// 무거운 연산이 포함된 경우로 초기화 함수
function init(count: State): State {
  return count;
}

// 초기값
const initialState: State = { count: 0 };

// 앞서 선언한 state와 action을 기반으로 state가 어떻게 변경될지 정의
function reducer(state: State, action: Action): State {
  switch (action.type) {
    case "up":
      return { count: state.count + 1 };
    case "down":
      return { count: state.count - 1 > 0 ? state.count - 1 : 0 };
    case "reset":
      return init(action.payload || { count: 0 });
    default:
      throw new Error(`Unexpected action type ${action.type}`);
  }
}

export default function App() {
  const [state, dispatcher] = useReducer(reducer, initialState, init);

  function handleUpButtonClick() {
    dispatcher({ type: "up" });
  }

  function handleDownButtonClick() {
    dispatcher({ type: "down" });
  }

  function handleResetButtonClick() {
    dispatcher({ type: "reset", payload: { count: 1 } });
  }

  return (
    <div className="App">
      <h1>{state.count}</h1>
      <button onClick={handleUpButtonClick}>+</button>
      <button onClick={handleDownButtonClick}>-</button>
      <button onClick={handleResetButtonClick}>reset</button>
    </div>
  );
}
```

Preact의 `useState` 코드는 `useReducer`로 구현돼 있습니다. 반대로 `useReducer`를 `useState`로 구현할 수도 있습니다.

```js
const useReducer = (reducer, initialArg, init) => {
  const [state, setState] = useState(
    init ? () => init(initialArg) : initialArg
  );

  const dispatch = useCallback(
    (action) => setState((prev) => reducer(prev, action)),
    [reducer]
  );

  return useMemo(() => [state, dispatch], [state, dispatch]);
};
```

## 3.1.8 useImperativeHandle

부모에게서 넘겨받은 `ref`를 원하는 대로 수정할 수 있는 훅입니다.

`ref`를 하위 컴포넌트로 전달하고 싶을 땐, `forwardRef`를 사용하곤 했습니다. (19 버전부터는 deprecated 되었으며, `ref`를 일반 props처럼 받을 수 있게 되었습니다.)

19 버전 이전에는 예약어인 `ref`는 props로 쓸 수 없었으며, `ref`대신 다른 이름으로 받을 수 있고, 일관성 있는 `ref` 전달을 위한 API인 `forwardRef`를 제공했었습니다. 이를 자식 컴포넌트에서 감싸서 `ref`를 받는 식으로 사용할 수 있었습니다.

```jsx
import React, {
  useRef,
  useState,
  useImperativeHandle,
  forwardRef,
} from "react";

const Input = forwardRef((props, ref) => {
  // useImperativeHandle을 사용하면 ref의 동작을 추가로 정의할 수 있다.
  useImperativeHandle(
    ref,
    () => ({
      alert: () => alert(props.value),
    }),
    // useEffect의 deps와 같다.
    [props.value]
  );

  return <input ref={ref} {...props} />;
});

function App() {
  // input에 사용할 ref
  const inputRef = useRef();

  // input의 value
  const [text, setText] = useState("");

  function handleClick() {
    // inputRef에 추가한 alert라는 동작을 사용할 수 있다.
    inputRef.current.alert();
  }

  function handleChange(e) {
    setText(e.target.value);
  }

  return (
    <>
      <Input ref={inputRef} value={text} onChange={handleChange} />
      <button onClick={handleClick}>Focus</button>
    </>
  );
}

export default App;
```

전달받은 `ref`에 추가적인 동작을 정의할 수 있습니다.

## 3.1.9 useLayoutEffect

`useEffect`와 다르게, 모든 DOM 의 변경 후에 콜백 함수 실행이 동기적으로 발생합니다. 브라우저에 변경 사항이 실제로 반영되기 전에 콜백 함수가 실행된다는 것을 의미합니다.

**DOM은 계산됐지만 화면에 반영하기 전에 하고 싶은 작업이 있을 때 사용합니다.** 특정 요소에 따라 DOM 요소를 기반으로 한 애니메이션, 스크롤 위치 제어 등 화면에 반영되기 전에 하고 싶은 작업에 사용한다면, `useEffect`보다 자연스러운 사용자 경험을 제공할 수 있습니다.

## 3.1.10 useDebugValue

디버깅하고 싶은 정보를 훅에 사용하면 리액트 개발자 도구에서 볼 수 있습니다.

## 3.1.11 훅의 규칙

1. 컴포넌트 최상단에서만 훅을 호출해야 한다. 반복문이나 조건문, 중첩된 함수 내에서 훅을 실행할 수 없다. 매 렌더링마다 훅의 호출 순서를 보장할 수 없기 때문이다.
2. 리액트 함수 컴포넌트, 혹은 사용자 정의 훅에서만 훅을 호출할 수 있다. 일반 JS 함수에서는 훅을 사용할 수 없다.

# 3.2 사용자 정의 훅과 고차 컴포넌트

리액트에서는 사용자 정의 훅과 고차 컴포넌트를 활용해 재사용 로직을 관리할 수 있습니다.

## 3.2.1 사용자 정의 훅

서로 다른 컴포넌트 내부에서 같은 로직을 공유하고자 할 때 주로 사용합니다.

HTTP 요청하는 `fetch`를 수행하는 사용자 정의 훅 예제입니다.

`use` 접두어를 사용해 리액트 훅임을 명시해 사용하는 것이 권장됩니다.

```ts
import { useEffect, useState } from 'react'

// HTTP 요청을 하는 사용자 정의 훅
function useFetch<T>({
  url,
  method,
  body,
}: { url: string, method, body }: { method: string; body?: XMLHttpRequestBodyInit }) {
  // 응답 결과
  const [result, setResult] = useState<T | undefined>()

  // 요청 중 여부
  const [isLoading, setIsLoading] = useState<boolean>(false)

  // 2xx 3xx로 정상 응답인지 여부
  const [ok, setOk] = useState<boolean | undefined>()

  // HTTP status
  const [status, setStatus] = useState<number | undefined>()

  useEffect(() => {
    const abortController = new AbortController()

    ;(async () => {
      setIsLoading(true)

      const response = await fetch(url, {
        method,
        body,
        signal: abortController.signal,
      })

      setOk(response.ok)
      setStatus(response.status)

      if (response.ok) {
        const apiResult = await response.json()
        setResult(apiResult)
      }

      setIsLoading(false)
    })()

    return () => {
      abortController.abort()
    }
  }, [url, method, body])

  return { ok, result, isLoading, status }
}
```

사용자 정의 훅은 리액트 커뮤니티에도 레퍼런스가 많은데, useHooks(https://usehooks.com/), react-use(https://github.com/streamich/react-use), ahooks(https://ahooks.js.org/) 등이 있습니다.

## 3.2.2 고차 컴포넌트

고차 컴포넌트는 JS의 일급 객체, 함수의 특징을 활용한 로직 재사용 방법이므로, JS 환경에서는 널리 쓰이는 방식입니다. 대표적인 리액트의 고차 컴포넌트로 `React.memo`가 있습니다.

`React.memo`는 props의 변화가 없음에도 리렌더링되는 현상을 방지하기 위해 사용됩니다. 이전 props와 비교해 이전과 같다면 렌더링 자체를 생략하고, 메모이제이션된 컴포넌트를 반환합니다. 앞서 봤던 `PureComponent`와 유사한 방식입니다.

고차 컴포넌트는 컴포넌트의 결과물에 영향을 미칠 수 있는 공통된 작업을 처리하는 데 유용합니다.

```ts
interface LoginProps {
  loginRequired?: boolean;
}

function withLoginComponent<T>(Component: ComponentType<T>) {
  return function (props: T & LoginProps) {
    const { loginRequired, ...restProps } = props;

    if (loginRequired) {
      return <>로그인이 필요합니다.</>;
    }

    return <Component {...(restProps as T)} />;
  };
}

// 원래 구현하고자 하는 컴포넌트를 만들고, withLoginComponent로 감싸기만 하면 끝이다.
// 로그인 여부, 로그인이 안 되면 다른 컴포넌트를 렌더링하는 책임은 모두
// 고차 컴포넌트인 withLoginComponent에 맡길 수 있어 매우 편리하다.
const Component = withLoginComponent(({ props }: { value: string }) => {
  return <h3>{props.value}</h3>;
});

export default function App() {
  // 로그인 관련 정보를 가져온다.
  const isLogin = true;
  return <Component value="text" loginRequired={isLogin} />;
  // return <Component value="text" />;
}
```

고차 컴포넌트는 `with`라는 접두어를 붙여 선언합니다. ESLint 등에 규칙이 있는 건 아니지만 리액트 커뮤니티에서 관습적으로 사용되어 온 네이밍입니다.

고차 컴포넌트를 사용할 때 주의할 점:

1. 컴포넌트의 props를 임의로 수정, 추가, 삭제하여 부수 효과를 일으키지 말 것: 컴포넌트를 사용하는 입장에서 props가 변경되어 예기치 못한 동작을 일으킬 수 있다는 부담감을 주는 것은 좋지 않습니다.
2. 최소한으로 사용할 것: 여러 개의 고차 컴포넌트를 감싸면 복잡성이 커지게 됩니다.

## 3.2.3 사용자 정의 훅과 고차 컴포넌트 중 무엇을 써야 할까?

### 사용자 정의 훅이 필요한 경우

단순히 리액트에서 제공하는 훅으로 공통 로직을 관리할 수 있다면 사용자 정의 훅을 사용하는 것이 좋습니다. 사용자 정의 훅 자체는 컴포넌트 렌더링에 영향을 주지 않아, 컴포넌트 내부에 미치는 영향을 최소화해 개발할 수 있다는 장점이 있습니다.

컴포넌트 전반에 동일한 로직으로 값을 제공하거나, 특정한 훅의 작동을 넣고 싶다면 사용자 정의 훅을 사용합니다.

### 고차 컴포넌트를 사용해야 하는 경우

특정 상황에서 공통의 대체 컴포넌트를 노출하고자 할 때 유용합니다. 사용자 정의 훅은 컴포넌트가 반환하는 렌더링 결과물에까지 영향을 미치기는 어렵기 때문입니다.

함수 컴포넌트의 반환값, 즉 렌더링의 결과물에도 영향을 미치는 공통 로직은 고차 컴포넌트를 사용하면 매우 유용합니다.
