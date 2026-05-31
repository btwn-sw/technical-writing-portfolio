# 빠른 참조 가이드

Eventbrite API 핵심 정보를 빠르게 확인하세요. API를 이미 알고 있고
엔드포인트, 헤더, 상태 코드, 요청 제한을 빠르게 확인해야 할 때
사용하는 페이지입니다.

전체 파라미터 정의, 요청 본문 스키마, 응답 필드 설명은
[API 레퍼런스](../api/api-reference.md)를 참고하세요.

<br>

## 목차

- [Base URL](#base-url)
- [인증](#인증)
- [이벤트 엔드포인트](#이벤트-엔드포인트)
- [요청 헤더](#요청-헤더)
- [응답 필드](#응답-필드)
- [HTTP 상태 코드](#http-상태-코드)
- [요청 제한](#요청-제한)
- [빠른 요청 예시](#빠른-요청-예시)

<br>

## Base URL

```
https://www.eventbriteapi.com/v3
```

- API 버전: `v3`
- 모든 엔드포인트는 `/v3/`로 시작합니다.
- 요청 및 응답 형식: `application/json`

<br>

## 인증

```
Authorization: Bearer YOUR_PRIVATE_TOKEN
```

- 토큰을 환경 변수에 저장하세요 — 소스 코드에 포함하지 마세요.
- [계정 설정 → API Keys](https://www.eventbrite.com/account-settings/apps)에서
토큰을 생성하거나 재발급할 수 있습니다.

👉 [인증 가이드](../guides/authentication.md)

<br>

## 이벤트 엔드포인트

| 작업 | 메서드 | 엔드포인트 |
| --- | --- | --- |
| 조직별 이벤트 목록 조회 | `GET` | `/organizations/{organization_id}/events/` |
| 이벤트 생성 | `POST` | `/organizations/{organization_id}/events/` |
| 이벤트 조회 | `GET` | `/events/{event_id}/` |
| 이벤트 수정 | `POST` | `/events/{event_id}/` |
| 이벤트 게시 | `POST` | `/events/{event_id}/publish/` |
| 이벤트 삭제 | `DELETE` | `/events/{event_id}/` |

👉 [API 레퍼런스](../api/api-reference.md)

<br>

## 요청 헤더

| 헤더 | 값 | 필수 여부 |
| --- | --- | --- |
| `Authorization` | `Bearer YOUR_PRIVATE_TOKEN` | 모든 요청에 필수 |
| `Content-Type` | `application/json` | POST 요청에 필수. GET과 DELETE에서는 생략 가능 |

👉 [인증 가이드](../guides/authentication.md)

<br>

## 응답 필드

| 필드 | 타입 | 설명 |
| --- | --- | --- |
| `id` | String | 이벤트 고유 식별자 |
| `name.text` | String | 일반 텍스트 형식의 이벤트 제목 |
| `name.html` | String | HTML 형식의 이벤트 제목 |
| `description.text` | String | 일반 텍스트 형식의 이벤트 설명. 주최자가 설명을 작성하지 않은 경우 `null` |
| `description.html` | String | HTML 형식의 이벤트 설명. 주최자가 설명을 작성하지 않은 경우 `null` |
| `status` | String | 이벤트 상태 — 가능한 값: `draft`, `live`, `started`, `ended`, `completed`, `canceled` |
| `start.utc` | String | UTC 시작 시간 — 형식: `YYYY-MM-DDTHH:MM:SSZ` |
| `start.local` | String | 이벤트 현지 시간대 기준 시작 시간 |
| `end.utc` | String | UTC 종료 시간 — 형식: `YYYY-MM-DDTHH:MM:SSZ` |
| `end.local` | String | 이벤트 현지 시간대 기준 종료 시간 |
| `capacity` | Integer | 최대 참석자 수. 설정하지 않으면 `null` |
| `currency` | String | ISO 4217 통화 코드 |
| `url` | String | Eventbrite 이벤트 공개 페이지 URL |

👉 [응답 처리 가이드](../guides/response-handling.md)

<br>

## HTTP 상태 코드

| 코드 | 오류 코드 | 의미 | 조치 |
| --- | --- | --- | --- |
| `200` | — | 요청 성공 | — |
| `400` | `FIELD_INVALID` | 유효하지 않거나 누락된 필드 | 요청 본문의 필수 필드와 값 형식 확인 |
| `400` | `VENUE_AND_ONLINE` | 장소와 `online_event`가 함께 설정됨 | 장소를 제거하거나 `online_event`를 `false`로 설정 |
| `400` | `BAD_CONTINUATION_TOKEN` | 페이지네이션 토큰이 잘못됐거나 만료됨 | `continuation` 파라미터 없이 첫 페이지부터 다시 시작 |
| `401` | `NO_AUTH` | 토큰이 없거나 유효하지 않음 | `Authorization : Bearer YOUR_PRIVATE_TOKEN` 형식 확인 |
| `403` | `NOT_AUTHORIZED` | 권한 부족 | 리소스가 내 계정에 속하는지, 이벤트 상태가 해당 작업을 허용하는지 확인 |
| `404` | `NOT_FOUND` | 리소스를 찾을 수 없음 | 이벤트 URL에서 `event_id`의 숫자 ID만 복사했는지 확인 |
| `429` | — | 요청 제한 초과 | `X-Apiary-RateLimit-Remaining` 헤더 확인 후 1시간 대기 |

👉 [오류 레퍼런스](../api/error-reference.md)

<br>

## 요청 제한

| 제한 유형 | 제한 |
| --- | --- |
| 전체 요청 | 시간당 2,000건 |
| 전체 요청 | 일일 48,000건 |
| 이벤트 생성 또는 게시 | 시간당 200건 |

**요청 제한 헤더:**

| 헤더 | 설명 |
| --- | --- |
| `X-Apiary-RateLimit-Limit` | 현재 윈도우에서 허용된 최대 요청 수 |
| `X-Apiary-RateLimit-Remaining` | 제한이 초기화되기 전까지 남은 요청 수 |

→ [오류 레퍼런스 — 429](../api/error-reference.md#429-too-many-requests)

<br>

## 빠른 요청 예시

**이벤트 조회:**

```bash
curl --request GET \
  --header "Authorization: Bearer YOUR_PRIVATE_TOKEN" \
  "https://www.eventbriteapi.com/v3/events/{event_id}/"
```

**조직별 이벤트 목록 조회:**

```bash
curl --request GET \
  --header "Authorization: Bearer YOUR_PRIVATE_TOKEN" \
  "https://www.eventbriteapi.com/v3/organizations/{organization_id}/events/"
```

👉 [코드 예제](../examples/code-examples.md)

<br>
