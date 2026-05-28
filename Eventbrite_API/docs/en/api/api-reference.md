# Eventbrite API Reference

Look up endpoints, parameters, and response fields for the Eventbrite API.
This reference documents a focused subset of Event-related endpoints.
For authentication setup, see the [Authentication Guide](https://www.notion.so/guides/authentication.md).

<br>

## Table of Contents

- [Base URL and Versioning](#base-url-and-versioning)
- [Authentication](#authentication)
- [Rate Limiting](#rate-limiting)
- [Error Handling](#error-handling)
- [Event Endpoints](#event-endpoints)
    - [Create an Event](#create-an-event)
    - [Retrieve an Event](#retrieve-an-event)
    - [Update an Event](#update-an-event)
    - [Delete an Event](#delete-an-event)

<br>

## Base URL and Versioning

All endpoints use the following base URL:

```
https://www.eventbriteapi.com/v3
```

- Current API version: `v3`
- All endpoints are prefixed with `/v3/`
- Request format: `application/json`
- Response format: `application/json`

<br>

## Authentication

All requests require a Private Token in the `Authorization` header:

```
Authorization: Bearer YOUR_PRIVATE_TOKEN
```

Replace `YOUR_PRIVATE_TOKEN` with the token from your
[Eventbrite API Keys page](https://www.eventbrite.com/account-settings/apps).
For full setup instructions, see the
[Authentication Guide](https://www.notion.so/guides/authentication.md).

<br>

## Rate Limiting

Eventbrite enforces rate limits across all API integrations to maintain
platform stability.

### Default Limits

| Limit type | Limit |
| --- | --- |
| All requests | 2,000 calls/hour |
| All requests | 48,000 calls/day |
| Create Event | 200 calls/hour |
| Publish Event | 200 calls/hour |

### Rate Limit Headers

When a rate limit applies, Eventbrite includes the following headers
in the response:

| Header | Description |
| --- | --- |
| `X-Apiary-RateLimit-Limit` | Maximum number of requests allowed |
| `X-Apiary-RateLimit-Remaining` | Number of requests remaining in the current window |

<br>

## Error Handling

All Event endpoints may return the following error responses.

| Status code | Error code | Description |
| --- | --- | --- |
| `400` | `BAD_CONTINUATION_TOKEN` | The request is invalid or contains conflicting parameters |
| `401` | `NO_AUTH` | No authentication token provided, or the token is invalid |
| `403` | `NOT_AUTHORIZED` | The token does not have permission to perform this action |
| `404` | `NOT_FOUND` | The requested event does not exist |

**Error response format:**

```json
{
  "error": "VENUE_AND_ONLINE",
  "error_description": "You cannot both specify a venue and set online_event",
  "status_code": 400
}
```

For error handling strategies, see the
[Response Handling Guide](https://www.notion.so/guides/response_handling.md).

<br>

## Event Endpoints

### Create an Event

Creates a new event under an organization.

**Endpoint**

```
POST /organizations/{organization_id}/events/
```

**Path Parameters**

| Name | Type | Required | Description | Example |
| --- | --- | --- | --- | --- |
| `organization_id` | String | Yes | The ID of the organization that will own the event | `12345` |

**Request Body Parameters**

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `event.name.html` | String | Yes | Event name in HTML format |
| `event.description.html` | String | No | Event description in HTML format |
| `event.start.utc` | String | Yes | Start time in UTC — format: `YYYY-MM-DDTHH:MM:SSZ` |
| `event.start.timezone` | String | Yes | Start time timezone — example: `America/Los_Angeles` |
| `event.end.utc` | String | Yes | End time in UTC — format: `YYYY-MM-DDTHH:MM:SSZ` |
| `event.end.timezone` | String | Yes | End time timezone |
| `event.currency` | String | Yes | ISO 4217 currency code — example: `USD` |
| `event.online_event` | Boolean | No | Set to `true` for online-only events. Cannot be combined with a venue. |
| `event.capacity` | Integer | No | Maximum number of attendees |
| `event.listed` | Boolean | No | Set to `true` to make the event publicly visible |
| `event.invite_only` | Boolean | No | Set to `true` to restrict access to invited attendees only |

**Request Example**

```bash
curl --request POST \\
  --header "Authorization: Bearer YOUR_PRIVATE_TOKEN" \\
  --header "Content-Type: application/json" \\
  --data '{
    "event": {
      "name": { "html": "<p>My Event</p>" },
      "start": { "timezone": "UTC", "utc": "2026-06-01T09:00:00Z" },
      "end":   { "timezone": "UTC", "utc": "2026-06-01T17:00:00Z" },
      "currency": "USD"
    }
  }' \\
  "<https://www.eventbriteapi.com/v3/organizations/{organization_id}/events/>"
```

**Response: `200 OK`**

```json
{
  "id": "12345",
  "name": {
    "text": "My Event",
    "html": "<p>My Event</p>"
  },
  "start": {
    "timezone": "UTC",
    "utc": "2026-06-01T09:00:00Z",
    "local": "2026-06-01T09:00:00"
  },
  "end": {
    "timezone": "UTC",
    "utc": "2026-06-01T17:00:00Z",
    "local": "2026-06-01T17:00:00"
  },
  "status": "draft",
  "url": "<https://www.eventbrite.com/e/12345>",
  "created": "2026-05-01T10:00:00Z"
}
```

**Response Fields**

| Field | Type | Description |
| --- | --- | --- |
| `id` | String | Unique identifier for the created event |
| `name.text` | String | Event name as plain text |
| `name.html` | String | Event name in HTML format |
| `start.utc` | String | Start time in UTC |
| `start.local` | String | Start time in the event's local timezone |
| `end.utc` | String | End time in UTC |
| `end.local` | String | End time in the event's local timezone |
| `status` | String | Event status — `draft`, `live`, or `canceled` |
| `url` | String | Public URL of the event on Eventbrite |
| `created` | String | Timestamp when the event was created — UTC |



### Retrieve an Event

Returns details for a single event.

**Endpoint**

```
GET /events/{event_id}/
```

**Path Parameters**

| Name | Type | Required | Description | Example |
| --- | --- | --- | --- | --- |
| `event_id` | String | Yes | The unique ID of the event to retrieve | `12345` |

**Request Example**

```bash
curl --request GET \\
  --header "Authorization: Bearer YOUR_PRIVATE_TOKEN" \\
  "<https://www.eventbriteapi.com/v3/events/{event_id}/>"
```

**Response: `200 OK`**

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
  "url": "<https://www.eventbrite.com/e/12345>",
  "capacity": 100,
  "currency": "USD"
}
```

**Response Fields**

| Field | Type | Description |
| --- | --- | --- |
| `id` | String | Unique identifier of the event |
| `name.text` | String | Event name as plain text |
| `name.html` | String | Event name in HTML format |
| `description.text` | String | Event description as plain text |
| `description.html` | String | Event description in HTML format |
| `start.utc` | String | Start time in UTC |
| `start.local` | String | Start time in the event's local timezone |
| `end.utc` | String | End time in UTC |
| `end.local` | String | End time in the event's local timezone |
| `status` | String | Event status — `draft`, `live`, or `canceled` |
| `url` | String | Public URL of the event on Eventbrite |
| `capacity` | Integer | Maximum number of attendees |
| `currency` | String | ISO 4217 currency code for the event |



### Update an Event

Updates fields on an existing event. Only include the fields you want to change.

**Endpoint**

```
POST /events/{event_id}/
```

**Path Parameters**

| Name | Type | Required | Description | Example |
| --- | --- | --- | --- | --- |
| `event_id` | String | Yes | The unique ID of the event to update | `12345` |

**Request Body Parameters**

Include only the fields you want to update.
The request body structure is identical to [Create an Event](https://www.notion.so/api-reference_en-md-36ea5679c0d580c4b135ccf8d82f1252?pvs=21).

**Request Example**

```bash
curl --request POST \\
  --header "Authorization: Bearer YOUR_PRIVATE_TOKEN" \\
  --header "Content-Type: application/json" \\
  --data '{
    "event": {
      "name": { "html": "<p>Updated Event Name</p>" },
      "capacity": 200
    }
  }' \\
  "<https://www.eventbriteapi.com/v3/events/{event_id}/>"
```

**Response: `200 OK`**

Returns the updated event object. Fields are identical to
[Retrieve an Event](https://www.notion.so/api-reference_en-md-36ea5679c0d580c4b135ccf8d82f1252?pvs=21) response fields.



### Delete an Event

Permanently deletes an event. This action cannot be undone.

**Endpoint**

```
DELETE /events/{event_id}/
```

**Path Parameters**

| Name | Type | Required | Description | Example |
| --- | --- | --- | --- | --- |
| `event_id` | String | Yes | The unique ID of the event to delete | `12345` |

**Request Example**

```bash
curl --request DELETE \\
  --header "Authorization: Bearer YOUR_PRIVATE_TOKEN" \\
  --header "Content-Type: application/json" \\
  "<https://www.eventbriteapi.com/v3/events/{event_id}/>"
```

**Response: `200 OK`**

```json
{
  "deleted": true
}
```

| Field | Type | Description |
| --- | --- | --- |
| `deleted` | Boolean | Returns `true` when the event is successfully deleted |

<br>

## Next Steps

- [Authentication Guide]()
- [Response Handling Guide]()
- [Code Examples]()
- [SDKs]()

<br>
