# Eventbrite API 문서

이 문서 세트를 통해 Eventbrite API 연동에 필요한 모든 것을 한 곳에서 찾을 수 있습니다. 인증 설정부터 이벤트 관리, 응답 처리, 오류 해결까지 전체 연동 흐름을 다룹니다.

<br>

## 문서 구조

```
docs/ko/
├── get-started/
│   └── quick-start.md
├── tutorials/
│   └── first-api-call.md
├── guides/
│   ├── authentication.md
│   ├── pagination-guide.md
│   ├── response-handling.md
│   └── troubleshooting.md
├── api/
│   ├── api-reference.md
│   ├── error-reference.md
│   └── quick-reference.md
├── examples/
│   └── code-examples.md
└── reference/
    └── glossary.md
```

<br>

## 문서 목록

| 문서 | 유형 | 설명 |
| --- | --- | --- |
| [빠른 시작 가이드](../ko/get-started/qucik-start.md) | How-to | 3분 안에 첫 API 요청 성공시키기 |
| [첫 API 호출 튜토리얼](../ko/tutorials/first-api-call.md) | Tutorial | Eventbrite API 콘솔과 Postman을 사용한 단계별 가이드 |
| [인증 가이드](../ko/guides/authentication.md) | How-to | 자격 증명 유형 선택, 프라이빗 토큰 생성, 요청 인증 방법 |
| [페이지네이션 가이드](../ko/guides/pagination.md) | How-to | 컨티뉴에이션 토큰으로 여러 페이지의 이벤트 가져오기 |
| [응답 처리 가이드](../ko/guides/response-handling.md) | Explanation | 응답 구조 설계 이유와 안전하게 데이터를 처리하는 방법 |
| [트러블슈팅 가이드](../ko/guides/troubleshooting.md) | How-to | 증상별 API 오류 진단 및 해결 방법 |
| [API 레퍼런스](../ko/api/api-reference.md) | Reference | 전체 이벤트 엔드포인트의 시그니처, 파라미터, 응답 필드 |
| [오류 레퍼런스](../ko/api/error-reference.md) | Reference | 전체 오류 코드, 원인, 해결 방법 |
| [빠른 참조 가이드](../ko/api/quick-reference.md) | Reference | 엔드포인트, 헤더, 상태 코드, 요청 제한 한눈에 보기 |
| [코드 예제](../ko/examples/code-examples.md) | Reference | cURL, JavaScript, Node.js, Python 요청 예제 |
| [용어집](../ko/reference/glossary.md) | Reference | 문서 전반에서 사용하는 주요 용어 정의 |

<br>

## 시작점 선택하기

### 빠르게 첫 API 요청을 보내고 싶어요

👉 [**빠른 시작 가이드**](../ko/get-started/qucik-start.md)

3분 안에 프라이빗 토큰으로 인증하고 이벤트 조회 엔드포인트에서 실제 JSON 응답을 받을 수 있습니다.

### API를 단계별로 학습하고 싶어요

👉 [**첫 API 호출 튜토리얼**](../ko/tutorials/first-api-call.md)

Eventbrite API 콘솔과 Postman을 사용한 단계별 가이드입니다.
인증, 실제 요청, 응답 확인, cURL 내보내기를 다룹니다.

### 빠르게 찾아보고 싶어요

👉 [**빠른 참조 가이드**](../ko/api/quick-reference.md)

엔드포인트, 요청 헤더, 응답 필드, 상태 코드, 요청 제한을
한 페이지에서 확인하세요.

### 문제를 해결하고 싶어요

👉 [**트러블슈팅 가이드**](../ko/guides/troubleshooting.md)

401·403·404 오류, 응답 데이터 누락, HTML 콘텐츠 문제, 요청 제한까지
증상별로 구성되어 있습니다. 각 섹션에서 원인, 해결 단계, 확인 방법을
안내합니다.

### 용어의 뜻을 모르겠어요

👉 [**용어집**](../ko/reference/glossary.md)

프라이빗 토큰, 컨티뉴에이션 토큰, draft vs live, UTC vs local 등
문서 전반에서 사용하는 주요 용어를 정의합니다.

<br>

## HTTP 클라이언트 직접 사용하기

Eventbrite는 어떤 프로그래밍 언어에 대해서도 공식 SDK를 제공하지 않습니다.
API는 표준 RESTful HTTP 인터페이스로, 인증·요청·응답을 표준 HTTP 클라이언트로
직접 처리합니다.

환경별 권장 도구:

| 환경 | 도구 |
| --- | --- |
| 브라우저 기반 JavaScript | `fetch` (내장) |
| Node.js / 서버 사이드 JavaScript | `node-fetch` 또는 `axios` |
| Python | `requests` |
| 테스트 및 탐색 | Postman |

실제 요청 예제는 [코드 예제](../ko/examples/code-examples.md)를 참고하세요.

<br>

## 대상 독자

- Eventbrite API를 연동하는 개발자
- API 문서 포트폴리오를 구축하는 테크니컬 라이터
- 문서 구조와 품질을 평가하는 검토자

<br>

## 프로젝트 상태

이 문서는 테크니컬 라이팅 포트폴리오 프로젝트로, 프로덕션 사용을 목적으로 하지 않습니다.
Eventbrite의 공개 API 문서를 바탕으로 작성됐으며,
문서 구조와 모범 사례에 초점을 맞추고 있습니다.

공식 Eventbrite API 문서는
[eventbrite.com/platform/api]를
참고하세요.

<br>