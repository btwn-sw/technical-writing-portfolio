# Code Examples

Copy and adapt these examples to make common Eventbrite API requests.
Each section shows the same operations in a different language or tool.
For full parameter and response field definitions, see the
[API Reference](../api/api-reference.md).

<br>

## Table of Contents

- [Authentication](#authentication)
- [cURL](#curl)
- [JavaScript](#javascript)
- [Node.js](#nodejs)
- [Python](#python)
- [Response Handling](#response-handling)
- [Pagination](#pagination)
- [Rate Limiting](#rate-limiting)

<br>

## Authentication

All examples read the Private Token from an environment variable.
Set it once before running any example:

```bash
export EVENTBRITE_TOKEN=your_token_here
```

For token setup instructions, see the
[Authentication Guide](../guides/authentication.md).

<br>

## cURL

### List Events by Organization

```bash
curl --request GET \
  --header "Authorization: Bearer $EVENTBRITE_TOKEN" \
  "https://www.eventbriteapi.com/v3/organizations/{organization_id}/events/"
```

### Retrieve an Event

```bash
curl --request GET \
  --header "Authorization: Bearer $EVENTBRITE_TOKEN" \
  "https://www.eventbriteapi.com/v3/events/{event_id}/"
```

### Create an Event

```bash
curl --request POST \
  --header "Authorization: Bearer $EVENTBRITE_TOKEN" \
  --header "Content-Type: application/json" \
  --data '{
    "event": {
      "name": { "html": "<p>My Event</p>" },
      "start": { "timezone": "UTC", "utc": "2026-06-01T09:00:00Z" },
      "end":   { "timezone": "UTC", "utc": "2026-06-01T17:00:00Z" },
      "currency": "USD"
    }
  }' \
  "https://www.eventbriteapi.com/v3/organizations/{organization_id}/events/"
```

### Update an Event

```bash
curl --request POST \
  --header "Authorization: Bearer $EVENTBRITE_TOKEN" \
  --header "Content-Type: application/json" \
  --data '{
    "event": {
      "name": { "html": "<p>Updated Event Name</p>" },
      "capacity": 200
    }
  }' \
  "https://www.eventbriteapi.com/v3/events/{event_id}/"
```

### Publish an Event

```bash
curl --request POST \
  --header "Authorization: Bearer $EVENTBRITE_TOKEN" \
  --header "Content-Type: application/json" \
  "https://www.eventbriteapi.com/v3/events/{event_id}/publish/"
```

### Delete an Event

```bash
curl --request DELETE \
  --header "Authorization: Bearer $EVENTBRITE_TOKEN" \
  "https://www.eventbriteapi.com/v3/events/{event_id}/"
```

<br>

## JavaScript

These examples use the Fetch API, supported in all modern browsers.
Replace `YOUR_PRIVATE_TOKEN` with your token.

### List Events by Organization

```javascript
async function listEvents(organizationId) {
  const response = await fetch(
    `https://www.eventbriteapi.com/v3/organizations/${organizationId}/events/`,
    {
      headers: {
        "Authorization": "Bearer YOUR_PRIVATE_TOKEN"
      }
    }
  );

  if (!response.ok) {
    throw new Error(`Request failed: ${response.status}`);
  }

  return response.json();
}
```

### Retrieve an Event

```javascript
async function getEvent(eventId) {
  const response = await fetch(
    `https://www.eventbriteapi.com/v3/events/${eventId}/`,
    {
      headers: {
        "Authorization": "Bearer YOUR_PRIVATE_TOKEN"
      }
    }
  );

  if (!response.ok) {
    throw new Error(`Request failed: ${response.status}`);
  }

  return response.json();
}
```

### Create an Event

```javascript
async function createEvent(organizationId, eventData) {
  const response = await fetch(
    `https://www.eventbriteapi.com/v3/organizations/${organizationId}/events/`,
    {
      method: "POST",
      headers: {
        "Authorization": "Bearer YOUR_PRIVATE_TOKEN",
        "Content-Type": "application/json"
      },
      body: JSON.stringify({ event: eventData })
    }
  );

  if (!response.ok) {
    throw new Error(`Request failed: ${response.status}`);
  }

  return response.json();
}
```

### Update an Event

```javascript
async function updateEvent(eventId, updates) {
  const response = await fetch(
    `https://www.eventbriteapi.com/v3/events/${eventId}/`,
    {
      method: "POST",
      headers: {
        "Authorization": "Bearer YOUR_PRIVATE_TOKEN",
        "Content-Type": "application/json"
      },
      body: JSON.stringify({ event: updates })
    }
  );

  if (!response.ok) {
    throw new Error(`Request failed: ${response.status}`);
  }

  return response.json();
}
```

### Delete an Event

```javascript
async function deleteEvent(eventId) {
  const response = await fetch(
    `https://www.eventbriteapi.com/v3/events/${eventId}/`,
    {
      method: "DELETE",
      headers: {
        "Authorization": "Bearer YOUR_PRIVATE_TOKEN"
      }
    }
  );

  if (!response.ok) {
    throw new Error(`Request failed: ${response.status}`);
  }

  return response.json();
}
```

<br>

## Node.js

These examples use `node-fetch` for server-side requests and read the
token from an environment variable.

```bash
npm install node-fetch
```

### List Events by Organization

```javascript
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
    throw new Error(`Request failed: ${response.status}`);
  }

  return response.json();
}
```

### Retrieve an Event

```javascript
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
    throw new Error(`Request failed: ${response.status}`);
  }

  const event = await response.json();

  return {
    title:       event.name?.text,
    description: event.description?.text,
    status:      event.status
  };
}
```

### Create an Event

```javascript
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
    throw new Error(`Request failed: ${response.status}`);
  }

  return response.json();
}
```

### Update an Event

```javascript
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
    throw new Error(`Request failed: ${response.status}`);
  }

  return response.json();
}
```

### Delete an Event

```javascript
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
    throw new Error(`Request failed: ${response.status}`);
  }

  return response.json();
}
```

<br>

## Python

These examples use the `requests` library and read the token from an
environment variable.

```bash
pip install requests
```

### List Events by Organization

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

### Retrieve an Event

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

### Create an Event

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

### Update an Event

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

### Delete an Event

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

## Response Handling

### Extract specific fields

Access only the fields your application needs.

```javascript
const event = await getEvent("12345");

