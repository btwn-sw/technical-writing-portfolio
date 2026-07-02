# 코드 예제

아래 예제를 복사해서 Eventbrite API 요청에 바로 활용하세요.
이벤트 조회, 생성, 수정, 삭제 작업을 cURL, JavaScript, Node.js, Python으로 보여줍니다.
전체 파라미터와 응답 필드 정의는 [API 레퍼런스](../api/api-reference.md)를 참고하세요.

<br>

## 목차

- [인증](#인증)
- [cURL](#curl)
- [JavaScript](#javascript)
- [Node.js](#nodejs)
- [Python](#python)
- [응답 처리](#응답-처리)
- [페이지네이션](#페이지네이션)
- [요청 제한](#요청-제한)

<br>

### 인증

모든 예제는 환경 변수에서 프라이빗 토큰을 읽습니다.
예제를 실행하기 전에 아래 명령어로 토큰을 한 번만 설정하세요.

```bash
export EVENTBRITE_TOKEN=your_token_here
```

토큰 설정 방법은 [인증 가이드](../guides/authentication.md)를 참고하세요.

<br>

## cURL

### 조직별 이벤트 목록 조회

```bash
curl --request GET \
  --header "Authorization: Bearer $EVENTBRITE_TOKEN" \
  "https://www.eventbriteapi.com/v3/organizations/{organization_id}/events/"
```

### 이벤트 조회

```bash
curl --request GET \
  --header "Authorization: Bearer $EVENTBRITE_TOKEN" \
  "https://www.eventbriteapi.com/v3/events/{event_id}/"
```

### 이벤트 생성

```bash
curl --request POST \
  --header "Authorization: Bearer $EVENTBRITE_TOKEN" \
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

### 이벤트 수정

```bash
curl --request POST \
  --header "Authorization: Bearer $EVENTBRITE_TOKEN" \
  --header "Content-Type: application/json" \
  --data '{
    "event": {
      "name": { "html": "<p>수정된 이벤트 이름</p>" },
      "capacity": 200
    }
  }' \
  "https://www.eventbriteapi.com/v3/events/{event_id}/"
```

### 이벤트 게시

```bash
curl --request POST \
  --header "Authorization: Bearer $EVENTBRITE_TOKEN" \
  --header "Content-Type: application/json" \
  "https://www.eventbriteapi.com/v3/events/{event_id}/publish/"
```

### 이벤트 삭제

```bash
curl --request DELETE \
  --header "Authorization: Bearer $EVENTBRITE_TOKEN" \
  "https://www.eventbriteapi.com/v3/events/{event_id}/"
```

<br>

## JavaScript

아래 예제는 모든 최신 브라우저에서 지원하는 Fetch API를 사용합니다. 토큰은 환경 변수 또는 안전한 설정에서 사용하고, 소스 코드에 직접 포함하지 마세요.

### 조직별 이벤트 목록 조회

```jsx
async function listEvents(organizationId) {
  const response = await fetch(
    `https://www.eventbriteapi.com/v3/organizations/${organizationId}/events/`,
    {
      headers: {
        "Authorization": `Bearer ${process.env.EVENTBRITE_TOKEN}`
      }
    }
  );

  if (!response.ok) {
    throw new Error(`요청 실패: ${response.status}`);
  }

  return response.json();
}
```

### 이벤트 조회

```jsx
async function getEvent(eventId) {
  const response = await fetch(
    `https://www.eventbriteapi.com/v3/events/${eventId}/`,
    {
      headers: {
        "Authorization": `Bearer ${process.env.EVENTBRITE_TOKEN}`
      }
    }
  );

  if (!response.ok) {
    throw new Error(`요청 실패: ${response.status}`);
  }

  return response.json();
}
```

### 이벤트 생성

```jsx
async function createEvent(organizationId, eventData) {
  const response = await fetch(
    `https://www.eventbriteapi.com/v3/organizations/${organizationId}/events/`,
    {
      method: "POST",
      headers: {
        "Authorization": `Bearer ${process.env.EVENTBRITE_TOKEN}`,
        "Content-Type": "application/json"
      },
      body: JSON.stringify({ event: eventData })
    }
  );

  if (!response.ok) {
    throw new Error(`요청 실패: ${response.status}`);
  }

  return response.json();
}
```

### 이벤트 수정

```jsx
async function updateEvent(eventId, updates) {
  const response = await fetch(
    `https://www.eventbriteapi.com/v3/events/${eventId}/`,
    {
      method: "POST",
      headers: {
        "Authorization": `Bearer ${process.env.EVENTBRITE_TOKEN}`,
        "Content-Type": "application/json"
      },
      body: JSON.stringify({ event: updates })
    }
  );

  if (!response.ok) {
    throw new Error(`요청 실패: ${response.status}`);
  }

  return response.json();
}
```

### 이벤트 삭제

```jsx
async function deleteEvent(eventId) {
  const response = await fetch(
    `https://www.eventbriteapi.com/v3/events/${eventId}/`,
    {
      method: "DELETE",
      headers: {
        "Authorization": `Bearer ${process.env.EVENTBRITE_TOKEN}`
      }
    }
  );

  if (!response.ok) {
    throw new Error(`요청 실패: ${response.status}`);
  }

  return response.json();
}
```

<br>

## Node.js

아래 예제는 서버 사이드 요청에 `node-fetch`를 사용하고
환경 변수에서 토큰을 읽습니다.

```bash
npm install node-fetch
```

### 조직별 이벤트 목록 조회

```jsx
import fetch from "node-fetch";

async function listEvents(organizationId) {
  const response = await fetch(
    `https://www.eventbriteapi.com/v3/organizations/${organizationId}/events/`,
    {
      headers: {
        "Authorization": `Bearer ${process.env.EVENTBRITE_TOKEN}`
      }
    }
  );

  if (!response.ok) {
    throw new Error(`요청 실패: ${response.status}`);
  }

  return response.json();
}
```

### 이벤트 조회

```jsx
import fetch from "node-fetch";

async function getEvent(eventId) {
  const response = await fetch(
    `https://www.eventbriteapi.com/v3/events/${eventId}/`,
    {
      headers: {
        "Authorization": `Bearer ${process.env.EVENTBRITE_TOKEN}`
      }
    }
  );

  if (!response.ok) {
    throw new Error(`요청 실패: ${response.status}`);
  }

  const event = await response.json();

  return {
    title:       event.name?.text,
    description: event.description?.text,
    status:      event.status
  };
}
```

### 이벤트 생성

```jsx
import fetch from "node-fetch";

async function createEvent(organizationId, eventData) {
  const response = await fetch(
    `https://www.eventbriteapi.com/v3/organizations/${organizationId}/events/`,
    {
      method: "POST",
      headers: {
        "Authorization": `Bearer ${process.env.EVENTBRITE_TOKEN}`,
        "Content-Type": "application/json"
      },
      body: JSON.stringify({ event: eventData })
    }
  );

  if (!response.ok) {
    throw new Error(`요청 실패: ${response.status}`);
  }

  return response.json();
}
```

### 이벤트 수정

```jsx
import fetch from "node-fetch";

async function updateEvent(eventId, updates) {
  const response = await fetch(
    `https://www.eventbriteapi.com/v3/events/${eventId}/`,
    {
      method: "POST",
      headers: {
        "Authorization": `Bearer ${process.env.EVENTBRITE_TOKEN}`,
        "Content-Type": "application/json"
      },
      body: JSON.stringify({ event: updates })
    }
  );

  if (!response.ok) {
    throw new Error(`요청 실패: ${response.status}`);
  }

  return response.json();
}
```

### 이벤트 삭제

```jsx
import fetch from "node-fetch";

async function deleteEvent(eventId) {
  const response = await fetch(
    `https://www.eventbriteapi.com/v3/events/${eventId}/`,
    {
      method: "DELETE",
      headers: {
        "Authorization": `Bearer ${process.env.EVENTBRITE_TOKEN}`
      }
    }
  );

  if (!response.ok) {
    throw new Error(`요청 실패: ${response.status}`);
  }

  return response.json();
}
```

<br>

## Python

아래 예제는 `requests` 라이브러리를 사용하고
환경 변수에서 토큰을 읽습니다.

```bash
pip install requests
```

### 조직별 이벤트 목록 조회

```python
import os
import requests

def list_events(organization_id):
    response = requests.get(
        f"https://www.eventbriteapi.com/v3/organizations/{organization_id}/events/",
        headers={"Authorization": f"Bearer {os.environ['EVENTBRITE_TOKEN']}"}
    )
    response.raise_for_status()
    return response.json()
```

### 이벤트 조회

```python
import os
import requests

def get_event(event_id):
    response = requests.get(
        f"https://www.eventbriteapi.com/v3/events/{event_id}/",
        headers={"Authorization": f"Bearer {os.environ['EVENTBRITE_TOKEN']}"}
    )
    response.raise_for_status()
    event = response.json()
    return {
        "title":       event.get("name", {}).get("text"),
        "description": event.get("description", {}).get("text"),
        "status":      event.get("status")
    }
```

### 이벤트 생성

```python
import os
import requests

def create_event(organization_id, event_data):
    response = requests.post(
        f"https://www.eventbriteapi.com/v3/organizations/{organization_id}/events/",
        headers={
            "Authorization": f"Bearer {os.environ['EVENTBRITE_TOKEN']}",
            "Content-Type": "application/json"
        },
        json={"event": event_data}
    )
    response.raise_for_status()
    return response.json()
```

### 이벤트 수정

```python
import os
import requests

def update_event(event_id, updates):
    response = requests.post(
        f"https://www.eventbriteapi.com/v3/events/{event_id}/",
        headers={
            "Authorization": f"Bearer {os.environ['EVENTBRITE_TOKEN']}",
            "Content-Type": "application/json"
        },
        json={"event": updates}
    )
    response.raise_for_status()
    return response.json()
```

### 이벤트 삭제

```python
import os
import requests

def delete_event(event_id):
    response = requests.delete(
        f"https://www.eventbriteapi.com/v3/events/{event_id}/",
        headers={"Authorization": f"Bearer {os.environ['EVENTBRITE_TOKEN']}"}
    )
    response.raise_for_status()
    return response.json()
```

<br>

## 응답 처리

### 필요한 필드만 추출하기

애플리케이션에서 필요한 필드만 접근하세요.

```jsx
const event = await getEvent("12345");

const title       = event.name?.text        ?? "제목 없음";
const description = event.description?.text ?? "";
const startTime   = event.start?.utc        ?? null;
```

### HTML과 일반 텍스트 분리하기

웹 인터페이스에서 렌더링할 때는 `html` 필드를 사용하세요.
알림이나 로그 같은 일반 문자열이 필요한 곳에는 `text` 필드를 사용하세요.

```jsx
// UI에 렌더링
container.innerHTML = event.description.html;

// 알림 시스템에 전달
sendNotification({ title: event.name.text });
```

### API 오류 처리하기

오류 코드를 애플리케이션에서 표시할 수 있는 메시지로 변환하세요.

```jsx
async function safeGetEvent(eventId) {
  const response = await fetch(
    `https://www.eventbriteapi.com/v3/events/${eventId}/`,
    {
      headers: {
        "Authorization": `Bearer ${process.env.EVENTBRITE_TOKEN}`
      }
    }
  );

  if (!response.ok) {
    const error = await response.json();
    const messages = {
      NO_AUTH:        "유효하지 않거나 없는 토큰입니다.",
      NOT_AUTHORIZED: "이 이벤트에 접근할 권한이 없습니다.",
      NOT_FOUND:      "이벤트를 찾을 수 없습니다."
    };
    throw new Error(
      messages[error.error] ?? `예기치 않은 오류: ${response.status}`
    );
  }

  return response.json();
}
```

<br>

## 페이지네이션

컨티뉴에이션 토큰을 사용해서 여러 페이지에 걸친 이벤트를 모두 가져오세요.

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

페이지네이션 작동 방식에 대한 자세한 설명은
[페이지네이션 가이드](../guides/pagination.md)를 참고하세요.

<br>

## 요청 제한

대량 작업을 처리할 때는 요청 사이에 시간 간격을 두고 허용된 요청 수를 유지하세요.

아래 예제는 요청 사이에 500ms 간격을 둡니다. 500ms 간격은 시간당 2,000건 제한 내에서 안전하게 작동하는 기본값입니다. 처리량을 높여야 한다면 간격을 줄이고 `X-Apiary-RateLimit-Remaining` 헤더를 모니터링하세요.

```jsx
import fetch from "node-fetch";

async function fetchWithDelay(eventIds, delayMs = 500) {
  const results = [];

  for (const id of eventIds) {
    const response = await fetch(
      `https://www.eventbriteapi.com/v3/events/${id}/`,
      {
        headers: {
          "Authorization": `Bearer ${process.env.EVENTBRITE_TOKEN}`
        }
      }
    );

    if (!response.ok) {
      throw new Error(`이벤트 ${id} 요청 실패: ${response.status}`);
    }

    results.push(await response.json());
    await new Promise(resolve => setTimeout(resolve, delayMs));
  }

  return results;
}
```

요청 제한에 대한 자세한 내용은
[오류 레퍼런스 — 429](../api/error-reference.md#429-too-many-requests)를
참고하세요.

<br>

## 다음 단계

- [API 레퍼런스](../api/api-reference.md)
- [페이지네이션 가이드](../guides/pagination.md)
- [응답 처리 가이드](../guides/response-handling.md)
- [인증 가이드](../guides/authentication.md)

<br>
