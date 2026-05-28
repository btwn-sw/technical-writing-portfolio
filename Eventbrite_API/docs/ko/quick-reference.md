# 빠른 참조 가이드

Eventbrite API 핵심 정보를 빠르게 알아보세요. API를 이미 알고 있고
엔드포인트, 헤더, 상태 코드, 요청 제한을 빠르게 확인해야 할 때
사용하는 가이드입니다.

전체 문서는 [API 레퍼런스]()에서 시작하세요.

<br>

## 목차

- [Base URL 및 버전](#base-url-및-버전)
- [인증](#인증)
- [이벤트 엔드포인트](#이벤트-엔드포인트)
- [요청 헤더](#요청-헤더)
- [응답 필드](#응답-필드)
- [HTTP 상태 코드](#http-상태-코드)
- [요청 제한](#요청-제한)
- [빠른 요청 예시](#빠른-요청-예시)

<br>

## Base URL 및 버전

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

- 토큰을 환경 변수에 저장하세요 — 소스 코드에 넣지 마세요.
- [계정 설정 → API 키](https://www.eventbrite.com/account-settings/apps)에서
토큰을 재발급할 수 있습니다.

→ [인증 가이드]()

<br>

## 이벤트 엔드포인트

| 작업 | 메서드 | 엔드포인트 |
| --- | --- | --- |
| 이벤트 생성 | `POST` | `/organizations/{organization_id}/events/` |
| 이벤트 조회 | `GET` | `/events/{event_id}/` |
| 이벤트 수정 | `POST` | `/events/{event_id}/` |
| 이벤트 삭제 | `DELETE` | `/events/{event_id}/` |

→ [API 레퍼런스]()

<br>

## 요청 헤더

| 헤더 | 값 |
| --- | --- |
| `Authorization` | `Bearer YOUR_PRIVATE_TOKEN` |
| `Content-Type` | `application/json` |

<br>

## 응답 필드

| 필드 | 타입 | 설명 |
| --- | --- | --- |
| `id` | String | 이벤트 고유 식별자 |
| `name.text` | String | 일반 텍스트 형식의 이벤트 제목 |
| `name.html` | String | HTML 형식의 이벤트 제목 |
| `description.text` | String | 일반 텍스트 형식의 이벤트 설명 |
| `description.html` | String | HTML 형식의 이벤트 설명 |
| `status` | String | 이벤트 상태 — `draft`, `live`, `started`, `ended`, `completed`, `canceled` |
| `start.utc` | String | UTC 시작 시간 |
| `end.utc` | String | UTC 종료 시간 |

→ [응답 처리 가이드]()

<br>

## HTTP 상태 코드

| 코드 | 오류 코드 | 의미 | 조치 |
| --- | --- | --- | --- |
| `200` | — | 요청 성공 | — |
| `400` | `FIELD_INVALID` | 유효하지 않거나 충돌하는 파라미터 | API 레퍼런스에서 요청 본문 확인 |
| `401` | `NO_AUTH` | 토큰이 없거나 유효하지 않음 | `Authorization` 헤더 형식과 토큰 값 확인 |
| `403` | `NOT_AUTHORIZED` | 권한 부족 | 이벤트가 내 계정에 속하는지 확인 |
| `404` | `NOT_FOUND` | 리소스를 찾을 수 없음 | `event_id`가 올바른지 확인 |
| `429` | — | 요청 제한 초과 | `X-Apiary-RateLimit-Remaining` 헤더 확인 |

→ [트러블슈팅 가이드]()

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
| `X-Apiary-RateLimit-Remaining` | 제한 초기화 전 남은 요청 수 |

→ [API 레퍼런스 — 요청 제한]()

<br>

## 빠른 요청 예시

**이벤트 조회:**

```bash
curl --request GET \\
  --header "Authorization: Bearer YOUR_PRIVATE_TOKEN" \\
  "<https://www.eventbriteapi.com/v3/events/{event_id}/>"
```

→ [코드 예제]()

<br>