const title       = event.name?.text        ?? "Untitled";
const description = event.description?.text ?? "";
const startTime   = event.start?.utc        ?? null;
```

### Separate HTML from plain text

Use `html` fields for rendering in a web interface.
Use `text` fields for plain string contexts such as notifications or logs.

```javascript
// Render in UI
container.innerHTML = event.description.html;

// Pass to notification system
sendNotification({ title: event.name.text });
```

### Handle API errors

Map error codes to messages your application can display.

```javascript
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
      NO_AUTH:        "Invalid or missing token.",
      NOT_AUTHORIZED: "You do not have permission to access this event.",
      NOT_FOUND:      "Event not found."
    };
    throw new Error(messages[error.error] ?? `Unexpected error: ${response.status}`);
  }

  return response.json();
}
```

<br>

## Pagination

Retrieve all events across multiple pages using continuation tokens.

```javascript
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
      throw new Error(`Request failed: ${response.status}`);
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

For a full explanation of how pagination works, see the
[Pagination Guide](../guides/pagination.md).

<br>

## Rate Limiting

Add a delay between requests during bulk operations to stay within
the rate limit.

```javascript
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
      throw new Error(`Request failed for event ${id}: ${response.status}`);
    }

    results.push(await response.json());
    await new Promise(resolve => setTimeout(resolve, delayMs));
  }

  return results;
}
```

For rate limit details, see the
[Error Reference — 429](../api/error-reference.md#429-too-many-requests).

<br>

## Next Steps

- [API Reference](../api/api-reference.md)
- [Pagination Guide](../guides/pagination.md)
- [Response Handling Guide](../guides/response_handling.md)
- [Authentication Guide](../guides/authentication.md)

<br>
