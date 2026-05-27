# Authentication Guide

Authenticate your Eventbrite API requests using a Private Token. This guide
walks you through generating a token from the Eventbrite Developer Dashboard,
adding it to your requests, and storing it securely. All Eventbrite API
endpoints require authentication.

<br>

## Table of Contents

- [How Eventbrite Authentication Works](#how-eventbrite-authentication-works)
- [Generate a Private Token](#generate-a-private-token)
- [Add Your Token to a Request](#add-your-token-to-a-request)
- [Secure Your Token](#secure-your-token)
- [Server-Side OAuth Flow](#server-side-oauth-flow)

<br>

## How Eventbrite Authentication Works

Eventbrite uses OAuth 2.0, an authorization framework that lets your
application access the API on behalf of a user. Every API request must
include a valid token. Without it, the API returns a 401 Unauthorized error.

Eventbrite issues two credential types:

| Credential | What it is | When to use it |
| --- | --- | --- |
| Private Token | A long-lived personal access token | Testing, server-side scripts |
| API Key + Client Secret | App credentials used in OAuth flows | Production apps with user login |

This guide uses **Private Token** for all examples. For production
applications that require user-level authorization, see
[Server-Side OAuth Flow](#server-side-oauth-flow).

<br>

## Generate a Private Token

**Prerequisites:** An Eventbrite account.
[Create one here](https://www.eventbrite.com/signin/) if you don't have one.

1. Log in to [Eventbrite](https://www.eventbrite.com/signin/).
2. Click your profile, then go to **Account Settings**.
3. Select **Developer Links** → **API Keys**.
4. Click **Create API Key** and fill in the required fields.
5. Click the **Create Key** button.
6. Copy your **Private Token** and store it safely —
you will not be able to view it again after leaving this page.

**Verify:** Make the following request. A `200 OK` response with your
account details confirms your token is valid.

```bash
curl -H "Authorization: Bearer YOUR_PRIVATE_TOKEN" \\
  <https://www.eventbrite.com/api/v3/users/me/>
```

**If you already have an API key:** Go to your
[API Key Management page](https://www.eventbrite.com/account-settings/apps),
click **Show API key, client secret and tokens** next to your app,
and copy your Private Token from there.

<br>

## Add Your Token to a Request

Use the `Authorization` header to authenticate requests. This is the
recommended method for all environments.

```bash
curl -H "Authorization: Bearer YOUR_PRIVATE_TOKEN" \\
  <https://www.eventbrite.com/api/v3/users/me/>
```

Replace `YOUR_PRIVATE_TOKEN` with the token you copied in the previous step.

**Query parameter (not recommended):** You can also pass the token as a URL
parameter. Avoid this method in production — tokens in URLs are logged by
servers and browsers, which creates a security risk.

```bash
<https://www.eventbrite.com/api/v3/users/me/?token=YOUR_PRIVATE_TOKEN>
```

<br>

## Secure Your Token

Treat your Private Token like a password. If it is exposed, anyone can make
API requests on your behalf.

- Store your token in environment variables, not in source code.
- Use separate tokens for development and production environments.
- Never commit tokens to version control or include them in public repositories.
- Delete tokens you no longer use from the
[API Key Management page](https://www.eventbrite.com/account-settings/apps).

<br>

## Server-Side OAuth Flow

Use the server-side OAuth flow when your application needs to authenticate
individual users — for example, a web app where each user logs in with their
own Eventbrite account.

**Prerequisites:** An API Key and Client Secret from your
[API Key Management page](https://www.eventbrite.com/account-settings/apps).

### Step 1. Redirect the user

Send the user to the Eventbrite authorization URL:

```bash
<https://www.eventbrite.com/oauth/authorize>
  ?response_type=code
  &client_id=YOUR_API_KEY
  &redirect_uri=YOUR_REDIRECT_URI
```

### Step 2. Receive the authorization code

Eventbrite redirects the user to your redirect URI with an authorization code:

```bash
<http://localhost:8080/oauth/redirect?code=YOUR_ACCESS_CODE>
```

### Step 3. Exchange the code for an access token

Send a POST request with your authorization code, API key, and client secret:

```bash
curl --request POST \\
  --url <https://www.eventbrite.com/oauth/token> \\
  --header 'Content-Type: application/x-www-form-urlencoded' \\
  --data grant_type=authorization_code \\
  --data client_id=YOUR_API_KEY \\
  --data client_secret=YOUR_CLIENT_SECRET \\
  --data code=YOUR_ACCESS_CODE \\
  --data redirect_uri=YOUR_REDIRECT_URI
```

### Step 4. Verify the token

A successful response returns a JSON object containing `access_token`.
Use this token in the `Authorization` header for subsequent requests.

<br>

## Next Steps

- [API Reference](../api/api-reference.md)
- [Code Examples](../examples/code-examples.md)
- [SDKs](../sdks/sdks.md)

<br>
