# Quick Start Guide

Make your first Eventbrite API request in under 3 minutes. This guide
covers the minimum steps to authenticate, call an endpoint, and verify
a live response. No prior experience with the Eventbrite API is required.

**Prerequisites:**

- An active Eventbrite account
- cURL or a web browser
- A basic understanding of HTTP requests

<br>

## Table of Contents

- [Step 1: Get Your Private Token](#step-1-get-your-private-token)
- [Step 2: Make Your First Request](#step-2-make-your-first-request)
- [Step 3: Verify the Response](#step-3-verify-the-response)

<br>

## Step 1: Get Your Private Token

All Eventbrite API requests require a Private Token in the
`Authorization` header.

1. Log in to [Eventbrite](https://www.eventbrite.com/signin/).
2. Go to **Account Settings → Developer Links → API Keys**.
3. Click **Create API Key**, fill in the required fields, then click
**Create Key**.
4. Copy your **Private Token**.

For full token setup details, see the
[Authentication Guide](../guides/authentication.md).

<br>

## Step 2: Make Your First Request

This request retrieves details for an existing Eventbrite event.
Replace `{event_id}` with a valid event ID from your Eventbrite account.

```bash
curl --request GET \
  --header "Authorization: Bearer YOUR_PRIVATE_TOKEN" \
  "https://www.eventbriteapi.com/v3/events/{event_id}/"
```

To find your `event_id`, open the event in your Eventbrite account.
The ID is the number at the end of the event URL:

```
https://www.eventbrite.com/e/my-event-name-12345678901
                                           ^^^^^^^^^^^
                                    This is your event_id
```

<br>

## Step 3: Verify the Response

A successful request returns `200 OK` with a JSON object containing
the event details.

```json
{
  "name": { "text": "My Event", "html": "<p>My Event</p>" },
  "status": "live",
  "start": { "utc": "2026-06-01T09:00:00Z" },
  "end":   { "utc": "2026-06-01T17:00:00Z" }
}
```

If you receive a `401 Unauthorized` error, check that your token is
correct and included in the `Authorization` header — not as a URL
parameter. For a full list of error codes and fixes, see the
[Error Reference](../api/error-reference.md).

<br>

## Next Steps

You have made your first successful API request.
Choose what to do next based on your goal.

| Goal | Guide |
| --- | --- |
| Learn the full API flow step by step | [First API Call Tutorial](../tutorials/first-api-call.md) |
| See all available endpoints | [API Reference](../api/api-reference.md) |
| Handle responses safely in code | [Response Handling Guide](../guides/response_handling.md) |
| Fix a problem | [Troubleshooting Guide](../guides/troubleshooting.md) |

<br>
