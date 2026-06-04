# 트러블슈팅 가이드

실패하는 Eventbrite API 요청의 원인을 찾고 해결하세요.
이 가이드를 통해 401·403·404 오류, 응답 데이터 누락, HTML 콘텐츠 문제, 요청 제한 초과까지 가장 자주 발생하는 문제를 이해할 수 있습니다. 각 섹션에서 증상, 원인, 해결 단계, 확인 방법을 안내합니다.

전체 오류 코드 목록은 [오류 레퍼런스](../api/error-reference.md)를 참고하세요.

<br>

## 목차

- [자주 발생하는 코드 문제](#자주-발생하는-코드-문제)
- [401 인증 오류](#401-인증-오류)
- [403 권한 오류](#403-권한-오류)
- [404 리소스를 찾을 수 없음](#404-리소스를-찾을-수-없음)
- [응답 데이터 누락 또는 예상과 다름](#응답-데이터-누락-또는-예상과-다름)
- [HTML 콘텐츠 문제](#html-콘텐츠-문제)
- [요청 제한](#요청-제한)
- [일반 디버깅 팁](#일반-디버깅-팁)

<br>

## 자주 발생하는 코드 문제

원인이 불분명할 때 여기서 시작하세요.
API에 도달하기 전에 발생하는 실패의 대부분이 아래의 문제에서 비롯됩니다.

### 문제 1 — 소스 코드에 토큰 포함

**증상:** 공개 저장소나 빌드 로그에 토큰이 노출됩니다.

**해결:** 토큰을 환경 변수로 옮기세요.

```bash
export EVENTBRITE_TOKEN=your_token_here
```

```jsx
const token = process.env.EVENTBRITE_TOKEN;
```

**확인:** 코드베이스에서 토큰 문자열을 검색하세요.
어떤 소스 파일에도 나타나지 않아야 합니다.

<br>

### 문제 2 — 잘못된 Base URL

**증상:** 모든 요청이 연결 오류 또는 `404`를 반환합니다.

**해결:** `eventbrite.com`이 아닌 `eventbriteapi.com`을 사용하세요.

```
✗  https://www.eventbrite.com/v3/events/
✓  https://www.eventbriteapi.com/v3/events/
```

**확인:** 수정한 URL로 요청을 다시 보내세요.

<br>

### 문제 3 — Content-Type 헤더 누락

**증상:** POST 요청이 명확한 오류 없이 `400 Bad Request`를 반환합니다.

**해결:** 모든 POST 요청에 `Content-Type: application/json`을 포함하세요.

```bash
curl --request POST \
  --header "Authorization: Bearer YOUR_PRIVATE_TOKEN" \
  --header "Content-Type: application/json" \
  --data '{ "event": { ... } }' \
  "https://www.eventbriteapi.com/v3/organizations/{organization_id}/events/"
```

**확인:** 요청을 다시 보내세요. `200 OK` 응답이 반환되면
헤더 누락이 원인입니다.

<br>

## 401 인증 오류

### 증상

- API가 `401 Unauthorized`를 반환합니다.
- Eventbrite 콘솔에서는 작동하지만 코드에서는 실패합니다.

### 가능한 원인

- `Authorization` 헤더가 누락되었거나 형식이 잘못되었습니다.
- 토큰을 복사한 후 재발급되었거나 취소되었습니다.
- 토큰에 오타가 있거나 앞뒤에 공백이 포함되어 있습니다.

### 해결 방법

1. 요청에 `Authorization` 헤더가 아래 형식으로 정확하게 포함되어 있는지
확인하세요. `Bearer` 접두사와 공백 하나가 필요합니다.

```
Authorization: Bearer YOUR_PRIVATE_TOKEN
```

2. [API Keys 페이지](https://www.eventbrite.com/account-settings/apps)에서
새 토큰을 복사해서 기존 토큰을 교체하세요.
3. 하드코딩 오류를 방지하려면 토큰을 환경 변수에 저장하세요.

```bash
export EVENTBRITE_TOKEN=your_token_here
```

### 확인

아래 요청을 보내세요. 계정 정보가 포함된 `200 OK` 응답이 반환되면
토큰이 유효한 것입니다.

```bash
curl --request GET \
  --header "Authorization: Bearer YOUR_PRIVATE_TOKEN" \
  "https://www.eventbriteapi.com/v3/users/me/"
```

### 관련 문서

- [인증 가이드](../guides/authentication.md)
- [오류 레퍼런스 — 401](../api/error-reference.md#401-unauthorized)

<br>

## 403 권한 오류

### 증상

- API가 `403 NOT_AUTHORIZED`를 반환합니다.
- 인증은 됐지만 접근이 거부됩니다.
- 이벤트가 존재하지만 조회하거나 수정할 수 없습니다.

### 가능한 원인

- 이벤트가 다른 Eventbrite 계정이나 조직에 속해 있습니다.
- 토큰에 이 작업을 수행할 권한이 없습니다.
- 이벤트의 현재 상태에서 이 작업이 허용되지 않습니다 — 예를 들어
기존 주문이 있는 이벤트를 삭제하려는 경우.

### 해결 방법

1. `event_id`가 토큰을 발급한 계정에 속하는지 확인하세요.
Eventbrite에 로그인해서 내 계정에 이벤트가 있는지 확인하세요.
2. `organization_id`를 사용한다면 이벤트를 소유한 조직과 일치하는지
확인하세요.

```bash
curl --request GET \
  --header "Authorization: Bearer YOUR_PRIVATE_TOKEN" \
  "https://www.eventbriteapi.com/v3/users/me/organizations/"
```

3. 이벤트 응답에서 `status` 필드를 확인하세요.
일부 작업은 이벤트 상태에 따라 제한됩니다 — 예를 들어 주문이 있는
이벤트는 삭제할 수 없습니다.

### 확인

원래 요청을 다시 보내세요. `200 OK` 응답이 반환되면 권한 문제가 해결된
것입니다.

### 관련 문서

- [API 레퍼런스](../api/api-reference.md)
- [오류 레퍼런스 — 403](../api/error-reference.md#403-forbidden)

<br>

## 404 리소스를 찾을 수 없음

### 증상

- API가 `404 NOT_FOUND`를 반환합니다.
- 이벤트 ID가 유효해 보이지만 요청이 실패합니다.

### 가능한 원인

- `event_id`가 잘못되었거나, 형식이 틀렸거나, 잘못된 URL에서 복사했습니다.
- 이벤트가 삭제되거나 비공개로 전환되었습니다.
- 이벤트가 다른 계정에 속해 있습니다.

### 해결 방법

1. Eventbrite 계정의 이벤트 URL에서 올바른 `event_id`를 확인하세요.
ID는 URL 끝의 11자리 숫자입니다.

```
https://www.eventbrite.com/e/my-event-name-12345678901
                                           ^^^^^^^^^^^
                                    이것이 event_id입니다
```

2. 올바른 Base URL을 쓰고 있는지 확인하세요.

```
https://www.eventbriteapi.com/v3
```

3. Eventbrite 계정에서 이벤트를 직접 열어서 접근 가능한지 확인하세요.

### 확인

수정한 `event_id`로 아래 요청을 보내세요.
`200 OK` 응답이 반환되면 이벤트에 접근할 수 있습니다.

```bash
curl --request GET \
  --header "Authorization: Bearer YOUR_PRIVATE_TOKEN" \
  "https://www.eventbriteapi.com/v3/events/{event_id}/"
```

### 관련 문서

- [API 레퍼런스](../api/api-reference.md)
- [오류 레퍼런스 — 404](../api/error-reference.md#404-not-found)

<br>

## 응답 데이터 누락 또는 예상과 다름

### 증상

- `description.html` 같은 필드가 비어 있거나 `null`입니다.
- 예상한 필드가 응답에 없습니다.
- 초안(draft) 이벤트가 불완전한 데이터를 반환합니다.

### 가능한 원인

- 이벤트가 `draft` 상태입니다. 일부 필드는 게시 후에만 채워집니다.
- 이벤트 주최자가 입력하지 않은 선택적 필드입니다.
- 토큰 권한이 반환되는 필드를 제한합니다.

### 해결 방법

1. 응답에서 `status` 필드를 확인하세요. `"status": "draft"`이면
이벤트를 게시하거나 테스트용으로 게시된 이벤트를 사용하세요.
2. 코드에서 중첩 필드에 접근하기 전에 null 체크를 추가하세요.

```jsx
const description = event?.description?.text ?? "";
const capacity    = event?.capacity          ?? null;
```

### 확인

이벤트를 게시하거나 게시된 테스트 이벤트로 바꾼 후 요청을 다시 보내세요.
예상한 필드가 모두 있어야 합니다.

### 관련 문서

- [응답 처리 가이드](../guides/response-handling.md)

<br>

## HTML 콘텐츠 문제

### 증상

- HTML 태그가 UI에 그대로 텍스트로 보입니다.
- HTML 출력이 페이지 레이아웃을 깨뜨립니다.
- 보안 검토에서 HTML 렌더링 위험이 발견됩니다.

### 가능한 원인

- `html` 필드가 일반 텍스트가 필요한 곳에 전달됩니다.
- HTML 콘텐츠를 정제 없이 렌더링합니다.

### 해결 방법

1. 알림, 로그, 이메일 제목처럼 일반 문자열이 필요한 곳에는
`name.text` 또는 `description.text`를 사용하세요.

```jsx
sendNotification({ title: event.name.text });
```

2. 웹 인터페이스에서 렌더링할 때만
`name.html` 또는 `description.html`을 쓰세요.

```jsx
container.innerHTML = event.description.html;
```

3. 출처를 신뢰할 수 없는 HTML을 렌더링한다면 먼저 정제하세요.

```jsx
import DOMPurify from "dompurify";
container.innerHTML = DOMPurify.sanitize(event.description.html);
```

### 확인

UI에서 렌더링 결과를 확인하세요. HTML 태그가 텍스트로 보이지 않고,
페이지 레이아웃이 되어야 정상입니다.

### 관련 문서

- [응답 처리 가이드](../guides/response-handling.md)
- [코드 예제](../examples/code-examples.md)

<br>

## 요청 제한

### 증상

- API가 `429 Too Many Requests`를 반환합니다.
- 단일 요청은 성공하지만 대량 요청은 처리 중 실패합니다.

### 가능한 원인

- 시간당 2,000건 제한을 초과했습니다.
- 일일 48,000건 제한을 초과했습니다.
- 이벤트 생성 또는 게시 엔드포인트의 시간당 200건 제한을 초과했습니다.

### 해결 방법

1. API 응답 헤더에서 남은 요청 수를 확인하세요.

| 헤더 | 설명 |
| --- | --- |
| `X-Apiary-RateLimit-Limit` | 이 윈도우에서 허용된 최대 요청 수 |
| `X-Apiary-RateLimit-Remaining` | 제한 초기화 전 남은 요청 수 |
2. `X-Apiary-RateLimit-Remaining`이 `0`이 되면 1시간 윈도우가
초기화될 때까지 기다린 후 요청을 다시 보내세요.
3. 대량 작업을 처리할 때는 요청 사이에 시간 간격을 두세요.
요청 간격을 두는 구현 예제는 [코드 예제 — 요청 제한](../examples/code-examples.md#요청-제한)을 참고하세요.

### 확인

이후 응답에서 `X-Apiary-RateLimit-Remaining` 값을 확인하세요.
0 이상을 유지해야 합니다.

### 관련 문서

- [오류 레퍼런스 — 429](../api/error-reference.md#429-too-many-requests)
- [코드 예제](../examples/code-examples.md)

<br>

## 일반 디버깅 팁

특정 오류 코드가 없거나 위 섹션으로 해결되지 않을 때 아래 순서대로 진행하세요.

1. **먼저 Eventbrite 콘솔에서 테스트하세요.** 콘솔에서는 작동하지만
코드에서 실패한다면 헤더나 토큰 처리의 문제입니다.
2. **Postman으로 이동하세요.** 콘솔의 **Show Code Example → cURL** 출력을
사용하세요. Postman은 실제로 전송되는 헤더와 요청 본문을 패널에서 직접 확인할 수 있어서 코드 밖에서 요청을 검증할 수 있습니다.
3. **개발 중에는 전체 응답을 로그로 남기세요** — 상태 코드, 헤더,
응답 본문을 포함하세요. 오류 설명은 응답 본문의
`error_description` 필드에 있습니다.
4. **작동하는 요청과 비교하세요.** API 레퍼런스에서
작동하는 cURL 예시를 복사해서 실패하는 요청과 줄 단위로 비교하세요.

<br>

## 다음 단계

- [인증 가이드](../guides/authentication.md)
- [API 레퍼런스](../api/api-reference.md)
- [오류 레퍼런스](../api/error-reference.md)
- [응답 처리 가이드](../guides/response-handling.md)
- [코드 예제](../examples/code-examples.md)

<br>
