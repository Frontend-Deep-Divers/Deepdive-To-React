# 14장 웹사이트 보안을 위한 리액트와 웹페이지 보안 이슈

# 14.1 리액트에서 발생하는 크로스 사이트 스크립팅(XSS)

크로스 사이트 스크립팅(XSS, Cross Site Scripting)이란 제3자가 웹사이트에 악성 스크립트를 삽입해 실행하는 공격을 말합니다.

리액트에서 XSS 이슈가 발생할 수 있는 원인에 대해 알아봅니다.

## 14.1.1 dangerouslySetInnerHTML prop

`dangerouslySetInnerHTML`은 특정 브라우저 DOM의 `innerHTML`을 특정한 내용으로 교체할 수 있는 방법입니다. 일반적으로 게시판과 같이 사용자나 관리가 입력한 내용을 브라우저에 표시하는 용도로 사용됩니다.

`dangerouslySetInnerHTML`은 오직 `__html`을 키로 가지고 있는 객체만 인수로 받을 수 있으며, 인수로 넘겨받은 문자열을 DOM에 그대로 표시하는 역할을 합니다. 그러나 **문제는 이 인수로 받는 문자열에 제한이 없다는 것**입니다.

```jsx
<div dangerouslySetInnerHTML={{ __html: '<img src=x onerror="alert(1)" />' }} />
```

때문에 사용에 주의해야 하는 prop이며 넘겨주는 문자열 값에는 검증이 요구됩니다.

## 14.1.2 useRef를 활용한 직접 삽입

`useRef`를 통해 DOM 요소에 접근해 `innerHTML`을 수정하면 리액트의 보안 보호를 우회할 수 있습니다.

```js
const ref = useRef();
useEffect(() => {
  ref.current.innerHTML = userInput;
}, []);
```

이외에도 `svg/onload`나 `<a>` 태그에 잘못된 `href`를 삽입하거나 `onclick`, `onload` 등의 이벤트를 활용하는 등의 방식이 있을 수 있습니다.

> `svg` 태그의 `onload` 속성은 svg가 로드될 때 실행됩니다. 만약 이 값에 공격 코드가 들어온다면, 임의로 스크립트를 실행할 수 있게 됩니다. `<svg onload="alert('XSS')" />`

## 14.1.3 리액트에서 XSS 문제를 피하는 방법

가장 안전한 방법은 제3자가 삽입할 수 있는 HTML을 안전한 HTML로 한 번 치환하는 것입니다. (sanitization)

직접 구현하거나 `DOMPurify`, `sanitize-html`과 같은 유명 라이브러리를 사용할 수 있습니다.

단순히 보여줄 때뿐만 아니라 사용자가 콘텐츠를 저장할 때도 한번 이스케이프 과정을 거치는 것이 더 효율적이고 안전합니다. 이러한 과정은 되도록 서버에서 수행하는 것이 좋습니다. 만약 POST 요청을 스크립트나 `curl` 등으로 직접 요청할 경우, 클라이언트의 과정을 생략하고 바로 저장될 수 있기 때문입니다.

게시판 같은 기능이 없어도 XSS 공격은 충분히 발생할 가능성이 있습니다. 예시로, 쿼리스트링에 있는 내용을 그대로 실행하거나 보여주는 경우에도 보안 취약점이 발생할 수 있습니다. (쿼리스트링에 스크립트를 삽입하는 경우)

💡 리액트는 기본적으로 XSS를 방어하기 위한 이스케이프 처리 로직이 존재합니다. 그러나 `dangerouslySetInnerHTML`이나 props로 넘겨받는 값의 경우 개발자의 활용도에 따라 원본 값이 필요할 수 있기 때문에 이런 작업이 무시됩니다.

# 14.2 getServerSideProps와 서버 컴포넌트를 주의하자

`getServerSideProps`가 반환하는 props 값은 모두 사용자의 HTML에 기록되고, 전역 변수로 등록되어 스크립트로 접근할 수 있는 보안 위협에 노출되는 값이 됩니다.

