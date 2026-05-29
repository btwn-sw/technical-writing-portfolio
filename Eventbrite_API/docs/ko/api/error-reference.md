# 오류 레퍼런스

Eventbrite API가 반환하는 오류 코드, 원인, 해결 방법을 조회하세요.

오류 처리 코드 예제는 [응답 처리 가이드](../guides/response-handling.md)를 참고하세요.
단계별 디버깅 방법은 [트러블슈팅 가이드](../guides/troubleshooting.md)를 참고하세요.

<br>

## 목차

- [오류 응답 형식](#오류-응답-형식)
- [오류 코드](#오류-코드)
    - [400 Bad Request](#400-bad-request)
    - [401 Unauthorized](#401-unauthorized)
    - [403 Forbidden](#403-forbidden)
    - [404 Not Found](#404-not-found)
    - [429 Too Many Requests](#429-too-many-requests)

<br>

## 오류 응답 형식

Eventbrite API의 모든 오류는 일관된 JSON 구조로 반환됩니다.

```json
{
  "error": "ERROR_CODE",
  "error_description": "오류에 대한 사람이 읽을 수 있는 설명.",
  "status_code": 400
}
```

| 필드 | 타입 | 설명 |
| --- | --- | --- |
| `error` | String | 기계가 읽을 수 있는 오류 코드. 애플리케이션 로직에서 오류 유형을 식별할 때 이 필드를 사용하세요. |
| `error_description` | String | 무엇이 잘못됐는지 설명하는 사람이 읽을 수 있는 메시지. 로깅과 디버깅에 유용하지만, 최종 사용자에게 직접 표시하는 용도로는 적합하지 않습니다. |
| `status_code` | Integer | HTTP 상태 코드. 응답 상태 코드와 일치합니다. |

<br>

## 오류 코드

### 400 Bad Request

유효하지 않거나 충돌하는 파라미터가 포함되어 요청이 거부됐습니다.

| 오류 코드 | 원인 | 해결 방법 |
| --- | --- | --- |
| `FIELD_INVALID` | 필수 필드가 없거나 필드에 유효하지 않은 값이 포함되어 있습니다. 예를 들어 시작 시간보다 이전으로 설정된 종료 시간, 또는 인식할 수 없는 통화 코드가 해당됩니다. | [API 레퍼런스](../api/api-reference.md)에서 요청 본문을 확인하세요. 필수 필드가 모두 포함되어 있는지, 모든 값이 예상 형식과 일치하는지 검토하세요. |
| `VENUE_AND_ONLINE` | 같은 요청에 장소와 `online_event: true`가 함께 설정되어 있습니다. 이벤트는 오프라인 또는 온라인 중 하나만 선택할 수 있습니다. | 장소를 제거하거나 `online_event`를 `false`로 설정하세요. |
| `BAD_CONTINUATION_TOKEN` | 쿼리 파라미터로 전달한 `continuation` 토큰이 잘못된 형식이거나 만료됐습니다. | `continuation` 파라미터 없이 요청을 다시 보내서 첫 페이지부터 페이지네이션을 다시 시작하세요. [페이지네이션 가이드](../guides/pagination-guide.md)를 참고하세요. |



### 401 Unauthorized

인증에 실패해서 요청이 거부됐습니다.

| 오류 코드 | 원인 | 해결 방법 |
| --- | --- | --- |
| `NO_AUTH` | `Authorization` 헤더가 없거나, 토큰 형식이 잘못됐거나, 토큰이 취소됐습니다. | 헤더가 `Authorization: Bearer YOUR_PRIVATE_TOKEN` 형식으로 정확히 작성됐는지 확인하세요. 필요하다면 [API Keys 페이지](https://www.eventbrite.com/account-settings/apps)에서 새 토큰을 발급받으세요. |



### 403 Forbidden

요청은 인증됐지만 토큰에 해당 작업을 수행할 권한이 없습니다.

| 오류 코드 | 원인 | 해결 방법 |
| --- | --- | --- |
| `NOT_AUTHORIZED` | 이벤트가 다른 조직이나 계정에 속해 있거나, 토큰에 필요한 권한이 없거나, 이벤트의 현재 상태에서 해당 작업이 허용되지 않습니다. 예를 들어 기존 주문이 있는 이벤트를 삭제하려는 경우가 해당됩니다. | `event_id` 또는 `organization_id`가 토큰을 발급한 계정에 속하는지 확인하세요. 이벤트의 `status` 필드를 확인하세요. 일부 작업은 상태에 따라 제한됩니다. |



### 404 Not Found

요청한 리소스가 존재하지 않습니다.

| 오류 코드 | 원인 | 해결 방법 |
| --- | --- | --- |
| `NOT_FOUND` | `event_id` 또는 `organization_id`가 잘못됐거나, 이벤트가 삭제됐거나, 토큰에 조회 권한이 없습니다. | Eventbrite 계정에서 이벤트를 직접 열어서 ID를 확인하세요. `event_id`는 이벤트 URL 끝의 숫자입니다: `https://www.eventbrite.com/e/my-event-123456789`. |



### 429 Too Many Requests

요청 제한을 초과해서 요청이 거부됐습니다.

| 제한 유형 | 제한 |
| --- | --- |
| 전체 요청 | 시간당 2,000건 |
| 전체 요청 | 일일 48,000건 |
| 이벤트 생성 | 시간당 200건 |
| 이벤트 게시 | 시간당 200건 |

이 오류는 JSON 오류 코드를 포함하지 않습니다. 아래 응답 헤더로
상황을 진단하세요.

| 헤더 | 설명 |
| --- | --- |
| `X-Apiary-RateLimit-Limit` | 현재 윈도우에서 허용된 최대 요청 수 |
| `X-Apiary-RateLimit-Remaining` | 제한이 초기화되기 전까지 남은 요청 수 |

`X-Apiary-RateLimit-Remaining`이 `0`이 되면 윈도우가 초기화될 때까지
기다린 후 요청을 다시 보내세요. 대량 작업을 처리할 때는 요청 사이에
시간 간격을 두고 허용된 요청 수를 유지하세요.

<br>

## 다음 단계

- [API 레퍼런스](../api/api-reference.md)
- [응답 처리 가이드](../guides/response-handling.md)
- [트러블슈팅 가이드](../guides/troubleshooting.md)

<br>
