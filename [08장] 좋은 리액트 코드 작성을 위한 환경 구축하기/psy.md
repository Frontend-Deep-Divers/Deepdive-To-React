## ESLint

정적 코드 분석을 통한 버그 방지

자바스크립트 코드를 AST로 구조화하고 규칙과 대조하여 수정

### eslint-plugin과 eslint-config

- eslint-plugin
    - 규칙을 모아놓은 패키지
- eslint-config
    - eslint-plugin을 묶어서 한 세트로 제공하는 패키지
    - eslint-config-airbnb
    - @titicaca/triple-config-kit
    - eslint-config-next

### 나만의 ESLint 규칙 만들기

yo, generate-eslint를 활용해 eslint-plugin을 구성활 환경을 빠르게 구성 가능

### 주의할 점

Prettier와의 충돌 최소화 → eslint-plugin-prettier

> Prettier는 줄바꿈, 들여쓰기, 따옴표 등 포매팅과 관련된 작업을 담당하고, ESLint는 잠재적인 문제가 될 수 있는 부분을 분석함
> 

규칙 예외 처리를 위해서는 eslint-disable- 주석을 사용 → 잘 생각해보고 사용

## 리액트 팀이 권장하는 리액트 테스트 라이브러리

프론트엔드 테스트는 코드를 알 필요 없는 블랙박스 형태의 테스트

### React Testing Library

DOM Testing Library를 기반으로 만들어짐 → 자바스크립트 환경에서 HTML을 사용할 수 있음

컴포넌트를 렌더링하지 않고도 원하는 대로 렌더링되는지 확인할 수 있음

테스팅 프레임워크 = assertion 기반 테스트 수행 + 테스트 정보 알려줌
