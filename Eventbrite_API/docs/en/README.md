# Eventbrite API Documentation

A complete documentation set for the Eventbrite API, covering
authentication, event management, response handling, and troubleshooting.
Built as a technical writing portfolio project.

<br>

## Documentation Structure

```
docs/en/
├── get-started/
│   └── quick-start.md
├── tutorials/
│   └── first-api-call.md
├── guides/
│   ├── authentication.md
│   ├── pagination-guide.md
│   ├── response-handling.md
│   └── troubleshooting.md
├── api/
│   ├── api-reference.md
│   ├── error-reference.md
│   └── quick-reference.md
├── examples/
│   └── code-examples.md
└── reference/
    └── glossary.md
```

<br>

## What's Included

| Document | Type | Description |
| --- | --- | --- |
| [Quick Start Guide](../en/get-started/quick-start.md) | How-to | Make your first API request in under 3 minutes |
| [First API Call Tutorial](../en/tutorials/first-api-call.md) | Tutorial | Guided walkthrough using the Eventbrite API Console and Postman |
| [Authentication Guide](../en/guides/authentication.md) | How-to | Choose a credential type, generate a Private Token, and authenticate requests |
| [Pagination Guide](../en/guides/pagination.md) | How-to | Retrieve multiple pages of events using continuation tokens |
| [Response Handling Guide](../en/guides/response_handling.md) | Explanation | Why responses are structured the way they are, and how to consume them safely |
| [Troubleshooting Guide](../en/guides/troubleshooting.md) | How-to | Diagnose and fix common API errors by symptom |
| [API Reference](../en/api/api-reference.md) | Reference | Endpoint signatures, parameters, and response fields for all Event endpoints |
| [Error Reference](../en/api/error-reference.md) | Reference | All error codes, their causes, and resolution steps |
| [Quick Reference](../en/api/quick-reference.md) | Reference | Endpoints, headers, status codes, and rate limits at a glance |
| [Code Examples](../en/examples/code-examples.md) | Reference | Request examples in cURL, JavaScript, Node.js, and Python |
| [Glossary](../en/reference/glossary.md) | Reference | Definitions for key terms used across the documentation |

<br>

## Choose Your Path

### I want to make my first API request fast

👉 [**Quick Start Guide**](../en/get-started/quick-start.md)

Get from zero to a working API response in under 3 minutes.
No prior Eventbrite API experience required.

### I want to learn the API step by step

👉 [**First API Call Tutorial**](../en/tutorials/first-api-call.md)

A guided walkthrough using the Eventbrite API Console and Postman.
Covers authentication, live requests, response inspection, and
exporting to cURL.

### I need to look something up

👉 [**Quick Reference**](../en/api/quick-reference.md)

Endpoints, request headers, response fields, status codes, and rate
limits — all on one page.

### I need to fix something that's broken

👉 [**Troubleshooting Guide**](../en/guides/troubleshooting.md)

Organized by error symptom. Each section covers cause, fix steps,
and a verification request.

### I don't understand a term

👉 [**Glossary**](../en/reference/glossary.md)

Definitions for Private Token, continuation token, draft vs live,
UTC vs local, and all other key terms used across the documentation.

<br>

## Direct HTTP Integration

Eventbrite does not provide official SDKs for any programming language.
The API is a standard RESTful HTTP interface — authentication,
requests, and responses are handled directly using standard HTTP clients.

Recommended tools by environment:

| Environment | Tool |
| --- | --- |
| Browser-based JavaScript | `fetch` (built-in) |
| Node.js / server-side JavaScript | `node-fetch` or `axios` |
| Python | `requests` |
| Testing and exploration | Postman |

For working request examples, see [Code Examples](../en/examples/code-examples.md).

<br>

## Intended Audience

- Developers integrating with the Eventbrite API
- Technical writers building API documentation portfolios
- Reviewers evaluating documentation structure and quality

<br>

## Project Status

This is a portfolio project and is not intended for production use.
The content is based on publicly available Eventbrite API documentation
and focuses on documentation structure and best practices.

For official Eventbrite API documentation, see
[eventbrite.com/platform/api](https://www.eventbrite.com/platform/api#/introduction/about-our-api).

<br>
