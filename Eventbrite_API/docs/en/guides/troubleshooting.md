# Troubleshooting Guide

Find the cause of a failing Eventbrite API request and fix it.
Each section covers a specific error or symptom, its most common causes,
and the steps to resolve it.

For a complete list of error codes, see the
[Error Reference](../api/error-reference.md).

<br>

## Table of Contents

- [Common Code Mistakes](#common-code-mistakes)
- [401 Authentication Errors](#401-authentication-errors)
- [403 Permission Errors](#403-permission-errors)
- [404 Resource Not Found](#404-resource-not-found)
- [Missing or Unexpected Response Data](#missing-or-unexpected-response-data)
- [HTML Content Issues](#html-content-issues)
- [Rate Limiting](#rate-limiting)
- [General Debugging Tips](#general-debugging-tips)

<br>

## Common Code Mistakes

Start here if your request is failing and the cause is unclear.
These mistakes account for the majority of failures before even reaching
the API.

### Mistake 1 — Token in source code

**Symptom:** Token is exposed in a public repository or build log.

**Fix:** Move the token to an environment variable:

```bash
export EVENTBRITE_TOKEN=your_token_here
```

```jsx
const token = process.env.EVENTBRITE_TOKEN;
```

**Verify:** Search your codebase for the token string. It should not
appear in any source file.

<br>

### Mistake 2 — Wrong base URL

**Symptom:** All requests return connection errors or `404`.

**Fix:** Use `eventbriteapi.com`, not `eventbrite.com`:

```
✗  https://www.eventbrite.com/v3/events/
✓  https://www.eventbriteapi.com/v3/events/
```

**Verify:** Retry with the corrected URL.

<br>

### Mistake 3 — Missing Content-Type header

**Symptom:** POST requests return `400 Bad Request` with no clear error.

**Fix:** Include `Content-Type: application/json` in all POST requests:

```bash
curl --request POST \
  --header "Authorization: Bearer YOUR_PRIVATE_TOKEN" \
  --header "Content-Type: application/json" \
  --data '{ "event": { ... } }' \
  "https://www.eventbriteapi.com/v3/organizations/{organization_id}/events/"
```

**Verify:** Retry the request. A `200 OK` response confirms the header
was the issue.

<br>

## 401 Authentication Errors

### Symptom

- API returns `401 Unauthorized`.
- Requests work in the Eventbrite console but fail in code.

### Possible Causes

- `Authorization` header is missing or formatted incorrectly.
- Token was revoked or regenerated after being copied.
- Token is hard-coded with a typo or extra whitespace.

### How to Fix

1. Confirm your request includes the `Authorization` header in this
exact format — including the `Bearer` prefix and a single space:

```
Authorization: Bearer YOUR_PRIVATE_TOKEN
```

2. Copy a fresh token from your
[API Keys page](https://www.eventbrite.com/account-settings/apps)
and replace the existing one.
3. Store your token in an environment variable to avoid hard-coding
errors:

```bash
export EVENTBRITE_TOKEN=your_token_here
```

### Verify

Send the following request. A `200 OK` response with your account
details confirms your token is valid.

```bash
curl --request GET \
  --header "Authorization: Bearer YOUR_PRIVATE_TOKEN" \
  "https://www.eventbriteapi.com/v3/users/me/"
```

### Related Documentation

- [Authentication Guide](../guides/authentication.md)
- [Error Reference — 401](../api/error-reference.md#401-unauthorized)

<br>

## 403 Permission Errors

### Symptom

- API returns `403 NOT_AUTHORIZED`.
- Request is authenticated but access is denied.
- Event exists but cannot be retrieved or modified.

### Possible Causes

- The event belongs to a different Eventbrite account or organization.
- The token does not have permission to perform this operation.
- The event's current status does not allow this action — for example,
deleting an event that has existing orders.

### How to Fix

1. Confirm the `event_id` belongs to the account that issued your token.
Log in to Eventbrite and verify the event appears under your account.
2. If the request uses an `organization_id`, confirm it matches the
organization that owns the event:

```bash
curl --request GET \
  --header "Authorization: Bearer YOUR_PRIVATE_TOKEN" \
  "https://www.eventbriteapi.com/v3/users/me/organizations/"
```

3. Check the `status` field in the event response. Some operations are
restricted based on event status — for example, events with orders
cannot be deleted.

### Verify

Retry the original request. A `200 OK` response confirms the permission
issue is resolved.

### Related Documentation

- [API Reference](../api/api-reference.md)
- [Error Reference — 403](../api/error-reference.md#403-forbidden)

<br>

## 404 Resource Not Found

### Symptom

- API returns `404 NOT_FOUND`.
- Event ID appears valid but the request fails.

### Possible Causes

- `event_id` is incorrect, malformed, or copied from the wrong URL.
- Event was deleted or unpublished.
- Event belongs to a different account.

### How to Fix

1. Find the correct `event_id` from the event URL in your Eventbrite
account. The ID is the number at the end of the URL:

```
https://www.eventbrite.com/e/my-event-name-12345678901
                                           ^^^^^^^^^^^
                                    This is your event_id
```

2. Confirm you are using the correct base URL:

```
https://www.eventbriteapi.com/v3
```

3. Confirm the event is still accessible by opening it in your
Eventbrite account.

### Verify

Run this request with your corrected `event_id`. A `200 OK` response
confirms the event is accessible.

```bash
curl --request GET \
  --header "Authorization: Bearer YOUR_PRIVATE_TOKEN" \
  "https://www.eventbriteapi.com/v3/events/{event_id}/"
```

### Related Documentation

- [API Reference](../api/api-reference.md)
- [Error Reference — 404](../api/error-reference.md#404-not-found)

<br>

## Missing or Unexpected Response Data

### Symptom

- Fields such as `description.html` are empty or `null`.
- Expected fields are missing from the response.
- Draft events return incomplete data.

### Possible Causes

- Event is in `draft` status — some fields are only populated after
the event is published.
- Field is optional and was not filled in by the event organizer.
- Token permissions restrict which fields are returned.

### How to Fix

1. Check the `status` field in the response. If `"status": "draft"`,
publish the event or use a published event for testing.
2. Add null checks before accessing nested fields in your code:

```jsx
const description = event?.description?.text ?? "";
const capacity    = event?.capacity          ?? null;
```

### Verify

Re-run the request after publishing the event or switching to a
published test event. All expected fields should now be present.

### Related Documentation

- [Response Handling Guide](../guides/response_handling.md)

<br>

## HTML Content Issues

### Symptom

- HTML tags appear as raw text in the UI.
- HTML output breaks the page layout.
- Security review flags unsanitized HTML rendering.

### Possible Causes

- `html` field is passed to a context that expects plain text.
- HTML content is rendered without sanitization.

### How to Fix

1. Use `name.text` or `description.text` for plain string contexts —
notifications, logs, email subjects:

```jsx
sendNotification({ title: event.name.text });
```

2. Use `name.html` or `description.html` only when rendering to a
web interface:

```jsx
container.innerHTML = event.description.html;
```

3. If rendering HTML from an untrusted source, sanitize it first:

```jsx
import DOMPurify from "dompurify";
container.innerHTML = DOMPurify.sanitize(event.description.html);
```

### Verify

Inspect the rendered output in your UI. HTML tags should not appear as
raw text, and the page layout should remain intact.

### Related Documentation

- [Response Handling Guide](../guides/response_handling.md)
- [Code Examples](../examples/code-examples.md)

<br>

## Rate Limiting

### Symptom

- API returns `429 Too Many Requests`.
- Requests succeed individually but fail during high-volume operations.

### Possible Causes

- Hourly limit of 2,000 requests exceeded.
- Daily limit of 48,000 requests exceeded.
- Create or Publish Event endpoint limit of 200 requests/hour exceeded.

### How to Fix

1. Check the rate limit headers in the API response to see how many
requests remain:

| Header | Description |
| --- | --- |
| `X-Apiary-RateLimit-Limit` | Maximum requests allowed in this window |
| `X-Apiary-RateLimit-Remaining` | Requests remaining before the limit resets |
2. Wait until the window resets before sending additional requests.
3. For bulk operations, add a delay between requests to stay within
the limit. See [Code Examples](../examples/code-examples.md) for a
working implementation.

### Verify

Monitor `X-Apiary-RateLimit-Remaining` in subsequent responses.
The value should stay above zero.

### Related Documentation

- [Error Reference — 429](../api/error-reference.md#429-too-many-requests)
- [Code Examples](../examples/code-examples.md)

<br>

## General Debugging Tips

Follow these steps in order when a request fails and the cause is
unclear.

1. **Test in the Eventbrite console first.** If the request works in
the console but not in code, the issue is in your headers or token
handling.
2. **Import the request into Postman.** Use the console's
**Show Code Example → cURL** output. Postman shows you exactly which
headers are being sent.
3. **Log the full response during development** — including status
code, headers, and body. Error details are in the `error_description`
field of the response body.
4. **Compare against a working request.** Copy a working cURL example
from the [API Reference](../api/api-reference.md) and diff it against
your failing request line by line.

<br>

## Next Steps

- [Authentication Guide](../guides/authentication.md)
- [API Reference](../api/api-reference.md)
- [Error Reference](../api/error-reference.md)
- [Response Handling Guide](../guides/response_handling.md)
- [Code Examples](../examples/code-examples.md)

<br>
