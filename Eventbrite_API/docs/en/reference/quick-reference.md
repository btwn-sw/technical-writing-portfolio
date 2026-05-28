# Quick Reference Guide

Fast lookup for Eventbrite API essentials. Use this guide when you already
know the API and need a quick reminder of endpoints, headers, status codes,
or rate limits.

For full documentation, start with the
[API Reference](../api/api-reference.md).

<br>

## Table of Contents

- [Base URL and Version](#base-url-and-version)
- [Authentication](#authentication)
- [Event Endpoints](#event-endpoints)
- [Request Headers](#request-headers)
- [Response Fields](#response-fields)
- [HTTP Status Codes](#http-status-codes)
- [Rate Limits](#rate-limits)
- [Quick Request Example](#quick-request-example)

<br>

## Base URL and Version

```
https://www.eventbriteapi.com/v3
```

- API version: `v3`
- All endpoints are prefixed with `/v3/`
- Request and response format: `application/json`

<br>

## Authentication

```
Authorization: Bearer YOUR_PRIVATE_TOKEN
```

- Store your token in an environment variable — never in source code.
- Regenerate your token at [Account Settings → API Keys](https://www.eventbrite.com/account-settings/apps).

→ [Authentication Guide](../guides/authentication.md)

<br>

## Event Endpoints

| Action | Method | Endpoint |
| --- | --- | --- |
| Create an Event | `POST` | `/organizations/{organization_id}/events/` |
| Retrieve an Event | `GET` | `/events/{event_id}/` |
| Update an Event | `POST` | `/events/{event_id}/` |
| Delete an Event | `DELETE` | `/events/{event_id}/` |

→ [API Reference](../api/api-reference.md)

<br>

## Request Headers

| Header | Value |
| --- | --- |
| `Authorization` | `Bearer YOUR_PRIVATE_TOKEN` |
| `Content-Type` | `application/json` |

<br>

## Response Fields

| Field | Type | Description |
| --- | --- | --- |
| `id` | String | Unique event identifier |
| `name.text` | String | Event title as plain text |
| `name.html` | String | Event title in HTML format |
| `description.text` | String | Event description as plain text |
| `description.html` | String | Event description in HTML format |
| `status` | String | Event status — `draft`, `live`, `started`, `ended`, `completed`, `canceled` |
| `start.utc` | String | Start time in UTC |
| `end.utc` | String | End time in UTC |

→ [Response Handling Guide](../guides/response_handling.md)

<br>

## HTTP Status Codes

| Code | Error code | Meaning | Action |
| --- | --- | --- | --- |
| `200` | — | Request successful | — |
| `400` | `FIELD_INVALID` | Invalid request or conflicting parameters | Check request body against the API Reference |
| `401` | `NO_AUTH` | Missing or invalid token | Check `Authorization` header format and token value |
| `403` | `NOT_AUTHORIZED` | Insufficient permissions | Verify the event belongs to your account |
| `404` | `NOT_FOUND` | Resource not found | Verify the `event_id` is correct |
| `429` | — | Rate limit exceeded | Check `X-Apiary-RateLimit-Remaining` header |

→ [Troubleshooting Guide](../guides/troubleshooting.md)

<br>

## Rate Limits

| Limit type | Limit |
| --- | --- |
| All requests | 2,000 / hour |
| All requests | 48,000 / day |
| Create or Publish Event | 200 / hour |

**Rate limit headers:**

| Header | Description |
| --- | --- |
| `X-Apiary-RateLimit-Limit` | Maximum requests allowed in the current window |
| `X-Apiary-RateLimit-Remaining` | Requests remaining before the limit resets |

→ [API Reference — Rate Limiting](../api/api-reference.md/#rate-limiting)

<br>

## Quick Request Example

**Retrieve an event:**

```bash
curl --request GET \\
  --header "Authorization: Bearer YOUR_PRIVATE_TOKEN" \\
  "<https://www.eventbriteapi.com/v3/events/{event_id}/>"
```

→ [Code Examples](../examples/code-examples.md)

<br>
