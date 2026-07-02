# 용어집

Eventbrite API 문서 전반에서 사용하는 주요 용어의 정의입니다.
용어는 알파벳 순으로 정렬되어 있습니다. 다른 문서에서 용어가 처음 등장할 때
이 용어집으로 링크됩니다.

<br>

## **A**

**Access Token (액세스 토큰)**
OAuth 흐름이 반환하는 자격 증명으로, 특정 사용자를 대신해서 API 요청을
인증합니다. 프라이빗 토큰과 동일하게 `Authorization` 헤더에 넣어서 사용합니다.
[인증 가이드](../guides/authentication.md)를 참고하세요.

**API Key (API 키)**
Eventbrite 개발자 대시보드에서 발급하는 앱 수준의 자격 증명입니다.
서버 사이드 OAuth 흐름에서 클라이언트 시크릿(Client Secret)과 함께 사용합니다.
프라이빗 토큰과는 다릅니다.
[인증 가이드](../guides/authentication.md)를 참고하세요.

**Authorization Header (Authorization 헤더)**
Eventbrite API 요청을 인증하는 HTTP 요청 헤더입니다.
`Authorization: Bearer YOUR_PRIVATE_TOKEN` 형식으로 작성해야 합니다.
유효한 Authorization 헤더가 없으면 `401 Unauthorized`를 반환합니다.

<br>

## **B**

**Base URL (베이스 URL)**
모든 Eventbrite API 엔드포인트의 공통 루트 URL입니다:
`https://www.eventbriteapi.com/v3`. 모든 엔드포인트 경로는 `/v3/`로
시작합니다. `eventbrite.com`이 아닌 `eventbriteapi.com`을 사용해야 합니다.

**Bearer Token (베어러 토큰)**
`Authorization` 헤더에서 `Bearer` 접두사와 함께 사용하는 인증 토큰입니다.
형식: `Authorization: Bearer YOUR_PRIVATE_TOKEN`. Eventbrite API에서
베어러 토큰은 프라이빗 토큰 또는 OAuth 액세스 토큰을 가리킵니다.

<br>

## **C**

**Client Secret (클라이언트 시크릿)**
API 키와 짝을 이루는 비밀 자격 증명입니다. 서버 사이드 OAuth 흐름에서
인증 코드를 액세스 토큰으로 교환할 때 API 키와 함께 사용됩니다.
반드시 기밀로 유지해야 하며 클라이언트 측 코드에 노출하지 마세요.

**Continuation Token (컨티뉴에이션 토큰)**
추가 페이지가 있을 때 페이지네이션 응답에 포함되는 문자열 값입니다.
다음 요청에서 이 값을 `continuation` 쿼리 파라미터로 넘기면 다음 페이지
결과를 가져옵니다. `has_more_items`가 `false`이면 응답에 포함되지 않습니다.
요청 사이에 이벤트 목록이 변경되면 토큰이 만료될 수 있습니다.
[페이지네이션 가이드](../guides/pagination.md)를 참고하세요.

**Currency Code (통화 코드)**
ISO 4217 표준에 따른 세 자리 통화 식별 코드입니다.
(예: 미국 달러는 `USD`, 대한민국 원은 `KRW`.)
이벤트 생성 시 필수 값입니다.

<br>

## **D**

