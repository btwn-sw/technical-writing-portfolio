# Eventbrite API 레퍼런스

Eventbrite API의 엔드포인트, 파라미터, 응답 필드를 조회하세요.
이 레퍼런스는 이벤트 관련 엔드포인트를 중심으로 안내합니다.
인증 설정은 [인증 가이드](../guides/authentication.md)를 참고하세요.

<br>

## 목차

- [Base URL 및 버전](#base-url-및-버전)
- [인증](#인증)
- [요청 제한](#요청-제한)
- [오류 처리](#오류-처리)
- [이벤트 엔드포인트](#이벤트-엔드포인트)
    - [조직별 이벤트 목록 조회](#조직별-이벤트-목록-조회)
    - [이벤트 생성](#이벤트-생성)
    - [이벤트 조회](#이벤트-조회)
    - [이벤트 수정](#이벤트-수정)
    - [이벤트 게시](#이벤트-게시)
    - [이벤트 삭제](#이벤트-삭제)

<br>

## Base URL 및 버전

모든 엔드포인트는 아래 Base URL을 사용합니다.

```
https://www.eventbriteapi.com/v3
```

- 현재 API 버전: `v3`
- 모든 엔드포인트는 `/v3/`로 시작합니다.
- 요청 형식: `application/json`
- 응답 형식: `application/json`

<br>

## 인증

모든 요청의 `Authorization` 헤더에 프라이빗 토큰을 포함해야 합니다.

```
Authorization: Bearer YOUR_PRIVATE_TOKEN
```

`YOUR_PRIVATE_TOKEN`은
[Eventbrite API Keys 페이지](https://www.eventbrite.com/account-settings/apps)에서 발급됩니다.
전체 설정 방법은 [인증 가이드](../guides/authentication.md)를 참고하세요.

<br>

## 요청 제한

Eventbrite는 플랫폼 안정성을 위해 모든 API 통합에 요청 제한을 둡니다.

### 기본 제한

| 제한 유형 | 제한 |
| --- | --- |
| 전체 요청 | 시간당 2,000건 |
| 전체 요청 | 일일 48,000건 |
| 이벤트 생성 | 시간당 200건 |
| 이벤트 게시 | 시간당 200건 |

### 요청 제한 헤더

요청이 제한되면 Eventbrite는 응답 결과에 아래 헤더를 포함합니다.

| 헤더 | 설명 |
| --- | --- |
| `X-Apiary-RateLimit-Limit` | 현재 윈도우에서 허용된 최대 요청 수 |
| `X-Apiary-RateLimit-Remaining` | 제한이 초기화되기 전까지 남은 요청 수 |

<br>

## 오류 처리

이벤트 엔드포인트에서 반환될 수 있는 주요 오류입니다.
전체 오류 코드와 해결 방법은 [오류 레퍼런스](../api/error-reference.md)를 참고하세요.

| 상태 코드 | 오류 코드 | 설명 |
| --- | --- | --- |
| `400` | `FIELD_INVALID` | 필수 필드가 없거나 파라미터 값이 유효하지 않습니다. |
| `401` | `NO_AUTH` | 인증 토큰이 없거나 유효하지 않습니다. |
| `403` | `NOT_AUTHORIZED` | 토큰에 이 작업을 수행할 권한이 없습니다. |
| `404` | `NOT_FOUND` | 요청한 이벤트가 존재하지 않습니다. |

**오류 응답 형식:**

```json
{
  "error": "NOT_FOUND",
  "error_description": "The requested event does not exist or you do not have permission to view it.",
  "status_code": 404
}
```

오류 처리 방법은 [응답 처리 가이드](../guides/response-handling.md)를 참고하세요.

<br>

## 이벤트 엔드포인트

### 조직별 이벤트 목록 조회

조직에 속한 이벤트 목록을 페이지 단위로 반환합니다.

**엔드포인트**

```
GET /organizations/{organization_id}/events/
```

**경로 파라미터**

| 이름 | 타입 | 필수 | 설명 | 예시 |
| --- | --- | --- | --- | --- |
| `organization_id` | String | 예 | 이벤트 목록을 조회할 조직의 ID | `12345` |

**쿼리 파라미터**

| 파라미터 | 타입 | 필수 | 설명 |
| --- | --- | --- | --- |
| `status` | String | 아니오 | 이벤트 상태로 필터링합니다. 허용 값: `draft`, `live`, `started`, `ended`, `completed`, `canceled`. 생략하면 모든 상태를 반환합니다. |
| `continuation` | String | 아니오 | 이전 응답에서 반환된 페이지네이션 토큰. 다음 페이지의 결과를 가져올 때 사용합니다. |

**요청 예시**

```bash
curl --request GET \
  --header "Authorization: Bearer YOUR_PRIVATE_TOKEN" \
  "https://www.eventbriteapi.com/v3/organizations/{organization_id}/events/"
```

**응답: `200 OK`**

```json
{
  "pagination": {
    "object_count": 4,
    "page_number": 1,
    "page_size": 50,
    "page_count": 1,
    "has_more_items": false
  },
  "events": [
    {
      "id": "12345",
      "name": {
        "text": "내 이벤트",
        "html": "<p>내 이벤트</p>"
      },
      "status": "live",
      "start": {
        "timezone": "UTC",
        "utc": "2026-06-01T09:00:00Z",
        "local": "2026-06-01T09:00:00"
      },
      "end": {
        "timezone": "UTC",
        "utc": "2026-06-01T17:00:00Z",
        "local": "2026-06-01T17:00:00"
      },
      "url": "https://www.eventbrite.com/e/12345"
    }
  ]
}
```

**응답 필드**

| 필드 | 타입 | 설명 |
| --- | --- | --- |
| `pagination.object_count` | Integer | 전체 페이지의 이벤트 총 개수 |
| `pagination.page_number` | Integer | 현재 페이지 번호. 1부터 시작합니다. |
| `pagination.page_size` | Integer | 페이지당 반환되는 최대 이벤트 수 |
| `pagination.page_count` | Integer | 전체 페이지 수 |
| `pagination.has_more_items` | Boolean | 추가 페이지가 있으면 `true` |
| `pagination.continuation` | String | 다음 요청의 `continuation` 파라미터로 전달할 토큰. `has_more_items`가 `true`일 때만 포함됩니다. |
| `events` | Array | 이벤트 객체 목록. 각 객체의 필드는 [이벤트 조회](#이벤트-조회)와 동일합니다. |

단계별 페이지네이션 사용 방법은 [페이지네이션 가이드](../guides/pagination.md)를 참고하세요.

<br>

### 이벤트 생성

조직 아래에 새 이벤트를 만듭니다. 이벤트는 `draft` 상태로 생성되며,
게시하기 전까지 공개적으로 표시되지 않습니다.

**엔드포인트**

```
POST /organizations/{organization_id}/events/
```

**경로 파라미터**

| 이름 | 타입 | 필수 | 설명 | 예시 |
| --- | --- | --- | --- | --- |
| `organization_id` | String | 예 | 이벤트를 소유할 조직의 ID | `12345` |

**요청 본문 파라미터**

| 필드 | 타입 | 필수 | 설명 |
| --- | --- | --- | --- |
| `event.name.html` | String | 예 | HTML 형식의 이벤트 이름 |
| `event.description.html` | String | 아니오 | HTML 형식의 이벤트 설명 |
| `event.start.utc` | String | 예 | UTC 시작 시간 — 형식: `YYYY-MM-DDTHH:MM:SSZ` |
| `event.start.timezone` | String | 예 | 시작 시간의 IANA 시간대 — 예: `America/Los_Angeles` |
| `event.end.utc` | String | 예 | UTC 종료 시간 — 형식: `YYYY-MM-DDTHH:MM:SSZ`. `start.utc`보다 이후여야 합니다. |
| `event.end.timezone` | String | 예 | 종료 시간의 IANA 시간대 |
| `event.currency` | String | 예 | ISO 4217 통화 코드 — 예: `USD` |
| `event.online_event` | Boolean | 아니오 | 온라인 전용 이벤트는 `true`. 기본값: `false`. 장소와 함께 설정할 수 없습니다. |
| `event.capacity` | Integer | 아니오 | 최대 참석자 수. 생략하면 전체 티켓 클래스 수량의 합으로 계산됩니다. |
| `event.listed` | Boolean | 아니오 | Eventbrite에서 이벤트를 공개적으로 검색이 가능하게 하려면 `true`. 기본값: `false`. |
| `event.invite_only` | Boolean | 아니오 | 초대받은 참석자만 접근하도록 제한하려면 `true`. 기본값: `false`. |

**요청 예시**

```bash
curl --request POST \
  --header "Authorization: Bearer YOUR_PRIVATE_TOKEN" \
  --header "Content-Type: application/json" \
  --data '{
    "event": {
      "name": { "html": "<p>내 이벤트</p>" },
      "start": { "timezone": "UTC", "utc": "2026-06-01T09:00:00Z" },
      "end":   { "timezone": "UTC", "utc": "2026-06-01T17:00:00Z" },
      "currency": "USD"
    }
  }' \
  "https://www.eventbriteapi.com/v3/organizations/{organization_id}/events/"
```

**응답: `200 OK`**

```json
{
  "id": "12345",
  "name": {
    "text": "내 이벤트",
    "html": "<p>내 이벤트</p>"
  },
  "start": {
    "timezone": "UTC",
    "utc": "2026-06-01T09:00:00Z",
    "local": "2026-06-01T09:00:00"
  },
  "end": {
    "timezone": "UTC",
    "utc": "2026-06-01T17:00:00Z",
    "local": "2026-06-01T17:00:00"
  },
  "status": "draft",
  "url": "https://www.eventbrite.com/e/12345",
  "created": "2026-05-01T10:00:00Z"
}
```

**응답 필드**

| 필드 | 타입 | 설명 |
| --- | --- | --- |
| `id` | String | 생성된 이벤트의 고유 식별자 |
| `name.text` | String | 일반 텍스트 형식의 이벤트 이름 |
| `name.html` | String | HTML 형식의 이벤트 이름 |
| `start.utc` | String | UTC 시작 시간 |
| `start.local` | String | 이벤트 현지 시간대의 시작 시간 |
| `end.utc` | String | UTC 종료 시간 |
| `end.local` | String | 이벤트 현지 시간대의 종료 시간 |
| `status` | String | 이벤트 상태. 생성 시 항상 `draft`입니다. [이벤트 게시](#이벤트-게시)로 공개 상태로 전환할 수 있습니다. |
| `url` | String | Eventbrite의 이벤트 공개 URL |
| `created` | String | 이벤트 생성 시각 — UTC |

<br>

### 이벤트 조회

단일 이벤트의 상세 정보를 반환합니다.

**엔드포인트**

```
GET /events/{event_id}/
```

**경로 파라미터**

| 이름 | 타입 | 필수 | 설명 | 예시 |
| --- | --- | --- | --- | --- |
| `event_id` | String | 예 | 조회할 이벤트의 고유 ID | `12345` |

**요청 예시**

```bash
curl --request GET \
  --header "Authorization: Bearer YOUR_PRIVATE_TOKEN" \
  "https://www.eventbriteapi.com/v3/events/{event_id}/"
```

**응답: `200 OK`**

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
  "url": "https://www.eventbrite.com/e/12345",
  "capacity": 100,
  "currency": "USD"
}
```

**응답 필드**

| 필드 | 타입 | 설명 |
| --- | --- | --- |
| `id` | String | 이벤트의 고유 식별자 |
| `name.text` | String | 일반 텍스트 형식의 이벤트 이름 |
| `name.html` | String | HTML 형식의 이벤트 이름 |
| `description.text` | String | 일반 텍스트 형식의 이벤트 설명. 설명이 없는 초안(draft) 이벤트에서는 `null`입니다. |
| `description.html` | String | HTML 형식의 이벤트 설명. 설명이 없는 초안(draft) 이벤트에서는 `null`입니다. |
| `start.utc` | String | UTC 시작 시간 |
| `start.local` | String | 이벤트 현지 시간대의 시작 시간 |
| `end.utc` | String | UTC 종료 시간 |
| `end.local` | String | 이벤트 현지 시간대의 종료 시간 |
| `status` | String | 이벤트 상태 — `draft`, `live`, `started`, `ended`, `completed`, `canceled` |
| `url` | String | Eventbrite의 이벤트 공개 페이지 URL |
| `capacity` | Integer | 최대 참석자 수. 설정되지 않은 경우 `null` |
| `currency` | String | 이벤트의 ISO 4217 통화 코드 |

<br>

### 이벤트 수정

기존 이벤트의 필드를 수정합니다. 변경할 필드만 포함하세요.
요청 본문에 포함되지 않은 필드는 변경되지 않습니다.

**엔드포인트**

```
POST /events/{event_id}/
```

**경로 파라미터**

| 이름 | 타입 | 필수 | 설명 | 예시 |
| --- | --- | --- | --- | --- |
| `event_id` | String | 예 | 수정할 이벤트의 고유 ID | `12345` |

**요청 본문 파라미터**

변경할 필드만 포함하세요.

| 필드 | 타입 | 설명 |
| --- | --- | --- |
| `event.name.html` | String | 수정할 HTML 형식의 이벤트 이름 |
| `event.description.html` | String | 수정할 HTML 형식의 이벤트 설명 |
| `event.start.utc` | String | 수정할 UTC 시작 시간 — 형식: `YYYY-MM-DDTHH:MM:SSZ` |
| `event.start.timezone` | String | 수정할 시작 시간의 IANA 시간대 |
| `event.end.utc` | String | 수정할 UTC 종료 시간 — 형식: `YYYY-MM-DDTHH:MM:SSZ`. `start.utc`보다 이후여야 합니다. |
| `event.end.timezone` | String | 수정할 종료 시간의 IANA 시간대 |
| `event.capacity` | Integer | 수정할 최대 참석자 수 |
| `event.listed` | Boolean | `true`로 설정하면 이벤트를 공개 검색 가능, `false`로 설정하면 비공개 |
| `event.invite_only` | Boolean | `true`로 설정하면 초대받은 참석자만 접근 가능 |
| `event.online_event` | Boolean | `true`로 설정하면 온라인 전용 이벤트. 장소와 함께 설정할 수 없습니다. |

**요청 예시**

```bash
curl --request POST \
  --header "Authorization: Bearer YOUR_PRIVATE_TOKEN" \
  --header "Content-Type: application/json" \
  --data '{
    "event": {
      "name": { "html": "<p>수정된 이벤트 이름</p>" },
      "capacity": 200
    }
  }' \
  "https://www.eventbriteapi.com/v3/events/{event_id}/"
```

**응답: `200 OK`**

수정된 이벤트 객체 전체를 반환합니다.
응답 필드는 [이벤트 조회](#이벤트-조회)와 동일합니다.

<br>

### 이벤트 게시

초안(draft) 이벤트를 게시해서 Eventbrite에 공개합니다.
이벤트가 게시되려면 이벤트의 이름, 설명, 티켓 클래스, 결제 옵션이 모두 설정되어 있어야 합니다.

**엔드포인트**

```
POST /events/{event_id}/publish/
```

**경로 파라미터**

| 이름 | 타입 | 필수 | 설명 | 예시 |
| --- | --- | --- | --- | --- |
| `event_id` | String | 예 | 게시할 이벤트의 고유 ID | `12345` |

**요청 본문**

요청 본문이 필요하지 않습니다.

**요청 예시**

```bash
curl --request POST \
  --header "Authorization: Bearer YOUR_PRIVATE_TOKEN" \
  --header "Content-Type: application/json" \
  "https://www.eventbriteapi.com/v3/events/{event_id}/publish/"
```

**응답: `200 OK`**

```json
{
  "published": true
}
```

| 필드 | 타입 | 설명 |
| --- | --- | --- |
| `published` | Boolean | 이벤트가 성공적으로 게시되면 `true`를 반환합니다. 필수 항목이 누락되어 게시에 실패하면 API는 누락된 필드 정보와 함께 `400` 오류를 반환합니다. |

<br>

### 이벤트 삭제

이벤트를 영구적으로 삭제합니다. 이 작업은 되돌릴 수 없습니다.

이벤트를 삭제하려면 진행 중이거나 완료된 주문이 없어야 합니다.
주문이 있는 이벤트를 삭제할 경우, `403` 오류를 반환합니다.

**엔드포인트**

```
DELETE /events/{event_id}/
```

**경로 파라미터**

| 이름 | 타입 | 필수 | 설명 | 예시 |
| --- | --- | --- | --- | --- |
| `event_id` | String | 예 | 삭제할 이벤트의 고유 ID | `12345` |

**요청 예시**

```bash
curl --request DELETE \
  --header "Authorization: Bearer YOUR_PRIVATE_TOKEN" \
  "https://www.eventbriteapi.com/v3/events/{event_id}/"
```

**응답: `200 OK`**

```json
{
  "deleted": true
}
```

| 필드 | 타입 | 설명 |
| --- | --- | --- |
| `deleted` | Boolean | 이벤트가 성공적으로 삭제되면 `true`를 반환합니다 |

<br>

## 다음 단계

- [인증 가이드](../guides/authentication.md)
- [응답 처리 가이드](../guides/response-handling.md)
- [오류 레퍼런스](../api/error-reference.md)
- [페이지네이션 가이드](../guides/pagination.md)
- [코드 예제](../examples/code-examples.md)

<br>
