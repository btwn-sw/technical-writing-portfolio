# Trackit API Documentation

This repository contains a complete API reference for Trackit, a fictional
task management API. Use the OpenAPI specification to explore the API contract,
or read the structured API reference for human-readable endpoint documentation.

<br>

## What's Included

Trackit covers five functional areas:

- Authentication (register / login)
- User profile management
- Project management
- Task management
- Notifications

This API is not connected to a live backend. It was created as a
specification-first documentation project.

<br>

## Repository Structure

```
/docs
 ├─ api/
 │  ├─ openapi.yaml        # OpenAPI 3.0 specification
 │  └─ api-reference.md    # Human-readable API reference
 └─ README.md              # Project overview
```

| File | When to use |
| --- | --- |
| `openapi.yaml` | Explore endpoint contracts, request/response schemas, and enums. Import into Swagger UI or Postman. |
| `api-reference.md` | Read structured developer-facing documentation with explanations and examples. |

<br>

## Documentation Approach

This project follows a spec-first workflow:

1. Define the API contract using OpenAPI 3.0.
2. Model reusable schemas and enums.
3. Craft a structured API reference from the specification.
4. Ensure consistent response patterns across all endpoints.

<br>

## About This Project

Trackit is a fictional API created for a technical writing portfolio.
It demonstrates OpenAPI specification writing, schema modeling,
and structured API reference documentation.

Trackit is not a real product and is not intended for production use.

<br>