**Draft (초안)**
새로 생성된 이벤트의 초기 상태입니다. 초안(draft) 상태의 이벤트는
Eventbrite에 공개되지 않으며, 일부 필드는 `null`을 반환합니다.
Publish 엔드포인트로 공개 상태(`live`)로 전환합니다.
[API 레퍼런스 — 이벤트 게시](../api/api-reference.md#이벤트-게시)를 참고하세요.

<br>

## **E**

**Event (이벤트)**
Eventbrite API의 핵심 리소스입니다. 이벤트는 하나의 조직(Organization)에
속하며 `draft`, `live`, `started`, `ended`, `completed`, `canceled` 중
하나의 상태를 가집니다.

**Event ID (`event_id`)**
이벤트의 고유 숫자 식별자입니다. Eventbrite 이벤트 URL 끝에서 확인할 수
있습니다: `https://www.eventbrite.com/e/my-event-name-{event_id}`.
이벤트 조회, 수정, 게시, 삭제 등 단일 이벤트 작업에 모두 필요합니다.

<br>

## **H**

**`has_more_items`**
페이지네이션 객체의 Boolean 필드입니다. `true`이면 추가 페이지가 있으며
응답에 `continuation` 토큰이 포함됩니다. `false`이면 현재 페이지가
마지막이며 `continuation` 토큰이 포함되지 않습니다.
다음 페이지를 요청하기 전에 반드시 이 필드를 먼저 확인하세요.
[페이지네이션 가이드](../guides/pagination.md)를 참고하세요.

<br>

## **I**

**IANA Timezone (IANA 시간대)**
IANA 시간대 데이터베이스의 시간대 식별자입니다. 이벤트의 현지 시간을
지정할 때 사용합니다. (예: `America/Los_Angeles`, `Asia/Seoul`, `UTC`.)
이벤트 생성 및 수정 시 UTC 시간 필드와 함께 필수로 제공해야 합니다.

<br>

## **L**

**Live (라이브)**
이벤트가 게시되어 Eventbrite에 공개된 상태입니다.
Publish 엔드포인트를 통해 `draft`에서 전환됩니다.

<br>

## **M**

**Multipart Text (멀티파트 텍스트)**
이벤트 이름과 설명에 쓰이는 필드 타입입니다. API 응답에서 두 가지 형식으로
반환됩니다: `text`(일반 텍스트, 알림이나 로그에 사용)와 `html`(HTML 형식,
웹 인터페이스 렌더링에 사용). 일반 텍스트가 필요한 곳에 `html` 값을 넘기지
마세요.
[응답 처리 가이드](../guides/response-handling.md)를 참고하세요.

<br>

## **O**

**OAuth 2.0**
Eventbrite API가 사용하는 인증 프레임워크입니다. 애플리케이션이 사용자를
대신해서 API에 접근할 수 있도록 권한을 부여합니다. Eventbrite는 프라이빗
토큰(Personal Access Token)과 서버 사이드 OAuth 흐름을 모두 지원합니다.
[인증 가이드](../guides/authentication.md)를 참고하세요.

**Organization (조직)**
이벤트를 소유하는 Eventbrite 계정 단위입니다. 모든 이벤트는 조직 아래에
생성됩니다. 이벤트 생성 및 목록 조회 시 `organization_id`가 필요합니다.

**Organization ID (`organization_id`)**
Eventbrite 조직의 고유 식별자입니다. 이벤트 생성과 조직별 이벤트 목록
조회에 필요합니다. `GET /v3/users/me/organizations/`로 조직 ID를 확인할 수
있습니다.

<br>

## **P**

**`page_size`**
페이지네이션 응답에서 페이지당 반환되는 결과 수입니다.
기본값은 50건입니다. 마지막 페이지에서는 `page_size`보다 적은 수의
결과가 반환될 수 있습니다.
[페이지네이션 가이드](../guides/pagination.md)를 참고하세요.

**Pagination (페이지네이션)**
Eventbrite API가 대량의 결과를 여러 페이지로 나눠서 반환하는 방식입니다.
페이지네이션 응답에는 `has_more_items`, `continuation`, `page_number`,
`page_size`, `page_count`, `object_count`가 담긴 `pagination` 객체가
포함됩니다.
[페이지네이션 가이드](../guides/pagination.md)를 참고하세요.

**Private Token (프라이빗 토큰)**
특정 Eventbrite 계정에 연결된 만료 기한이 없는 개인 액세스 토큰입니다.
API 요청 인증을 위해 `Authorization` 헤더에 넣어서 사용합니다. 테스트와 단일
계정을 사용하는 서버 사이드 통합에 적합합니다.
Eventbrite [API Keys 페이지](https://www.eventbrite.com/account-settings/app)에서
발급합니다.
[인증 가이드](../guides/authentication.md)를 참고하세요.

<br>

## **R**

**Rate Limit (요청 제한)**
일정 시간 안에 허용되는 API 요청 수의 상한선입니다. Eventbrite는 전체 요청에
시간당 2,000건, 일일 48,000건 제한을 적용하며, 이벤트 생성과 게시에는
별도로 시간당 200건 제한을 적용합니다. 제한을 초과하면 `429 Too Many
Requests`를 반환합니다. 윈도우가 1시간 동안 초기화될 때까지 기다린 후
요청을 다시 보내세요.
[오류 레퍼런스](../api/error-reference.md)를 참고하세요.

<br>

## **S**

**Status — Event (이벤트 상태)**
이벤트의 현재 상태를 나타냅니다. 가능한 값:

| **상태** | **설명** |
| --- | --- |
| `draft` | 생성됐지만 아직 게시되지 않은 상태. 일부 필드는 `null`을 반환합니다. |
| `live` | 게시되어 Eventbrite에 공개된 상태 |
| `started` | 이벤트 시작 시간이 지난 상태 |
| `ended` | 이벤트 종료 시간이 지난 상태 |
| `completed` | 이벤트가 완료 처리된 상태 |
| `canceled` | 이벤트가 취소된 상태 |

<br>

## **U**

**UTC (협정 세계시, Coordinated Universal Time)**
Eventbrite API의 모든 날짜·시간 필드에 쓰이는 시간 표준입니다.
모든 `utc` 필드는 `YYYY-MM-DDTHH:MM:SSZ` 형식을 따릅니다. UTC 필드와 함께
반드시 IANA 시간대를 제공해야 Eventbrite가 참석자에게 올바른 현지 시간을
표시할 수 있습니다.

<br>

---

## **ㄱ**

**기본 URL → Base URL 참고**

<br>

## **ㄴ**

**날짜·시간 형식 → UTC 참고**

<br>

## **ㄷ**

**드래프트 → 초안(Draft) 참고**

<br>

## **ㄹ**

**라이브 (Live)**
이벤트가 게시되어 Eventbrite에 공개된 상태입니다.
Publish 엔드포인트를 통해 `draft`에서 전환됩니다.

**레퍼런스 → API 레퍼런스 참고**

<br>

## **ㅁ**

**멀티파트 텍스트 (Multipart Text)**
이벤트 이름과 설명에 쓰이는 필드 타입입니다. API 응답에서 두 가지 형식으로
반환됩니다: `text`(일반 텍스트, 알림이나 로그에 사용)와 `html`(HTML 형식,
웹 인터페이스 렌더링에 사용). 일반 텍스트가 필요한 곳에 `html` 값을 넘기지
마세요.
[응답 처리 가이드](../guides/response-handling.md)를 참고하세요.

<br>

## **ㅂ**

**베어러 토큰 (Bearer Token)**
`Authorization` 헤더에서 `Bearer` 접두사와 함께 사용하는 인증 토큰입니다.
형식: `Authorization: Bearer YOUR_PRIVATE_TOKEN`. Eventbrite API에서
베어러 토큰은 프라이빗 토큰 또는 OAuth 액세스 토큰을 가리킵니다.

**베이스 URL (Base URL)**
모든 Eventbrite API 엔드포인트의 공통 루트 URL입니다:
`https://www.eventbriteapi.com/v3`. 모든 엔드포인트 경로는 `/v3/`로
시작합니다. `eventbrite.com`이 아닌 `eventbriteapi.com`을 사용해야 합니다.

<br>

## **ㅅ**

**상태 — 이벤트 (Status — Event)**
이벤트의 현재 상태를 나타냅니다. 가능한 값:

| **상태** | **설명** |
| --- | --- |
| `draft` | 생성됐지만 아직 게시되지 않은 상태. 일부 필드는 `null`을 반환합니다. |
| `live` | 게시되어 Eventbrite에 공개된 상태 |
| `started` | 이벤트 시작 시간이 지난 상태 |
| `ended` | 이벤트 종료 시간이 지난 상태 |
| `completed` | 이벤트가 완료 처리된 상태 |
| `canceled` | 이벤트가 취소된 상태 |

<br>

## **ㅇ**

**액세스 토큰 (Access Token)**
OAuth 흐름이 반환하는 자격 증명으로, 특정 사용자를 대신해서 API 요청을
인증합니다. 프라이빗 토큰과 동일하게 `Authorization` 헤더에 넣어서 씁니다.
[인증 가이드](../guides/authentication.md)를 참고하세요.

**요청 제한 (Rate Limit)**
일정 시간 안에 허용되는 API 요청 수의 상한선입니다. Eventbrite는 전체 요청에
시간당 2,000건, 일일 48,000건 제한을 적용하며, 이벤트 생성과 게시에는
별도로 시간당 200건 제한을 적용합니다. 제한을 초과하면 `429 Too Many
Requests`를 반환합니다. 1시간 윈도우가 초기화될 때까지 기다린 후
요청을 다시 보내세요.
[오류 레퍼런스](../api/error-reference.md)를 참고하세요.

**이벤트 (Event)**
Eventbrite API의 핵심 리소스입니다. 이벤트는 하나의 조직(Organization)에
속하며 `draft`, `live`, `started`, `ended`, `completed`, `canceled` 중
하나의 상태를 가집니다.

**이벤트 ID (`event_id`)**
이벤트의 고유 숫자 식별자입니다. Eventbrite 이벤트 URL 끝에서 확인할 수
있습니다: `https://www.eventbrite.com/e/my-event-name-{event_id}`.
이벤트 조회, 수정, 게시, 삭제 등 단일 이벤트 작업에 모두 필요합니다.

**이벤트 상태 → 상태 — 이벤트 참고**

**인증 헤더 (Authorization Header)**
Eventbrite API 요청을 인증하는 HTTP 요청 헤더입니다.
`Authorization: Bearer YOUR_PRIVATE_TOKEN` 형식으로 작성해야 합니다.
유효한 Authorization 헤더가 없으면 `401 Unauthorized`를 반환합니다.

<br>

## **ㅈ**

**조직 (Organization)**
이벤트를 소유하는 Eventbrite 계정 단위입니다. 모든 이벤트는 조직 아래에
생성됩니다. 이벤트 생성 및 목록 조회 시 `organization_id`가 필요합니다.

**조직 ID (`organization_id`)**
Eventbrite 조직의 고유 식별자입니다. 이벤트 생성과 조직별 이벤트 목록
조회에 필요합니다. `GET /v3/users/me/organizations/`로 조직 ID를 확인할 수
있습니다.

**초안 (Draft)**
새로 생성된 이벤트의 초기 상태입니다. 초안(draft) 상태의 이벤트는
Eventbrite에 공개되지 않으며, 일부 필드는 `null`을 반환합니다.
Publish 엔드포인트로 공개 상태(`live`)로 전환합니다.
[API 레퍼런스 — 이벤트 게시](../api/api-reference.md#이벤트-게시)를 참고하세요.

<br>

## **ㅋ**

**컨티뉴에이션 토큰 (Continuation Token)**
추가 페이지가 있을 때 페이지네이션 응답에 포함되는 문자열 값입니다.
다음 요청에서 이 값을 `continuation` 쿼리 파라미터로 넘기면 다음 페이지
결과를 가져옵니다. `has_more_items`가 `false`이면 응답에 포함되지 않습니다.
요청 사이에 이벤트 목록이 변경되면 토큰이 만료될 수 있습니다.
[페이지네이션 가이드](../guides/pagination.md)를 참고하세요.

**클라이언트 시크릿 (Client Secret)**
API 키와 짝을 이루는 비밀 자격 증명입니다. 서버 사이드 OAuth 흐름에서
인증 코드를 액세스 토큰으로 교환할 때 API 키와 함께 사용합니다.
반드시 기밀로 유지해야 하며 클라이언트 측 코드에 노출하지 마세요.

<br>

## **ㅌ**

**통화 코드 (Currency Code)**
ISO 4217 표준에 따른 세 자리 통화 식별 코드입니다.
(예: 미국 달러는 `USD`, 대한민국 원은 `KRW`.)
이벤트 생성 시 필수 값입니다.

<br>

## **ㅍ**

**페이지네이션 (Pagination)**
Eventbrite API가 대량의 결과를 여러 페이지로 나눠서 반환하는 방식입니다.
페이지네이션 응답에는 `has_more_items`, `continuation`, `page_number`,
`page_size`, `page_count`, `object_count`가 담긴 `pagination` 객체가
포함됩니다.
[페이지네이션 가이드](../guides/pagination.md)를 참고하세요.

**프라이빗 토큰 (Private Token)**
특정 Eventbrite 계정에 연결된 만료 기한 없는 개인 액세스 토큰입니다.
API 요청 인증을 위해 `Authorization` 헤더에 넣어서 사용합니다. 테스트와 단일
계정을 쓰는 서버 사이드 통합에 적합합니다.
Eventbrite [API Keys 페이지](https://www.eventbrite.com/account-settings/apps)에서
발급합니다.
[인증 가이드](../guides/authentication.md)를 참고하세요.

<br>

## **ㅎ**

**`has_more_items`**
페이지네이션 객체의 Boolean 필드입니다. `true`이면 추가 페이지가 있으며
응답에 `continuation` 토큰이 포함됩니다. `false`이면 현재 페이지가
마지막이며 `continuation` 토큰이 포함되지 않습니다.
다음 페이지를 요청하기 전에 반드시 이 필드를 먼저 확인하세요.
페이지네이션 가이드를 참고하세요.

**협정 세계시 (UTC, Coordinated Universal Time)**
Eventbrite API의 모든 날짜·시간 필드에 쓰이는 시간 표준입니다.
모든 `utc` 필드는 `YYYY-MM-DDTHH:MM:SSZ` 형식을 따릅니다. UTC 필드와 함께
반드시 IANA 시간대를 제공해야 Eventbrite가 참석자에게 올바른 현지 시간을
표시할 수 있습니다.

<br>

## **다음 단계**

- [API 레퍼런스](../api/api-reference.md)
- [인증 가이드](../guides/authentication.md)
- [응답 처리 가이드](../guides/response-handling.md)
- [페이지네이션 가이드](../guides/pagination.md)

<br>
