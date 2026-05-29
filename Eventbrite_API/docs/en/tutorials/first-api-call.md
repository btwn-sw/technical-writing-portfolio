# First API Call Tutorial

Learn how the Eventbrite API works by making a real request and
inspecting the response. This tutorial uses the Eventbrite API Console
and Postman — no code editor required.

**By the end of this tutorial, you will be able to:**

- Authenticate an API request using a Private Token
- Call the Retrieve an Event endpoint from the Eventbrite API Console
- Read and interpret the JSON response
- Import and re-run the request in Postman

<br>

## Table of Contents

- [Prerequisites](#prerequisites)
- [Step 1: Open the API Reference](#step-1-open-the-api-reference)
- [Step 2: Enable the Interactive Console](#step-2-enable-the-interactive-console)
- [Step 3: Configure the Request](#step-3-configure-the-request)
- [Step 4: Inspect the Response](#step-4-inspect-the-response)
- [Step 5: Export as cURL](#step-5-export-as-curl)
- [Step 6: Import into Postman](#step-6-import-into-postman)

<br>

## Prerequisites

- An active Eventbrite account
- A valid **Private Token** — if you don't have one yet, follow the
[Authentication Guide](https://claude.ai/guides/authentication.md) first, then return here
- Postman installed or accessible at [postman.com](https://www.postman.com/)

<br>

## Step 1: Open the API Reference

You will call the **Retrieve an Event** endpoint, which returns details
for a single Eventbrite event.

1. Open the Eventbrite [API Reference](https://www.eventbrite.com/platform/api/).
2. In the sidebar, navigate to **Reference → Event → Retrieve an Event**.

**Result:** The endpoint page displays the request format, required
parameters, and a live console on the right-hand side.

<br>

## Step 2: Enable the Interactive Console

The Eventbrite API Reference includes an interactive console powered by
Apiary. It lets you send live requests directly from the browser.

1. In the right-hand pane, click the **Try** button.

**Result:** The console becomes active and input fields for parameters
and headers appear.

<br>

## Step 3: Configure the Request

Enter the following values in the console.

**URI Parameters**

| Parameter | Value |
| --- | --- |
| `event_id` | A valid event ID from your Eventbrite account |

To find your `event_id`, open the event in your Eventbrite account.
The ID is the number at the end of the event URL:

```
https://www.eventbrite.com/e/my-event-name-12345678901
                                           ^^^^^^^^^^^
                                    This is your event_id
```

**Headers**

```
Authorization: Bearer YOUR_PRIVATE_TOKEN
Content-Type: application/json
```

Replace `YOUR_PRIVATE_TOKEN` with your token.

**Request Body:** Not required for this endpoint.

When you have filled in all fields, click **Call Resource**.

**Result:** The console sends a GET request to
`https://www.eventbriteapi.com/v3/events/{event_id}/`.

<br>

## Step 4: Inspect the Response

Scroll to the **Response Body** section below the console.

A successful request returns `200 OK` with a JSON object.
The key fields are:

```json
{
  "name": { "text": "My Event", "html": "<p>My Event</p>" },
  "description": { "text": "Event description here" },
  "start": { "utc": "2026-06-01T09:00:00Z" },
  "end":   { "utc": "2026-06-01T17:00:00Z" },
  "status": "live"
}
```

Notice that `name` and `description` each return two variants — `text`
and `html`. Use `text` for plain string contexts like notifications, and
`html` when rendering in a web interface. For more on this pattern, see
the [Response Handling Guide](../guides/response_handling.md).

**Result:** You have successfully called a live Eventbrite API endpoint
and received a structured response.

<br>

## Step 5: Export as cURL

To reuse this request outside the console, export it as a cURL command.

1. Click **Show Code Example**, located below the parameters section.
2. Select **cURL** from the language dropdown.
3. Copy the generated command. It looks like this:

```bash
curl --request GET \
  --header "Authorization: Bearer YOUR_PRIVATE_TOKEN" \
  --header "Content-Type: application/json" \
  "https://www.eventbriteapi.com/v3/events/{event_id}/"
```

<br>

## Step 6: Import into Postman

Import the cURL command into Postman to inspect headers, parameters,
and the response in detail.

1. Open Postman and sign in.
2. In the menu bar, click **File → Import**.
3. Select the **Paste** tab and paste the cURL command you copied
in Step 5.
4. Optionally rename the collection — for example, `Eventbrite API`.
5. Click **Import Into Collection**.

Postman populates a new request tab with the method, URL, and
Authorization header already filled in.

6. Click **Send**.

**Result:** A `200 OK` response appears in the response panel.
Select **JSON** from the format dropdown to view the formatted
response body.

<br>

## What You Accomplished

In this tutorial you authenticated a live API request, called the
Retrieve an Event endpoint, read a real JSON response, and set up
the request in Postman for future use. You now have a working
environment to explore the rest of the Eventbrite API.

<br>

## Next Steps

- [API Reference](../api/api-reference.md)
- [Response Handling Guide](../guides/response_handling.md)
- [Code Examples](../examples/code-examples.md)

<br>
