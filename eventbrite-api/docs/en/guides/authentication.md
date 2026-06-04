# Authentication Guide

Authenticate your Eventbrite API requests using a Private Token.
This guide walks you through choosing the right credential type,
generating a token, adding it to your requests, and storing it securely.
All Eventbrite API endpoints require authentication.

<br>

## Table of Contents

- [Choose Your Authentication Method](#choose-your-authentication-method)
- [Generate a Private Token](#generate-a-private-token)
- [Add Your Token to a Request](#add-your-token-to-a-request)
- [Secure Your Token](#secure-your-token)
- [Server-Side OAuth Flow](#server-side-oauth-flow)

<br>

## Choose Your Authentication Method

Eventbrite uses OAuth 2.0, an authorization framework that lets your
application access the API on behalf of a user. Every API request must
include a valid token. Without it, the API returns a `401 Unauthorized`
error.

Eventbrite issues two credential types. Choose based on your use case:

| Credential | What it is | When to use it |
| --- | --- | --- |
| Private Token | A long-lived personal access token tied to your account | Testing, scripts, server-side integrations where only your account is involved |
| API Key + Client Secret | App credentials used in the OAuth flow | Production apps where individual users log in with their own Eventbrite accounts |

This guide uses **Private Token** for all examples.
For production applications that require user-level authorization,
see [Server-Side OAuth Flow](#server-side-oauth-flow).

<br>

## Generate a Private Token

**Prerequisites:** An active Eventbrite account.
[Create one here](https://www.eventbrite.com/signin/) if you don't have one.

1. Log in to [Eventbrite](https://www.eventbrite.com/signin/).
2. Click your profile, then go to **Account Settings**.
3. Select **Developer Links** → **API Keys**.
4. Click **Create API Key** and fill in the required fields.
5. Click **Create Key**.
6. Copy your **Private Token** and store it somewhere safe —
you will not be able to view it again after leaving this page.

**If you already have an API key:** Go to your
[API Keys page](https://www.eventbrite.com/account-settings/apps),
click **Show API key, client secret and tokens** next to your app,
and copy your Private Token from there.

**Verify your token works:** Send the following request.
A `200 OK` response confirms your token is valid.

```bash
curl --request GET \
  --header "Authorization: Bearer YOUR_PRIVATE_TOKEN" \
  "https://www.eventbriteapi.com/v3/users/me/"
```

<br>

## Add Your Token to a Request

Use the `Authorization` header to authenticate every request.
This is the only recommended method for all environments.

```bash
curl --request GET \
  --header "Authorization: Bearer YOUR_PRIVATE_TOKEN" \
  "https://www.eventbriteapi.com/v3/users/me/"
```

Replace `YOUR_PRIVATE_TOKEN` with the token you copied above.

**Query parameter method (not recommended):** You can pass the token
as a URL parameter, but avoid this in production. Tokens in URLs are
recorded in server logs and browser history, which creates a security risk.

```
https://www.eventbriteapi.com/v3/users/me/?token=YOUR_PRIVATE_TOKEN
```

<br>

## Secure Your Token

Treat your Private Token like a password. If exposed, anyone can make
API requests on your behalf.

- Store your token in environment variables, not in source code.
- Use separate tokens for development and production environments.
- Never commit tokens to version control or include them in public repositories.
- Revoke tokens you no longer use from your
[API Keys page](https://www.eventbrite.com/account-settings/apps).

<br>

## Server-Side OAuth Flow

Use the server-side OAuth flow when your application needs to act on behalf
of individual users — for example, a web app where each user logs in with
their own Eventbrite account.

**Prerequisites:** An API Key and Client Secret from your
[API Keys page](https://www.eventbrite.com/account-settings/apps).

### Step 1. Redirect the user

Send the user to the Eventbrite authorization URL:

```
https://www.eventbrite.com/oauth/authorize
  ?response_type=code
  &client_id=YOUR_API_KEY
  &redirect_uri=YOUR_REDIRECT_URI
```

### Step 2. Receive the authorization code

Eventbrite redirects the user to your redirect URI with an authorization
code appended as a query parameter:

```
http://localhost:8080/oauth/redirect?code=YOUR_ACCESS_CODE
```

### Step 3. Exchange the code for an access token

Send a POST request with your authorization code, API key, and client secret:

```bash
curl --request POST \
  --url "https://www.eventbrite.com/oauth/token" \
  --header "Content-Type: application/x-www-form-urlencoded" \
  --data "grant_type=authorization_code" \
  --data "client_id=YOUR_API_KEY" \
  --data "client_secret=YOUR_CLIENT_SECRET" \
  --data "code=YOUR_ACCESS_CODE" \
  --data "redirect_uri=YOUR_REDIRECT_URI"
```

### Step 4. Use the access token

A successful response returns a JSON object containing `access_token`.
Use this token in the `Authorization` header for all subsequent requests,
exactly as you would use a Private Token.

<br>

## Next Steps

- [API Reference](../api/api-reference.md)
- [Code Examples](../examples/code-examples.md)
- [Quick Start Guide](../get-started/quick-start.md)

<br>
