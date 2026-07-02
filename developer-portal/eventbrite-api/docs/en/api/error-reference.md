# Error Reference

Look up error codes returned by the Eventbrite API, their causes,
and the steps to resolve them.

For error handling code examples, see the
[Response Handling Guide](../guides/response_handling.md).
For step-by-step debugging, see the
[Troubleshooting Guide](../guides/troubleshooting.md).

<br>

## Table of Contents

- [Error Response Format](#error-response-format)
- [Error Codes](#error-codes)
    - [400 Bad Request](#400-bad-request)
    - [401 Unauthorized](#401-unauthorized)
    - [403 Forbidden](#403-forbidden)
    - [404 Not Found](#404-not-found)
    - [429 Too Many Requests](#429-too-many-requests)

<br>

## Error Response Format

All Eventbrite API errors return a consistent JSON structure:

```json
{
  "error": "ERROR_CODE",
  "error_description": "A human-readable explanation of the error.",
  "status_code": 400
}
```

| Field | Type | Description |
| --- | --- | --- |
| `error` | String | Machine-readable error code. Use this field to identify the error type in your application logic. |
| `error_description` | String | Human-readable explanation of what went wrong. Useful for logging and debugging, but not intended for display to end users. |
| `status_code` | Integer | HTTP status code. Matches the response status code. |

<br>

## Error Codes

### 400 Bad Request

The request was rejected because it contains invalid or conflicting
parameters.

| Error code | Cause | How to fix |
| --- | --- | --- |
| `FIELD_INVALID` | A required field is missing, or a field contains an invalid value — for example, an end time set before the start time, or an unrecognized currency code. | Check the request body against the [API Reference](../api/api-reference.md). Verify all required fields are present and all values match the expected format. |
| `VENUE_AND_ONLINE` | Both a venue and `online_event: true` are set on the same request. An event can be either in-person or online, not both. | Remove the venue or set `online_event` to `false`. |
| `BAD_CONTINUATION_TOKEN` | The `continuation` token passed as a query parameter is malformed or has expired. | Restart pagination from the first page by making the request without a `continuation` parameter. See the [Pagination Guide](../guides/pagination.md). |



### 401 Unauthorized

The request was rejected because authentication failed.

| Error code | Cause | How to fix |
| --- | --- | --- |
| `NO_AUTH` | The `Authorization` header is missing, the token format is incorrect, or the token has been revoked. | Confirm the header is formatted as `Authorization: Bearer YOUR_PRIVATE_TOKEN`. Generate a new token from your [API Keys page](https://www.eventbrite.com/account-settings/apps) if needed. |



### 403 Forbidden

The request was authenticated but the token does not have permission to
perform the action.

| Error code | Cause | How to fix |
| --- | --- | --- |
| `NOT_AUTHORIZED` | The event belongs to a different organization or account, the token lacks the required scope, or the action is not permitted for the event's current status — for example, deleting an event that has existing orders. | Confirm the `event_id` or `organization_id` belongs to the account that issued the token. Check the event's `status` field — some actions are restricted based on status. |



### 404 Not Found

The requested resource does not exist.

| Error code | Cause | How to fix |
| --- | --- | --- |
| `NOT_FOUND` | The `event_id` or `organization_id` is incorrect, the event has been deleted, or the token does not have permission to view it. | Verify the ID by opening the event in your Eventbrite account. The `event_id` is the number at the end of the event URL: `https://www.eventbrite.com/e/my-event-123456789`. |



### 429 Too Many Requests

The request was rejected because a rate limit has been exceeded.

| Limit type | Limit |
| --- | --- |
| All requests | 2,000 calls/hour |
| All requests | 48,000 calls/day |
| Create Event | 200 calls/hour |
| Publish Event | 200 calls/hour |

This error does not include a JSON error code. Check the following
response headers to diagnose the issue:

| Header | Description |
| --- | --- |
| `X-Apiary-RateLimit-Limit` | Maximum number of requests allowed in the current window |
| `X-Apiary-RateLimit-Remaining` | Number of requests remaining before the limit resets |

When `X-Apiary-RateLimit-Remaining` reaches `0`, wait until the window
resets before sending additional requests. For bulk operations, add a
delay between requests to stay within the limit.

<br>

## Next Steps

- [API Reference](../api/api-reference.md)
- [Response Handling Guide](../guides/response_handling.md)
- [Troubleshooting Guide](../guides/troubleshooting.md)

<br>
