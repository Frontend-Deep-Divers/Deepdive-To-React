# 8장 좋은 리액트 코드 작성을 위한 환경 구축하기

# 8.1 ESLint를 활용한 정적 코드 분석

ESLint는 파싱된 JS의 AST(Abstract Syntax TreE)를 기준으로 각종 규칙과 대조해 이를 위반한 코드를 알리거나 수정하는 정적 코드 분석 도구입니다.

JS 파서로 espree를 사용합니다. 파싱된 트리는 변수인지, 함수인지, 함수명은 무엇인지, 코드의 위치 등 자세한 정보를 담습니다.

```js
function hello(str) {}
```

위 코드를 espree로 파싱한 결과입니다.

```json
{
  "type": "Program",
  "start": 0,
  "end": 22,
  "range": [0, 22],
  "body": [
    {
      "type": "FunctionDeclaration",
      "start": 0,
      "end": 22,
      "range": [0, 22],
      "id": {
        "type": "Identifier",
        "start": 9,
        "end": 14,
        "range": [9, 14],
        "name": "hello"
      },
      "expression": false,
      "generator": false,
      "async": false,
      "params": [
        {
          "type": "Identifier",
          "start": 15,
          "end": 18,
          "range": [15, 18],
          "name": "str"
        }
      ],
      "body": {
        "type": "BlockStatement",
        "start": 20,
        "end": 22,
        "range": [20, 22],
        "body": []
      }
    }
  ],
  "sourceType": "module"
}
```

- `esplint-plugin`: ESLint의 규칙을 모아놓은 패키지입니다.
- `eslint-config`: `eslint-plugin`을 묶어서 제공하는 패키지입니다. `eslint-config-airbnb`나 `eslint-config-google` 등의 유명 회사들의 설정이 인기 있으며, 당장 적용해서 시작하기 좋은 설정들입니다. Next.js를 위한 `eslint-config-next`는 JS 뿐만 아니라 Next.js 환경에서의 HTML 코드를 정적 분석 대상으로 제공하는 등 유용한 기능을 가지고 있습니다.

# 8.2 리액트 팀이 권장하는 리액트 테스트 라이브러리

프론트엔드의 테스팅은 사용자와 동일하거나 유사한 환경에서 수행됩니다. 블랙박스 형태로 테스트가 이뤄지며, 코드가 어떻게 작성되었는지는 그다지 관심이 없고 의도대로 작동하는 데 좀 더 초점이 맞춰지게 됩니다.

## React Testing Library란?

리액트 애플리케이션을 테스팅하기 위해 React Testing Library가 널리 사용됩니다. DOM Testing Library를 기반으로 만들어졌습니다.

DOM Testing Library는 jsdom을 기반으로 하고 있습니다. jsdom은 Node.js 같은 JS만 존재하는 환경에서 HTML과 DOM을 사용할 수 있도록 해주는 라이브러리입니다. jsdom을 활용해 JS 환경에서도 HTML을 사용할 수 있으므로 DOM Testing Library에서 제공하는 API를 통해 테스트를 수행할 수 있게 됩니다.

리액트 테스팅 라이브러리는 DOM Testing Library를 통해 브라우저를 실행하지 않고도 리액트 컴포넌트가 의도대로 렌더링되고 있는지 확인할 수 있습니다.

테스팅 프레임워크는 어설션을 기반으로 테스트를 수행하며, 테스트 코드 작성자에게 도움이 될 만한 정보를 알려주는 역할을 수행합니다.

> 어설션 라이브러리: 테스트 결과를 확인할 수 있도록 `assert` 등의 메서드를 제공하는 라이브러리로, `should.js`, `expect.js`, `chai` 등의 다양한 라이브러리가 있습니다.

Jest, Mocha, Karma, Jasmine 등의 프레임워크가 있습니다. 이들 중 현재 메타에서 작성한 오픈소스 라이브러리인 Jest가 널리 쓰이고 있습니다.

## 리액트 컴포넌트 테스트 코드 작성

1. 컴포넌트를 렌더링한다.
2. 필요하다면 컴포넌트에서 특정 액션을 수행한다.
3. 1, 2번을 통해 기대하는 결과와 실제 결과를 비교한다.
