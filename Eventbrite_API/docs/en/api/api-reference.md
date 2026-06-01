# Eventbrite API Reference

Look up endpoints, parameters, and response fields for the Eventbrite API.
This reference documents Event-related endpoints.
For authentication setup, see the [Authentication Guide](../guides/authentication.md).

<br> 

## Table of Contents

- [Base URL and Versioning](#base-url-and-versioning)
- [Authentication](#authentication)
- [Rate Limiting](#rate-limiting)
- [Error Handling](#error-handling)
- [Event Endpoints](#event-endpoints)
    - [List Events by Organization](#list-events-by-organization)
    - [Create an Event](#create-an-event)
    - [Retrieve an Event](#retrieve-an-event)
    - [Update an Event](#update-an-event)
    - [Publish an Event](#publish-an-event)
    - [Delete an Event]( #delete-an-event)

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
[Authentication Guide](../guides/authentication.md).

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
| `X-Apiary-RateLimit-Limit` | Maximum number of requests allowed in the current window |
| `X-Apiary-RateLimit-Remaining` | Number of requests remaining before the limit resets |

<br>

## Error Handling

All Event endpoints may return the following error responses.
For the full list of error codes and resolution steps, see the
[Error Reference](../api/error-reference.md).

| Status code | Error code | Description |
| --- | --- | --- |
| `400` | `FIELD_INVALID` | A required field is missing or a parameter contains an invalid value |
| `401` | `NO_AUTH` | No authentication token provided, or the token is invalid |
| `403` | `NOT_AUTHORIZED` | The token does not have permission to perform this action |
| `404` | `NOT_FOUND` | The requested event does not exist |

**Error response format:**

```json
{
  "error": "NOT_FOUND",
  "error_description": "The requested event does not exist or you do not have permission to view it.",
  "status_code": 404
}
```

For error handling strategies, see the
[Response Handling Guide](../guides/response_handling.md).

<br>

## Event Endpoints

### List Events by Organization

Returns a paginated list of events under an organization.

**Endpoint**

```
GET /organizations/{organization_id}/events/
```

**Path Parameters**

| Name | Type | Required | Description | Example |
| --- | --- | --- | --- | --- |
| `organization_id` | String | Yes | The ID of the organization to list events for | `12345` |

**Query Parameters**

| Parameter | Type | Required | Description |
| --- | --- | --- | --- |
| `status` | String | No | Filter by event status. Accepted values: `draft`, `live`, `started`, `ended`, `completed`, `canceled`. Returns all statuses when omitted. |
| `continuation` | String | No | Pagination token returned by a previous response. Used to retrieve the next page of results. |

**Request Example**

```bash
curl --request GET \
  --header "Authorization: Bearer YOUR_PRIVATE_TOKEN" \
  "https://www.eventbriteapi.com/v3/organizations/{organization_id}/events/"
```

**Response: `200 OK`**

```json
{
  "pagination": {
    "object_count": 4,
    "page_number": 1,
    "page_size": 50,
    "page_count": 1,
    "has_more_items": false
  },
  "events": [
    {
      "id": "12345",
      "name": {
        "text": "My Event",
        "html": "<p>My Event</p>"
      },
      "status": "live",
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
      "url": "https://www.eventbrite.com/e/12345"
    }
  ]
}
```

**Response Fields**

| Field | Type | Description |
| --- | --- | --- |
| `pagination.object_count` | Integer | Total number of events across all pages |
| `pagination.page_number` | Integer | Current page number, starting at 1 |
| `pagination.page_size` | Integer | Maximum number of events returned per page |
| `pagination.page_count` | Integer | Total number of pages |
| `pagination.has_more_items` | Boolean | `true` when additional pages are available |
| `pagination.continuation` | String | Token to pass as `continuation` in the next request. Present only when `has_more_items` is `true`. |
| `events` | Array | List of event objects. Each object contains the same fields as [Retrieve an Event](#retrieve-an-event). |

For step-by-step pagination instructions, see the
[Pagination Guide](../guides/pagination.md).



### Create an Event

Creates a new event under an organization. The event is created in `draft`
status and is not publicly visible until published.

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
| `event.start.timezone` | String | Yes | IANA timezone for the start time — example: `America/Los_Angeles` |
| `event.end.utc` | String | Yes | End time in UTC — format: `YYYY-MM-DDTHH:MM:SSZ`. Must be after `start.utc`. |
| `event.end.timezone` | String | Yes | IANA timezone for the end time |
| `event.currency` | String | Yes | ISO 4217 currency code — example: `USD` |
| `event.online_event` | Boolean | No | Set to `true` for online-only events. Default: `false`. Cannot be combined with a venue. |
| `event.capacity` | Integer | No | Maximum number of attendees. When omitted, capacity is calculated from the sum of all ticket class quantities. |
| `event.listed` | Boolean | No | Set to `true` to make the event publicly searchable on Eventbrite. Default: `false`. |
| `event.invite_only` | Boolean | No | Set to `true` to restrict access to invited attendees only. Default: `false`. |

**Request Example**

```bash
curl --request POST \
  --header "Authorization: Bearer YOUR_PRIVATE_TOKEN" \
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
  "url": "https://www.eventbrite.com/e/12345",
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
| `status` | String | Event status. Always `draft` on creation. Use [Publish an Event](#publish-an-event) to make the event live. |
| `url` | String | Public URL of the event on Eventbrite |
| `created` | String | Timestamp when the event was created, in UTC |



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
curl --request GET \
  --header "Authorization: Bearer YOUR_PRIVATE_TOKEN" \
  "https://www.eventbriteapi.com/v3/events/{event_id}/"
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
  "url": "https://www.eventbrite.com/e/12345",
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
| `description.text` | String | Event description as plain text. May be `null` for draft events with no description set. |
| `description.html` | String | Event description in HTML format. May be `null` for draft events with no description set. |
| `start.utc` | String | Start time in UTC |
| `start.local` | String | Start time in the event's local timezone |
| `end.utc` | String | End time in UTC |
| `end.local` | String | End time in the event's local timezone |
| `status` | String | Event status — `draft`, `live`, `started`, `ended`, `completed`, or `canceled` |
| `url` | String | Public URL of the event on Eventbrite |
| `capacity` | Integer | Maximum number of attendees. `null` when not set. |
| `currency` | String | ISO 4217 currency code for the event |



### Update an Event

Updates fields on an existing event. Include only the fields you want to change.
Fields not included in the request body remain unchanged.

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

| Field | Type | Description |
| --- | --- | --- |
| `event.name.html` | String | Updated event name in HTML format |
| `event.description.html` | String | Updated event description in HTML format |
| `event.start.utc` | String | Updated start time in UTC — format: `YYYY-MM-DDTHH:MM:SSZ` |
| `event.start.timezone` | String | Updated IANA timezone for the start time |
| `event.end.utc` | String | Updated end time in UTC — format: `YYYY-MM-DDTHH:MM:SSZ`. Must be after `start.utc`. |
| `event.end.timezone` | String | Updated IANA timezone for the end time |
| `event.capacity` | Integer | Updated maximum number of attendees |
| `event.listed` | Boolean | Set to `true` to make the event publicly searchable, `false` to make it private |
| `event.invite_only` | Boolean | Set to `true` to restrict access to invited attendees only |
| `event.online_event` | Boolean | Set to `true` for online-only events. Cannot be combined with a venue. |

**Request Example**

```bash
curl --request POST \
  --header "Authorization: Bearer YOUR_PRIVATE_TOKEN" \
  --header "Content-Type: application/json" \
  --data '{
    "event": {
      "name": { "html": "<p>Updated Event Name</p>" },
      "capacity": 200
    }
  }' \
  "https://www.eventbriteapi.com/v3/events/{event_id}/"
```

**Response: `200 OK`**

Returns the full updated event object. Response fields are identical to
[Retrieve an Event](#retrieve-an-event).



### Publish an Event

Publishes a draft event, making it publicly visible on Eventbrite.
Before publishing, the event must have a name, a description, at least one
ticket class, and valid payment options configured.

**Endpoint**

```
POST /events/{event_id}/publish/
```

**Path Parameters**

| Name | Type | Required | Description | Example |
| --- | --- | --- | --- | --- |
| `event_id` | String | Yes | The unique ID of the event to publish | `12345` |

**Request Body**

No request body required.

**Request Example**

```bash
curl --request POST \
  --header "Authorization: Bearer YOUR_PRIVATE_TOKEN" \
  --header "Content-Type: application/json" \
  "https://www.eventbriteapi.com/v3/events/{event_id}/publish/"
```

**Response: `200 OK`**

```json
{
  "published": true
}
```

| Field | Type | Description |
| --- | --- | --- |
| `published` | Boolean | Returns `true` when the event is successfully published. When publish fails due to missing requirements, the API returns a `400` error with details of which fields are incomplete. |



### Delete an Event

Permanently deletes an event. This action cannot be undone.

To delete an event, it must have no pending or completed orders.
Deleting an event that has orders returns a `403` error.

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
curl --request DELETE \
  --header "Authorization: Bearer YOUR_PRIVATE_TOKEN" \
  "https://www.eventbriteapi.com/v3/events/{event_id}/"
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

- [Authentication Guide](../guides/authentication.md)
- [Response Handling Guide](../guides/response_handling.md)
- [Error Reference](../api/error-reference.md)
- [Pagination Guide](../guides/pagination.md)
- [Code Examples](../examples/code-examples.md)

<br>
