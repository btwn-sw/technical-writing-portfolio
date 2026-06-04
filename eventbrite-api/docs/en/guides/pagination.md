# Pagination Guide

Retrieve multiple pages of events from the Eventbrite API using
continuation tokens. This guide covers how paginated responses are
structured, how to move through pages, and how to handle the last page.

<br>

## Table of Contents

- [How Pagination Works](#how-pagination-works)
- [Paginated Response Structure](#paginated-response-structure)
- [Step-by-Step: Retrieve All Events](#step-by-step-retrieve-all-events)
- [Code Example](#code-example)
- [Handling the Last Page](#handling-the-last-page)

<br>

## How Pagination Works

When a listing endpoint returns more results than fit on a single page,
Eventbrite splits the response into pages. Each page contains a
`pagination` object alongside the list of results.

To move through pages, copy the `continuation` token from the current
response and pass it as a query parameter in the next request.
Repeat until `has_more_items` is `false`.

<br>

## Paginated Response Structure

Every paginated response contains two top-level sections: a `pagination`
object and a list of results.

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
    { "id": "11111", "name": { "text": "Event One" }, "status": "live" },
    { "id": "22222", "name": { "text": "Event Two" }, "status": "draft" }
  ]
}
```

**Pagination fields:**

| Field | Type | Description |
| --- | --- | --- |
| `object_count` | Integer | Total number of results across all pages |
| `page_number` | Integer | Current page number, starting at 1 |
| `page_size` | Integer | Maximum number of results returned per page |
| `page_count` | Integer | Total number of pages |
| `has_more_items` | Boolean | `true` when additional pages are available. Always check this before requesting the next page. |
| `continuation` | String | Token to pass as a query parameter in the next request. Only present when `has_more_items` is `true`. |

<br>

## Step-by-Step: Retrieve All Events

Follow these steps to retrieve all events under an organization.

**Step 1. Make the first request**

Call the List Events by Organization endpoint without a `continuation`
parameter:

```bash
curl --request GET \
  --header "Authorization: Bearer YOUR_PRIVATE_TOKEN" \
  "https://www.eventbriteapi.com/v3/organizations/{organization_id}/events/"
```

**Step 2. Check `has_more_items`**

In the response, look at `pagination.has_more_items`:

- If `false` — you have all results. Stop here.
- If `true` — more pages are available. Continue to Step 3.

**Step 3. Copy the continuation token**

Copy the `continuation` value from `pagination.continuation` in the
current response.

**Step 4. Request the next page**

Add the `continuation` token as a query parameter and call the same
endpoint again:

```bash
curl --request GET \
  --header "Authorization: Bearer YOUR_PRIVATE_TOKEN" \
  "https://www.eventbriteapi.com/v3/organizations/{organization_id}/events/?continuation=AEtFRyiWxkr0ZXyCJcnZ5U1-uSWXJ6vO0sxN06GbrDngaX5U5i8XYmEuZfmZZYB9Uq6bSizOLYoV"
```

**Step 5. Repeat until done**

Repeat Steps 2–4 until `has_more_items` is `false`. The response on the
final page does not include a `continuation` token.

<br>

## Code Example

This Node.js example retrieves all events under an organization and
returns them as a single array.

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

<br>

## Handling the Last Page

When `has_more_items` is `false`, the response does not include a
`continuation` field in the `pagination` object. Accessing
`pagination.continuation` on the last page returns `undefined` in
JavaScript or raises a `KeyError` in Python.

Always read `has_more_items` first and stop the loop before attempting
to read the `continuation` token:

```javascript
// Correct — check has_more_items before reading continuation
continuation = data.pagination.has_more_items
  ? data.pagination.continuation
  : null;

// Incorrect — continuation is undefined on the last page
continuation = data.pagination.continuation;
```

If a `BAD_CONTINUATION_TOKEN` error is returned, the token has expired
or is malformed. Restart pagination from the first page by making the
request without a `continuation` parameter.
For more on this error, see the
[Error Reference](../api/error-reference.md#400-bad-request).

<br>

## Next Steps

- [API Reference — List Events by Organization](../api/api-reference.md#list-events-by-organization)
- [Error Reference](../api/error-reference.md)
- [Code Examples](../examples/code-examples.md)

<br>
