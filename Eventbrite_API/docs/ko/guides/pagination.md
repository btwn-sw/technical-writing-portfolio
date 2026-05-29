# 페이지네이션 가이드

컨티뉴에이션 토큰(continuation token)을 사용해서 Eventbrite API에서
여러 페이지에 걸친 이벤트를 가져오세요. 이 가이드에서는 페이지네이션
응답의 구조, 페이지 이동 방법, 마지막 페이지 처리 방법을 안내합니다.

<br>

## 목차

- [페이지네이션 작동 방식](#페이지네이션-작동-방식)
- [페이지네이션 응답 구조](#페이지네이션-응답-구조)
- [단계별 가이드: 전체 이벤트 가져오기](#단계별-가이드-전체-이벤트-가져오기)
- [코드 예제](#코드-예제)
- [마지막 페이지 처리](#마지막-페이지-처리)

<br>

## 페이지네이션 작동 방식

목록 엔드포인트가 한 페이지에 담을 수 없는 결과를 반환할 때,
Eventbrite는 응답을 여러 페이지로 나눕니다. 각 페이지에는 결과 목록과 함께
`pagination` 객체가 포함됩니다.

페이지를 이동하려면 현재 응답의 `continuation` 토큰을 복사해서
다음 요청의 쿼리 파라미터로 넘기세요.
`has_more_items`가 `false`가 될 때까지 반복합니다.

<br>

## 페이지네이션 응답 구조

페이지네이션 응답은 `pagination` 객체와 결과 목록, 두 가지 최상위 섹션으로
구성됩니다.

```json
{
  "pagination": {
    "object_count": 4,
    "page_number": 1,
    "page_size": 2,
    "page_count": 2,
    "has_more_items": true,
    "continuation": "AEtFRyiWxkr0ZXyCJcnZ5U1-uSWXJ6vO0sxN06GbrDngaX5U5i8XYmEuZfmZZYB9Uq6bSizOLYoV"
  },
  "events": [
    { "id": "11111", "name": { "text": "이벤트 1" }, "status": "live" },
    { "id": "22222", "name": { "text": "이벤트 2" }, "status": "draft" }
  ]
}
```

**페이지네이션 필드:**

| 필드 | 타입 | 설명 |
| --- | --- | --- |
| `object_count` | Integer | 전체 페이지에 걸친 결과 총 개수 |
| `page_number` | Integer | 현재 페이지 번호. 1부터 시작합니다. |
| `page_size` | Integer | 페이지당 반환되는 최대 결과 수 |
| `page_count` | Integer | 전체 페이지 수 |
| `has_more_items` | Boolean | 추가 페이지가 있으면 `true`. 다음 페이지를 요청하기 전에 반드시 이 값을 먼저 확인하세요. |
| `continuation` | String | 다음 요청에 쿼리 파라미터로 넘길 토큰. `has_more_items`가 `true`일 때만 포함됩니다. |

<br>

## 단계별 가이드: 전체 이벤트 가져오기

조직에 속한 이벤트를 모두 가져오려면 아래 단계를 따르세요.

**1단계. 첫 번째 요청 보내기**

`continuation` 파라미터 없이 조직별 이벤트 목록 조회 엔드포인트를
호출하세요.

```bash
curl --request GET \
  --header "Authorization: Bearer YOUR_PRIVATE_TOKEN" \
  "https://www.eventbriteapi.com/v3/organizations/{organization_id}/events/"
```

**2단계. `has_more_items` 확인하기**

응답의 `pagination.has_more_items` 값을 확인하세요.

- `false`이면 — 결과를 모두 가져온 것입니다. 여기서 멈추세요.
- `true`이면 — 추가 페이지가 있습니다. 3단계로 이동하세요.

**3단계. 컨티뉴에이션 토큰 복사하기**

현재 응답의 `pagination.continuation` 값을 복사하세요.

**4단계. 다음 페이지 요청하기**

`continuation` 토큰을 쿼리 파라미터로 추가해서 같은 엔드포인트를 다시
호출하세요.

```bash
curl --request GET \
  --header "Authorization: Bearer YOUR_PRIVATE_TOKEN" \
  "https://www.eventbriteapi.com/v3/organizations/{organization_id}/events/?continuation=AEtFRyiWxkr0ZXyCJcnZ5U1-uSWXJ6vO0sxN06GbrDngaX5U5i8XYmEuZfmZZYB9Uq6bSizOLYoV"
```

**5단계. 완료될 때까지 반복하기**

`has_more_items`가 `false`가 될 때까지 2~4단계를 반복하세요.
마지막 페이지 응답에는 `continuation` 토큰이 포함되지 않습니다.

<br>

## 코드 예제

아래 Node.js 예제는 조직의 이벤트를 모두 가져와서 하나의 배열로 반환합니다.

```jsx
import fetch from "node-fetch";

async function getAllEvents(organizationId) {
  const events = [];
  let continuation = null;

  do {
    const url = new URL(
      `https://www.eventbriteapi.com/v3/organizations/${organizationId}/events/`
    );

    if (continuation) {
      url.searchParams.set("continuation", continuation);
    }

    const response = await fetch(url.toString(), {
      headers: {
        "Authorization": `Bearer ${process.env.EVENTBRITE_TOKEN}`
      }
    });

    if (!response.ok) {
      throw new Error(`요청 실패: ${response.status}`);
    }

    const data = await response.json();

    events.push(...data.events);

    continuation = data.pagination.has_more_items
      ? data.pagination.continuation
      : null;

  } while (continuation);

  return events;
}
```

<br>

## 마지막 페이지 처리

`has_more_items`가 `false`이면 응답의 `pagination` 객체에 `continuation`
필드가 포함되지 않습니다. 마지막 페이지에서 `pagination.continuation`에
접근하면 JavaScript에서는 `undefined`, Python에서는 `KeyError`가 발생합니다.

반드시 `has_more_items`를 먼저 읽고 루프를 멈춘 후에 `continuation` 토큰을
읽으세요.

```jsx
// 올바른 방법 — has_more_items를 먼저 확인한 후 continuation을 읽습니다
continuation = data.pagination.has_more_items
  ? data.pagination.continuation
  : null;

// 잘못된 방법 — 마지막 페이지에서 continuation이 undefined입니다
continuation = data.pagination.continuation;
```

`BAD_CONTINUATION_TOKEN` 오류가 반환되면 토큰이 만료됐거나 형식이
잘못된 것입니다. `continuation` 파라미터 없이 요청을 다시 보내서
첫 페이지부터 다시 시작하세요.
이 오류에 대한 자세한 내용은
[오류 레퍼런스](../api/error-reference.md)를 참고하세요.

<br>

## 다음 단계

- [API 레퍼런스 — 조직별 이벤트 목록 조회](../api/api-reference.md)
- [오류 레퍼런스](../api/error-reference.md)
- [코드 예제](../examples/code-examples.md)

<br>
