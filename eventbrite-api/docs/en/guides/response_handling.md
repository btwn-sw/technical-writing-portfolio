# Response Handling Guide

Eventbrite API responses follow consistent patterns that affect how you
read event data, handle missing fields, and process HTML content safely.
This guide explains why those patterns exist and how to work with them
correctly in your application.

For endpoint schemas and parameter definitions, see the
[API Reference](../api/api-reference.md).
For a full list of error codes, see the
[Error Reference](../api/error-reference.md).

<br>

## Table of Contents

- [Why Responses Are Structured This Way](#why-responses-are-structured-this-way)
- [The Event Object](#the-event-object)
- [Handling HTML-Formatted Content](#handling-html-formatted-content)
- [Handling Optional and Nullable Fields](#handling-optional-and-nullable-fields)
- [Error Responses](#error-responses)
- [Best Practices](#best-practices)

<br>

## Why Responses Are Structured This Way

Understanding the design decisions behind the response format helps you
handle edge cases correctly rather than working around them by trial
and error.

**Why do `name` and `description` return both `text` and `html`?**
Eventbrite events are created through a rich-text editor that produces
HTML. However, not every consumer of the API — notifications, logs,
search indexes, mobile apps — can render HTML safely. Returning both
variants lets each consumer pick the right format without having to
strip or parse HTML themselves.

**Why are some fields `null` instead of empty strings?**
Eventbrite distinguishes between a field that has not been set and a
field that has been deliberately cleared. A `null` description means
the organizer has not written one yet — it is not an empty string. This
lets your application display "No description yet" rather than blank
space, and avoids rendering an empty HTML tag.

**Why do time fields include both `utc` and `local`?**
Event times need to be communicated in two contexts: the organizer's
local timezone (for display on the event page) and a timezone-neutral
format (for scheduling, calendar exports, and cross-timezone comparison).
The `utc` field is always present and consistent; the `local` field
depends on the timezone set by the organizer.

<br>

## The Event Object

Most Event-related endpoints return an Event object. Its fields fall
into four categories:

| Category | Fields |
| --- | --- |
| Identification | `id`, `url` |
| Text content | `name`, `description` — each contains `text` and `html` variants |
| Scheduling | `start`, `end` — each contains `utc`, `local`, and `timezone` |
| Metadata | `status`, `currency`, `capacity` |

**Example response:**

```json
{
  "id": "12345",
  "name": {
    "text": "My Event",
    "html": "<p>My Event</p>"
  },
  "description": {
    "text": "Event description here",
    "html": "<p>Event description here</p>"
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

For the full field reference, see
[API Reference — Retrieve an Event](../api/api-reference.md#retrieve-an-event).

<br>

## Handling HTML-Formatted Content

Eventbrite returns HTML content in two patterns depending on the endpoint.

### Pattern 1 — Dual text/html fields

The most common pattern. Text content objects contain both a plain text
variant and an HTML variant for the same value.

```json
"name": {
  "text": "My Event",
  "html": "<p>My Event</p>"
}
```

Use `text` when the consumer expects a plain string — notifications,
logs, search indexes, email subjects. Use `html` when rendering in a
web interface.

```jsx
// Render in UI
container.innerHTML = event.description.html;

// Pass to notification system
sendNotification({ title: event.name.text });
```

### Pattern 2 — Full HTML response

Some endpoints return the entire response body as a raw HTML string.

**Endpoint:**

```
GET /events/{event_id}/description/
```

**Response:**

```json
{
  "description": "<div>Example summary!</div>\n<div><p>My <em>event's</em> description goes <strong>here</strong>.</p></div>"
}
```

Use an HTML parser to render this content. Never pass the raw string
directly to `innerHTML` without sanitizing it first, especially if the
source is not fully trusted.

```jsx
import DOMPurify from "dompurify";
container.innerHTML = DOMPurify.sanitize(event.description);
```

<br>

## Handling Optional and Nullable Fields

Not all fields are present in every response. A field may be missing
or `null` in the following cases:

- Draft events with incomplete data — `description` is `null` until
the organizer fills it in
- Fields the organizer has not set — `capacity` is `null` when no
limit has been configured
- Fields the token does not have permission to access

Always check for field existence before accessing nested properties.

**JavaScript:**

```jsx
const name        = event?.name?.text        ?? "Untitled Event";
const description = event?.description?.text ?? "";
const capacity    = event?.capacity          ?? null;
```

**Python:**

```python
name        = event.get("name", {}).get("text", "Untitled Event")
description = event.get("description", {}).get("text", "")
capacity    = event.get("capacity")
```

<br>

## Error Responses

All Event endpoints return errors in a consistent format:

```json
{
  "error": "ERROR_CODE",
  "error_description": "A human-readable explanation of the error.",
  "status_code": 400
}
```

Use the `error` field to identify the error type in your application
logic. Use `error_description` for logging and debugging — it is not
intended for display to end users.

For the full list of error codes and resolution steps, see the
[Error Reference](../api/error-reference.md).

<br>

## Best Practices

**Extract only the fields you need.**
Avoid storing the full response object. Access only the fields your
application uses. This reduces the impact of future API changes and
keeps your data model clean.

```jsx
const { id, status } = response;
const name     = response.name?.text;
const startUtc = response.start?.utc;
```

**Separate HTML from plain text.**
Never use `html` fields where plain text is expected, and never pass
`text` fields to an HTML renderer. Mixing these causes either broken
layouts or raw HTML appearing as visible text.

```jsx
// Display in UI — use html
container.innerHTML = event.name.html;

// Pass to notification system — use text
sendNotification({ title: event.name.text });
```

**Map error codes to user-facing messages.**
The API's `error_description` is written for developers, not users.
Map each error code to a message your users can understand and act on.

```jsx
const ERROR_MESSAGES = {
  NO_AUTH:        "Authentication failed. Check your API token.",
  NOT_AUTHORIZED: "You do not have permission to access this event.",
  NOT_FOUND:      "This event could not be found.",
  FIELD_INVALID:  "The request contained an invalid value.",
};

function handleApiError(error) {
  return ERROR_MESSAGES[error.error] ?? "An unexpected error occurred.";
}
```

<br>

## Next Steps

- [API Reference](../api/api-reference.md)
- [Error Reference](../api/error-reference.md)
- [Authentication Guide](../guides/authentication.md)
- [Code Examples](../examples/code-examples.md)

<br>