# 14.3 <a> 태그의 값에 적절한 제한을 둬야 한다

`href` 속성에 `javascript:`로 시작하는 JS 코드를 넣어두는 경우가 있습니다. 이는 기본 기능을 막고 `onclick` 이벤트와 같이 별도의 이벤트를 작동시키기 위한 용도로 사용되곤 합니다.

하지만 이러한 방식은 마크업 관점에서 또한 안티 패턴이며 `<a>` 태그는 반드시 페이지 이동이 있을 때만 사용하는 것이 좋습니다.

`href`에 사용자가 입력한 주소를 넣을 수 있다면 XSS 이슈가 발생할 수 있으며, `href`에 들어갈 수 있는 값 또한 제한해야 합니다. 피싱 사이트로 이동하는 것을 막기 위해 가능하면 `origin`도 확인해 처리하는 것이 좋습니다.

# 14.4 HTTP 보안 헤더 설정하기

## 14.4.1 Strict-Transport-Security

`Strict-Transport-Security` 응답 헤더는 모든 사이트가 HTTPS를 통해 접근해야 하며, 만약 HTTP로 접근하는 경우 모든 시도를 HTTPS로 변경되기 합니다.

사용법은 다음과 같습니다.

```
Strict-Transport-Security: max-age=<expire-time>; includeSubDomains
```

<expire-time>로 설정한 시간은 브라우저가 HTTP로 요청을 보내도 자동으로 HTTPS로 요청하게 하는 시간입니다. 0으로 설정하면 헤더가 즉시 만료되고 HTTP로 요청하게 됩니다. `includeSubDomains`는 규칙을 서브 도메인에도 적용합니다.

## 14.4.2 X-XSS-Protection

현재는 비표준 기술로, 사파리와 구형 브라우저에서만 제공되는 기능입니다.

이 헤더는 XSS 취약점이 발견되면 페이지 로딩을 중단하는 헤더입니다. `Content-Security-Policy`를 지원하지 않는 구형 브라우저에서 사용 가능합니다. 그러나 해당 헤더를 전적으로 믿으면 안되며, 페이지 내부에 별도의 XSS에 대한 처리가 존재하는 것이 권장됩니다.

```
X-XSS-Protection: 0
X-XSS-Protection: 1
X-XSS-Protection: 1; mode=block
X-XSS-Protection: 1; report=<reporting-uri>
```

- 0: 필터링 OFF
- 1: 기본값으로, 필터링 ON(XSS 공격 감지 시 XSS 관련 코드 제거 후 안전한 페이지를 보여줌)
- 1; mode=block: 1과 유사하지만 접근 자체를 막음
- 1; report=<reporting-uri>: 크로미움 기반 브라우저에서만 작동, XSS 공격 감지 시 보고서를 report= 쪽으로 보냄

현재는 대부분의 최신 브라우저(크롬, 파이어폭스 등)에서 이 헤더를 무시하거나 비활성화합니다.
실제 보안을 위해서는 Content-Security-Policy (CSP) 사용이 권장됩니다.

## 14.4.3 X-Frame-Options

페이지를 `frame`, `iframe`, `embed`, `object` 내부에서 렌더링을 허용할지를 나타낼 수 있습니다. 외부에서 자신의 페이지가 해당 태그 내에서 삽입되는 것을 막아줍니다.

```
X-Frame-Options: DENY
X-Frame-Options: SAMEORIGIN
```

## 14.4.4 Permissions-Policy

웹사이트에서 사용할 수 있는 기능과 사용할 수 없는 기능을 명시적으로 선언하는 헤더입니다.

## 14.4.5 X-Contnet-Type-Options

MIME 타입이 브라우저에 의해 임의로 변경되지 않게 하는 헤더입니다. 웹서버가 브라우저에 강제로 이 파일을 읽는 방식을 지정하게 합니다.

