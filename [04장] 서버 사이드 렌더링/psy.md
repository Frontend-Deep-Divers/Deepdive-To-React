## 서버 사이드 렌더링이란?

JAM(Javascript, API, Markup)

자바스크립트 리소스의 크기가 증가함 → 클라이언트의 책임 증가 → 로딩 속도 지연

### 장점

1. FCP가 빨라질 수 있음
2. SEO에 유리
3. CLS가 적음
4. 사용자의 디바이스 성능에 비교적 자유로움
5. 보안에 유리

### 단점

1. 서버의 코드를 작성해야 함
2. 서버가 구축되어야 함
3. 서비스 지연 시 사용자에게 피드백을 줄 수 없음 → UX가 안좋아짐

### 싱글 페이지 vs 멀티 페이지

- 가장 뛰어난 싱글 페이지 앱은 가장 뛰어난 멀티 페이지 앱보다 나음
- 평균의 싱글 페이지 앱은 평균의 멀티 페이지 앱보다 느림

### 현대의 서버 사이드 렌더링

두 가지 장점을 모두 취하는 방식

## 서버 사이드 렌더링을 위한 리액트 API

리액트는 Node 환경에서 실행할 수 있는 API를 제공함

초기 렌더링을 빠르게 하기만을 위함

### renderToString

인수로 넘겨받은 리액트 컴포넌트를 HTML 문자열로 반환

훅이나 이벤트 핸들러는 포함되지 않음 → 빠르게 브라우저가 렌더링 할 수 있게 하기 위함

사용자와 인터렉션을 하기 위해서는 별도의 자바스크립트 코드를 다운로드, 파싱, 실행해야함 → hydrate

리액트의 루트 엘리먼트에 data-reactroot 속성이 추가됨

### renderToStaticMarkup

renderToString과 유사하지만 data-reactroot와 같은 리액트에서만 사용하는 추가적인 DOM 속성을 만들지 않음

hydrate 불가

### renderToNodeStream

renderToString과 결과물이 완전히 동일하지만,

1. 브라우저에서 사용 불가, 노드 환경에 의존
2. 결과물의 타입이 ReadableStream → 노드에서만 사용 가능
- ReadableStream
    - 스트림은 큰 데이터를 다룰 때 데이터를 청크로 분할해 조금씩 가져오는 방식

### renderToStaticNodeStream

리액트 속성이 제공되지 않는 renderToNodeStream

### hydrate (클라이언트에서 실행)

renderToString과 renderToNodeStream으로 생성된 HTML 콘텐츠에 자바스크립트 핸들러나 입네트를 붙이는 역할

이미 렌더링된 HTML이 있다는 가정하에 작업을 수행

서버에서 제공받은 HTML과 인수로 넘긴 컴포넌트 사이에 차이가 있으면 hydrate가 렌더링한 기준으로 웹페이지를 다시 그림
