# 응답 처리 가이드

Eventbrite API 응답은 이벤트 데이터를 읽고, 누락된 필드를 처리하고,
HTML 콘텐츠를 안전하게 다루는 방식에 영향을 주는 일관된 패턴을 따릅니다.
이 가이드에서는 그 패턴이 왜 존재하는지, 애플리케이션에서 어떻게 올바르게
다루는지를 설명합니다.

엔드포인트 스키마와 파라미터 정의는 [API 레퍼런스](../api/api-reference.md)를,
전체 오류 코드 목록은 [오류 레퍼런스](../api/error-reference.md)를 참고하세요.

<br>

## 목차

- [응답 구조 설계 이유](#응답-구조-설계-이유)
- [이벤트 객체](#이벤트-객체)
- [HTML 형식 콘텐츠 처리](#html-형식-콘텐츠-처리)
- [선택적·널 필드 처리](#선택적널-필드-처리)
- [오류 응답](#오류-응답)
- [Best Practices](#best-practices)

<br>

## 응답 구조 설계 이유

응답 형식의 설계 결정을 이해하면 시행착오 없이 엣지 케이스를 올바르게
처리할 수 있습니다.

**`name`과 `description`이 `text`와 `html`을 모두 반환하는 이유는 무엇인가요?**
Eventbrite 이벤트는 HTML을 생성하는 리치 텍스트 에디터로 만듭니다.
하지만 알림, 로그, 검색 인덱스, 모바일 앱처럼 API를 소비하는 모든
클라이언트가 HTML을 안전하게 렌더링할 수 있는 것은 아닙니다. 두 가지
형식을 함께 반환하면 각 클라이언트가 HTML을 직접 파싱하거나 제거하지
않고도 필요한 형식을 바로 쓸 수 있습니다.

**빈 문자열 대신 `null`을 반환하는 이유는 무엇인가요?**
Eventbrite는 설정되지 않은 필드와 의도적으로 비운 필드를 구분합니다.
`null`인 description은 주최자가 아직 설명을 작성하지 않았다는 뜻입니다.
이를 통해 애플리케이션은 빈 공간 대신 "설명이 없습니다"를 표시할 수 있고,
빈 HTML 태그를 렌더링하는 상황도 피할 수 있습니다.

**시간 필드에 `utc`와 `local`이 함께 있는 이유는 무엇인가요?**
이벤트 시간은 두 가지 맥락에서 필요합니다. 이벤트 페이지에 표시할
주최자의 현지 시간과, 일정 관리·캘린더 내보내기·시간대 간 비교에
쓰이는 시간대 중립 형식입니다. `utc` 필드는 항상 일관되게 제공되고,
`local` 필드는 주최자가 설정한 시간대에 따라 달라집니다.

<br>

## 이벤트 객체

대부분의 이벤트 관련 엔드포인트는 이벤트 객체를 반환합니다.
필드는 네 가지 범주로 나뉩니다.

| 범주 | 필드 |
| --- | --- |
| 식별 | `id`, `url` |
| 텍스트 콘텐츠 | `name`, `description` — 각각 `text`와 `html` 변형 포함 |
| 일정 | `start`, `end` — 각각 `utc`, `local`, `timezone` 포함 |
| 메타데이터 | `status`, `currency`, `capacity` |

**응답 예시:**

```json
{
  "id": "12345",
  "name": {
    "text": "내 이벤트",
    "html": "<p>내 이벤트</p>"
  },
  "description": {
    "text": "이벤트 설명",
    "html": "<p>이벤트 설명</p>"
  },
  "start": {
    "timezone": "America/Los_Angeles",
    "utc": "2026-06-01T09:00:00Z",
    "local": "2026-06-01T02:00:00"
  },
  "end": {
    "timezone": "America/Los_Angeles",
    "utc": "2026-06-01T17:00:00Z",
    "local": "2026-06-01T10:00:00"
  },
  "status": "live",
  "currency": "USD",
  "capacity": 100,
  "url": "https://www.eventbrite.com/e/12345"
}
```

전체 필드 레퍼런스는
[API 레퍼런스 — 이벤트 조회](../api/api-reference.md#이벤트-조회)를
참고하세요.

<br>

## HTML 형식 콘텐츠 처리

Eventbrite는 엔드포인트에 따라 두 가지 패턴으로 HTML 콘텐츠를 반환합니다.

### 패턴 1 — text/html 이중 필드

가장 일반적인 패턴입니다. 텍스트 콘텐츠 객체는 같은 값에 대해
일반 텍스트 변형과 HTML 변형을 모두 포함합니다.

```json
"name": {
  "text": "내 이벤트",
  "html": "<p>내 이벤트</p>"
}
```

알림, 로그, 검색 인덱스, 이메일 제목처럼 일반 문자열이 필요한 곳에는
`text`를 쓰세요. 웹 인터페이스에서 렌더링할 때는 `html`을 쓰세요.

```jsx
// UI에 렌더링
container.innerHTML = event.description.html;

// 알림 시스템에 전달
sendNotification({ title: event.name.text });
```

### 패턴 2 — 전체 HTML 응답

일부 엔드포인트는 응답 본문 전체를 원시 HTML 문자열로 반환합니다.

**엔드포인트:**

```
GET /events/{event_id}/description/
```

**응답:**

```json
{
  "description": "<div>예시 요약!</div>\n<div><p>내 <em>이벤트</em> 설명이 <strong>여기</strong>에 들어갑니다.</p></div>"
}
```

이 콘텐츠를 렌더링할 때는 HTML 파서를 쓰세요. 특히 출처를 완전히
신뢰할 수 없는 경우, 먼저 정제하지 않고 원시 문자열을 `innerHTML`에
바로 넘기지 마세요.

```jsx
import DOMPurify from "dompurify";
container.innerHTML = DOMPurify.sanitize(event.description);
```

<br>

## 선택적·널 필드 처리

모든 응답에 모든 필드가 포함되지는 않습니다. 다음의 경우 필드가 없거나
`null`일 수 있습니다.

- 데이터가 불완전한 초안(draft) 이벤트 — `description`은 주최자가 작성하기
전까지 `null`입니다
- 주최자가 설정하지 않은 필드 — `capacity`는 제한이 없으면 `null`입니다
- 토큰에 접근 권한이 없는 필드

중첩된 속성에 접근하기 전에 필드가 있는지 반드시 확인하세요.

**JavaScript:**

```jsx
const name        = event?.name?.text        ?? "제목 없는 이벤트";
const description = event?.description?.text ?? "";
const capacity    = event?.capacity          ?? null;
```

**Python:**

```python
name        = event.get("name", {}).get("text", "제목 없는 이벤트")
description = event.get("description", {}).get("text", "")
capacity    = event.get("capacity")
```

<br>

## 오류 응답

모든 이벤트 엔드포인트는 일관된 형식으로 오류를 반환합니다.

```json
{
  "error": "ERROR_CODE",
  "error_description": "오류 내용을 설명하는 메시지.",
  "status_code": 400
}
```

`error` 필드로 애플리케이션 로직에서 오류 유형을 식별하세요.
`error_description`은 로깅과 디버깅에 쓰세요. 최종 사용자에게 그대로
표시하는 용도로는 적합하지 않습니다.

전체 오류 코드와 해결 방법은 [오류 레퍼런스](../api/error-reference.md)를
참고하세요.

<br>

## Best Practices

**필요한 필드만 추출하세요.**
전체 응답 객체를 저장하지 마세요. 애플리케이션에서 쓰는 필드만
접근하세요. 이렇게 하면 향후 API 변경의 영향을 줄이고 데이터 모델을
깔끔하게 유지할 수 있습니다.

```jsx
const { id, status } = response;
const name     = response.name?.text;
const startUtc = response.start?.utc;
```

**HTML과 일반 텍스트를 분리하세요.**
일반 텍스트가 필요한 곳에 `html` 필드를 쓰거나, HTML 렌더러에 `text`
필드를 넘기지 마세요. 이 둘을 섞으면 레이아웃이 깨지거나 HTML 태그가
텍스트로 그대로 보입니다.

```jsx
// UI에 표시 — html 사용
container.innerHTML = event.name.html;

// 알림 시스템에 전달 — text 사용
sendNotification({ title: event.name.text });
```

**오류 코드를 사용자 메시지로 변환하세요.**
API의 `error_description`은 개발자를 위한 메시지입니다.
사용자가 이해하고 행동할 수 있는 메시지로 바꾸세요.

```jsx
const ERROR_MESSAGES = {
  NO_AUTH:        "인증에 실패했습니다. API 토큰을 확인하세요.",
  NOT_AUTHORIZED: "이 이벤트에 접근할 권한이 없습니다.",
  NOT_FOUND:      "이벤트를 찾을 수 없습니다.",
  FIELD_INVALID:  "요청에 유효하지 않은 값이 포함되어 있습니다.",
};

function handleApiError(error) {
  return ERROR_MESSAGES[error.error] ?? "예기치 않은 오류가 발생했습니다.";
}
```

<br>

## 다음 단계

- [API 레퍼런스](../api/api-reference.md)
- [오류 레퍼런스](../api/error-reference.md)
- [인증 가이드](../guides/authentication.md)
- [코드 예제](../examples/code-examples.md)

<br>