`Content-Type` 헤더가 없거나 잘못된 경우, 브라우저가 임의로 자바스크립트나 CSS로 인식하는 것을 막기 위해 사용됩니다.

## 14.4.6 Referrer-Policy

HTTP 요청의 `Referer` 헤더는 요청을 보낸 페이지의 주소가 들어 있습니다.

이 헤더는 브라우저가 다른 도메인으로 요청을 보낼 때 **Referer 헤더에 어느 수준의 정보(전체 URL / 도메인 / 없음)** 를 포함시킬지 제어합니다.

| Referrer-Policy의 값              | No Data      | Origin Only               | Full URL    | 브라우저 기본값          |
| --------------------------------- | ------------ | ------------------------- | ----------- | ------------------------ |
| `no-referrer`                     | ✅           |                           |             |                          |
| `origin`                          |              | ✅                        |             |                          |
| `unsafe-url`                      |              |                           | ✅          |                          |
| `strict-origin`                   | HTTPS → HTTP | 동일 scheme               |             |                          |
| `no-referrer-when-downgrade`      | HTTPS → HTTP |                           | 동일 scheme | 엣지                     |
| `origin-when-cross-origin`        |              | cross-origin              | same-origin |                          |
| `same-origin`                     | cross-origin |                           | same-origin |                          |
| `strict-origin-when-cross-origin` | HTTPS → HTTP | cross-origin, 동일 scheme |             | 크롬, 파이어폭스, 사파리 |

`meta` 태그를 사용해도 설정할 수 있습니다.

```html
<meta name="referrer" content="origin"
```

페이지 이동 시나 이미지 요청, `link` 태그 등에도 사용할 수 있습니다.

```html
<a href="http://yceffort.kr" referrerpolicy="origin">...</a>
```

구글에서는 `strict-origin-when-cross-origin` 혹은 그 이상을 명시적으로 선언해 둘 것을 권고하고 있습니다.

## 14.4.7 Content-Security-Policy

콘텐츠 보안 정책(CSP)은 XSS 공격이나 데이터 삽입 공격과 같은 보안 위협을 막기 위해 설계됐습니다.

사용 가능한 모든 지시문은 W3에서 확인할 수 있습니다: https://www.w3.org/TR/CSP3/

- \*-src: 다양한 `src`를 제어할 수 있는 지시문입니다. 선언된 출처에서만 소스를 가져올 수 있습니다.
- form-action: 폼 양식으로 제출할 수 있는 URL을 제한할 수 있습니다. `form-action` 자체를 모두 막아버리는 것도 가능합니다.

```
Content-Security-Policy: default-src 'self'; script-src 'self' https://apis.google.com; object-src 'none'; frame-ancestors 'none';
```

## 14.4.8 보안 헤더 설정하기

Next.js에서는 `next.config.js`에서 HTTP 경로별 보안 헤더를 적용할 수 있습니다.

Nginx에서는 경로별로 `add_header` 지시자를 사용해 원하는 응답 헤더를 추가할 수 있습니다.

## 14.4.9 보안 헤더 확인하기

서비스 중인 웹사이트의 보안 헤더를 확인하려면 https://securityheaders.com/를 방문해 주소를 입력하면 보안 헤더 상황을 알 수 있습니다.

# 14.5 취약점이 있는 패키지의 사용을 피하자

보안에 취약한 패키지가 있는지 항상 점검해야 합니다.

https://security.snyk.io/에서 패키지 이름으로 검색하면 라이브러리의 취약점을 파악할 수 있습니다.

# 14.6 OWASP Top 10

OWASP은 Open WorldWide Application Security Project라는 프로젝트로, 주로 웹에서 발생할 수 있는 정보 노출, 악성 스크립트, 보안 취약점 등을 연구하며, 주기적으로 10대 웹 애플리케이션 취약점을 공개합니다.

https://owasp.org/Top10/에서 최신 목록을 확인할 수 있습니다.
