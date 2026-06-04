# Trackit API Reference

Trackit is a lightweight personal task management API that helps users organize tasks across daily routines, work, hobbies, and more.

### Table of Contents
 
- [Overview](#overview)
- [Authentication](#authentication)
- [Error Handling](#error-handling)
- [Authentication Endpoints](#authentication-endpoints)
    - [Register a New User](#register-a-new-user)
    - [Log In](#log-in)
- [User Endpoints](#user-endpoints)
    - [Retrieve a User](#retrieve-a-user)
    - [Update a User](#update-a-user)
    - [Upload Profile Image](#upload-profile-image)
- [Project Endpoints](#project-endpoints)
    - [Create a New Project](#create-a-new-project)
    - [Get All Projects](#get-all-projects)
    - [Update Projects](#update-projects)
    - [Delete Projects](#delete-projects)
- [Task Endpoints](#task-endpoints)
    - [Create a New Task in a Project](#create-a-new-task-in-a-project)
    - [Get Tasks in a Project](#get-tasks-in-a-project)
    - [Retrieve a Task](#retrieve-a-task)
    - [Update a Task](#update-a-task)
    - [Delete a Task](#delete-a-task)
- [Overview Endpoints](#overview-endpoints)
    - [Get Tasks Overview](#get-tasks-overview)
- [Notifications Endpoints](#notifications-endpoints)
    - [Get Notifications](#get-notifications)
    - [Mark Notification as Read](#mark-notification-as-read)
- [Schemas](#schemas)
- [Enums](#enums)

<br>

## Overview

### Base URL

```json
https://mock.pstmn.io/v1
```

### Version

- All endpoints are prefixed with: `v1`

**Example:**

```json
GET /v1/user
```

### Content-Type

- Request: `application/json`
- Response: `application/json`
    
    ***Note:** Some endpoints use `multipart/form-data` (e.g., profile image upload).*
    

<br>

## Authentication

Trackit API requires a Bearer Token.

### Bearer Token

**Step 1. Get a Token**

Retrieve a token via either:

- **`POST** /v1/register`
- **`POST** /v1/login`

**Step 2. Send the Token**

Include the token in the `Authorication` header:

```json
Authorization: Bearer <trackitToken>
```

<br>

## Error Handling

Trackit uses standard HTTP status codes. Depending on the endpoint, responses may follow one of two patterns:

### Response Patterns

**A. Flat response**

```json
{
  "code": 200,
  "message": "Login successful.",
  "token": "Bearer trackitMockToken"
}
```

**B. `meta` + `data` response**

```json
{
  "meta": {
    "code": 200,
    "message": "User info retrieved successfully."
  },
  "data": {
    "userId": "user_12345",
    "email": "johndoe@example.com"
  }
}
```

### Common HTTP Status Codes

| Code | Error | Description |
| --- | --- | --- |
| `400` | Bad Request | The request contains invalid or missing input fields. |
| `401` | Unauthorized | Authentication token is missing or invalid. |
| `403` | Forbidden | The user is not authorized to access. |
| `404` | Not Found | The requested resource does not exist. |
| `409` | Conflict | Resource already exists or duplicate detected. |
| `422` | Unprocessable Entity | Validation failed for the request. |
| `500` | Internal Server Error | Something went wrong on internal server. |
| `503` | Service Unavailable | The server is currently unable to handle the request. |

<br>

## Authentication Endpoints

### Register a New User

**Endpoint**

Creates a new user account.

```bash
POST /v1/register
```

**Request Body**

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `email`  | string (email) | Yes | User’s email (must be unique) |
| `password` | string (password) | Yes | User’s password |
| `name` | string | Yes | Full name of the user |
| `nickname`  | string | Optional | Nickname displayed in the app |

**Sample Request**

```json
{
  "email": "johndoe@example.com",
  "password": "password123",
  "name": "John Doe",
  "nickname": "WriterJohn"
}
```

**Response**

`201` Created

- User successfully registered.
    
    ```json
    {
      "code": 201,
      "message": "User registered successfully.",
      "userId": "user_12345"
    }
    ```
    

### Log In

**Endpoint**

```bash
POST /v1/login
```

**Request Body**

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `email`  | string (email) | Yes | User’s email (must be unique) |
| `password` | string (password) | Yes | User’s password |

**Sample Request**

```json
{
  "email": "johndoe@example.com",
  "password": "password123"
}
```

**Response**

`200` OK

- Login successful.
    
    ```json
    {
      "code": 200,
      "message": "Login successful.",
      "token": "Bearer trackitToken"
    }
    ```
    

<br>

## User Endpoints

These endpoints allow an authenticated user to retrieve and update their profile information.

### Retrieve a User

**Endpoint**

Returns the authenticated user’s profile information.

```bash
GET /v1/me
```

**Header Parameter**

```bash
Authorization: Bearer <token>
```

**Response**

`200` OK

```json
{
  "meta": {
    "code": 200,
    "message": "User info retrieved successfully."
  },
  "data": {
    "userId": "user_12345",
    "email": "johndoe@example.com",
    "name": "John Doe",
    "nickname": "WriterJohn",
    "profileImageUrl": "https://example.com/profile.jpg",
    "createdAt": "2026-01-01T10:00:00Z"
  }
}
```

### Update a User

**Endpoint**

Updates the authenticated user’s profile information.

```bash
PATCH /v1/me
```

**Header Parameter**

```bash
Authorization: Bearer <token>
```

**Request Body**

At least one field must be provided.

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `name` | string | Optional | Full name of the user |
| `nickname`  | string | Optional | Nickname displayed in the app |

Example Request

```json
{
  "nickname": "SuperWriter"
}
```

**Response**

`200` OK: Profile updated successfully.

```json
{
  "code": 200,
  "message": "User profile updated successfully."
}
```

### Upload Profile Image

**Endpoint**

Uploads a profile image for the authenticated user.

```bash
POST /v1/me/profile-image
```

**Header Parameter**

```bash
Authorization: Bearer <token>
```

**Content-Type**

```bash
multipart/form-data
```

**Request Body**

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `image` | file (binary) | Yes | Profile image file |

**cURL Sample**

```bash
curl -X POST https://api.trackit.com/v1/me/profile-image \
  -H "Authorization: Bearer trackitToken" \
  -F "image=@profile.jpg"
```

**Response**

`200` OK: Image uploaded successfully.

```json
{
  "meta": {
    "code": 200,
    "message": "Profile image uploaded successfully."
  },
  "data": {
    "profileImageUrl": "https://example.com/profile_12345.jpg"
  }
}
```

## Project Endpoints

### Create a New Project

**Endpoint**

Creates a new project under the authenticated user’s account.

```bash
POST /v1/projects
```

**Header Parameter**

```bash
Authorization: Bearer <token>
```

**Request Body**

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `title` | string | Yes | Project title |
| `colour` | string | Yes | HEX colour (e.g., `#87CDFA`) |
| `isDeletable`  | boolean | No | Whether the project can be deleted. |

**Sample Request**

```json
{
  "title": "Car Maintenance",
  "color": "#87CEFA",
  "isDeletable": true
}
```

**Response**

`200` Created

```json
{
  "meta": {
    "code": 201,
    "message": "Project created successfully."
  },
  "data": {
    "projectId": "proj_123",
    "title": "Car Maintenance",
    "color": "#87CEFA",
    "isDeletable": true,
    "createdAt": "2025-04-17T12:00:00Z"
  }
}
```

### Get All Projects

**Endpoint**

Retrieves all projects created by the authenticated user.

```bash
GET /v1/projects
```

**Header Parameter**

```bash
Authorization: Bearer <token>
```

**Response**

`200` OK

```json
{
  "meta": {
    "code": 200,
    "message": "All projects retrieved successfully."
  },
  "data": [
    {
      "projectId": "proj_001",
      "title": "Work",
      "color": "#FF6B6B",
      "isDeletable": true,
      "createdAt": "2025-04-17T09:00:00Z"
    },
    {
      "projectId": "proj_004",
      "title": "Pet Care",
      "color": "#FFD54F",
      "isDeletable": false,
      "createdAt": "2025-04-17T09:04:00Z"
    }
  ]
}
```

### Update Projects

**Endpoint**

Updates the title, colour, or deletable status of an existing project. 

```bash
PATCH /v1/projects/{projectId}
```

**Header Parameter**

```bash
Authorization: Bearer <token>
```

**Path Parameter**

| Name | Type | Required | Description |
| --- | --- | --- | --- |
| `projectId`  | string | Yes | Unique ID of the project to update |

**Request Body**

At least one of the fields must be provided.

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `title` | string | No | Updated project title |
| `colour` | string | No | Updated HEX colour (e.g., `#87CDFA`) |
| `isDeletable`  | boolean | No | Updated deletable status |

**Sample Request**

```json
{
  "title": "Updated Project Title",
  "color": "#009688",
  "isDeletable": true
}
```

**Response**

`200` OK: Project updated successfully.

```json
{
  "meta": {
    "code": 200,
    "message": "Project updated successfully."
  },
  "data": {
    "projectId": "proj_003",
    "title": "Updated Project Title",
    "color": "#009688",
    "isDeletable": true,
    "updatedAt": "2025-04-17T14:00:00Z"
  }
}
```

### Delete Projects

**Endpoint**

Deletes the specified project if it is deletable and owned by the authenticated user.

```bash
DELETE /v1/projects/{projectId}
```

**Header Parameter**

```bash
Authorization: Bearer <token>
```

**Path Parameter**

| Name | Type | Required | Description |
| --- | --- | --- | --- |
| `projectId`  | string | Yes | Unique ID of the project to delete |

**Request Body**

`204` No Content: Project deleted successfully.

```json
{
  "code": 204,
  "message": "Project deleted successfully."
}
```

***Note**: Although `204` typically has an empty body, this API returns a minimal JSON message per the spec.*

<br>

## Task Endpoints

These endpoints let an authenticated user create and manage tasks inside projects, retrieve a single task, and view tasks in a date range.

### Create a New Task in a Project

**Endpoint**

Creates a new task under the specified proejct.

```bash
POST /v1/projects/{projectId}/tasks
```

**Header Parameter**

```bash
Authorization: Bearer <token>
```

**Path Parameters**

| Name | Type | Required | Description |
| --- | --- | --- | --- |
| `projectId`  | string | Yes | Unique ID of the project |

**Request Body**

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `title` | string | Yes | Task title |
| `dueDate` | string (date-time) | Yes | Due date/time (ISO 8601) |
| `status` | string | Yes | `not-started` | `in-progress`| `completed` | `delayed` |
| `priority`  | string | No | `low` | `medium` | `high` |
| `description` | string | No | Task description |

**Sample Request**

```json
{
  "title": "Finish outline for new novel",
  "description": "Initial draft of chapter structure",
  "dueDate": "2025-04-20T18:00:00Z",
  "status": "not-started",
  "priority": "high"
}
```

**Response**

`201` Created: Task Created successfully.

```json
{
  "meta": {
    "code": 201,
    "message": "Task created successfully."
  },
  "data": {
    "taskId": "task_001",
    "projectId": "proj_001",
    "title": "Finish outline for new novel",
    "description": "Initial draft of chapter structure",
    "status": "not-started",
    "priority": "high",
    "dueDate": "2025-04-20T18:00:00Z",
    "createdAt": "2025-04-17T15:00:00Z"
  }
}
```

### Get Tasks in a Project

**Endpoint**

Retrieves all tasks for a given project.

```bash
GET /v1/projects/{projectId}/tasks
```

**Header Parameter**

```bash
Authorization: Bearer <token>
```

**Path Parameters**

| Name | Type | Required | Description |
| --- | --- | --- | --- |
| `projectId`  | string | Yes | Unique ID of the project |

**Response**

`200` OK: Tasks retrieved successfully.

```json
{
  "meta": {
    "code": 200,
    "message": "Tasks retrieved successfully."
  },
  "data": [
    {
      "taskId": "task_011",
      "projectId": "proj_003",
      "title": "Go to bookstore",
      "description": "Spend an hour browsing new releases",
      "status": "not-started",
      "priority": "medium",
      "dueDate": "2025-04-23T15:00:00Z",
      "createdAt": "2025-04-17T11:10:00Z"
    }
  ]
}
```

### Retrieve a Task

**Endpoint**

Retrieves a single task by its ID.

```bash
GET /v1/tasks/{taskId}
```

**Header Parameter**

```bash
Authorization: Bearer <token>
```

**Path Parameters**

| Name | Type | Required | Description |
| --- | --- | --- | --- |
| `taskId`  | string | Yes | Unique ID of the task |

**Response**

`200` OK: Task retrieved successfully.

```json
{
  "meta": {
    "code": 200,
    "message": "Task retrieved successfully."
  },
  "data": {
    "taskId": "task_001",
    "projectId": "proj_001",
    "title": "Write outline",
    "description": "Plan the structure of the upcoming article",
    "status": "not-started",
    "priority": "high",
    "dueDate": "2025-05-20T08:00:00Z",
    "createdAt": "2025-04-17T09:00:00Z"
  }
}
```

### Update a Task

**Endpoint**

Updates fields on an existing task.

```bash
PATCH /v1/tasks/{taskId}
```

**Header Parameter**

```bash
Authorization: Bearer <token>
```

**Path Parameters**

| Name | Type | Required | Description |
| --- | --- | --- | --- |
| `taskId`  | string | Yes | Unique ID of the task |

**Request Body**

Provide one or more fields to update.

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `title` | string | No | Updated task title |
| `description`  | string | No | Updated description |
| `dueDate` | string (date-time) | No | Updated due date/time (ISO 8601) |
| `status` | string | No | `not-started` | `in-progress`| `completed` | `delayed` |
| `priority`  | string | No | `low` | `medium` | `high` |

**Sample Request**

```json
{
  "title": "Updated draft title",
  "status": "in-progress"
}
```

**Response**

`200` OK: Task updated successfully.

```json
{
  "meta": {
    "code": 200,
    "message": "Task updated successfully."
  },
  "data": {
    "taskId": "task_004",
    "projectId": "proj_001",
    "title": "Updated draft title",
    "description": "Start the first draft of chapter one.",
    "status": "in-progress",
    "priority": "medium",
    "dueDate": "2025-04-24T09:00:00Z",
    "createdAt": "2025-04-17T09:30:00Z"
  }
}
```

### Delete a Task

**Endpoint**

Deletes a task by its ID.

```json
DELETE /v1/tasks/{taskId}
```

**Header Parameter**

```bash
Authorization: Bearer <token>
```

**Path Parameters**

| Name | Type | Required | Description |
| --- | --- | --- | --- |
| `taskId`  | string | Yes | Unique ID of the task |

**Response**

`204` No Content: Task deleted successfully.

```json
{
  "code": 204,
  "message": "Task deleted successfully."
}
```

<br>

## Overview Endpoints

### Get Tasks Overview

**Endpoint**

Returns tasks within a date range, optionally filtered by status or project.

```json
GET /v1/tasks/overview
```

**Header Parameter**

```bash
Authorization: Bearer <token>
```

**Query Parameter**

| Name | Type | Required | Description |
| --- | --- | --- | --- |
| `startDate` | string (date) | Yes | Start date (ISO 8601 `YYYY-MM-DD`) |
| `endDate` | string (date) | No | End date (ISO 8601 `YYYY-MM-DD`) |
| `status` | string | No | `not-started` | `in-progress` | `completed` | `delayed` |
| `projectId` | string | No | Filter by project ID |

**Response**

`200` OK: Tasks overview retrieved successfully.

```json
{
  "meta": {
    "code": 200,
    "message": "Tasks overview retrieved successfully."
  },
  "data": [
    {
      "taskId": "task_006",
      "projectId": "proj_002",
      "title": "Grocery shopping",
      "status": "not-started",
      "priority": "medium",
      "dueDate": "2025-04-20T11:00:00Z"
    },
    {
      "taskId": "task_011",
      "projectId": "proj_003",
      "title": "Go to bookstore",
      "status": "not-started",
      "priority": "medium",
      "dueDate": "2025-04-23T15:00:00Z"
    }
  ]
}
```

<br>

## Notifications Endpoints

### Get Notifications

**Endpoint**

Retrieves notifications for upcoming/overdue tasks or system-generated task alerts.

```json
GET /v1/notifications
```

**Header Parameter**

```bash
Authorization: Bearer <token>
```

**Response**

`200` OK: Notification list retrieved successfully.

```json
{
  "meta": {
    "code": 200,
    "message": "Notifications retrieved successfully."
  },
  "data": [
    {
      "notificationId": "n_001",
      "taskId": "task_004",
      "type": "due-soon",
      "message": "Your task 'Draft writing' is due tomorrow.",
      "read": false,
      "createdAt": "2025-04-23T08:00:00Z"
    },
    {
      "notificationId": "n_002",
      "taskId": "task_005",
      "type": "delayed",
      "message": "Your task 'Review references' is delayed.",
      "read": true,
      "createdAt": "2025-04-22T12:00:00Z"
    }
  ]
}
```

### Mark Notification as Read

**Endpoint**

Marks a notification as read to indicate it has been acknowledged.

```json
PATCH /v1/notifications/{notificationId}
```

**Header Parameter**

```bash
Authorization: Bearer <token>
```

**Path Parameters**

| Name | Type | Required | Description |
| --- | --- | --- | --- |
| `notificationId`  | string | Yes | Unique ID of the notification to update. |

**Request Body**

Provide one or more fields to update.

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `read`  | boolean | Yes | Set to `true` to mark as read |

**Sample Request**

```json
{
  "read": true
}
```

**Response**

`200` OK: Notification marked as read.

```json
{
  "meta": {
    "code": 200,
    "message": "Notification marked as read."
  },
  "data": {
    "notificationId": "n_001",
    "taskId": "task_004",
    "read": true,
    "updatedAt": "2025-04-23T12:00:00Z"
  }
}
```

<br>

## Schemas

This section defines the core object models and enumerations used throughout the Trackit API.

### User Object

Represents an authenticated user.

```json
{
  "userId": "user_12345",
  "email": "johndoe@example.com",
  "name": "John Doe",
  "nickname": "WriterJohn",
  "profileImageUrl": "https://example.com/profile.jpg",
  "createdAt": "2026-01-01T10:00:00Z"
}
```

| Field | Type | Description |
| --- | --- | --- |
| userId | string | Unique user identifier |
| email | string (email) | User email address |
| name | string | Full name |
| nickname | string | Optional display nickname |
| profileImageUrl | string (URL) | Profile image location |
| createdAt | string (date-time) | ISO 8601 timestamp |

### Project Object

Represents a project container for tasks.

```json
{
  "projectId": "proj_001",
  "title": "Work",
  "color": "#FF6B6B",
  "isDeletable": true,
  "createdAt": "2025-04-17T09:00:00Z"
}
```

| Field | Type | Description |
| --- | --- | --- |
| projectId | string | Unique project identifier |
| title | string | Project title |
| colour | string (HEX) | HEX colour code |
| isDeletable | boolean | Indicates if deletion is allowed |
| createdAt | string (date-time) | Creation timestamp |

### Task Object

Represents an individual task within a project.

```json
{
  "taskId": "task_001",
  "projectId": "proj_001",
  "title": "Write outline",
  "description": "Plan the article structure",
  "status": "not-started",
  "priority": "high",
  "dueDate": "2025-05-20T08:00:00Z",
  "createdAt": "2025-04-17T09:00:00Z"
}
```

| Field | Type | Description |
| --- | --- | --- |
| taskId | string | Unique task identifier |
| projectId | string | Parent project ID |
| title | string | Task title |
| description | string | Optional task description |
| status | TaskStatus | Current task status |
| priority | TaskPriority | Task priority level |
| dueDate | string (date-time) | Due date (ISO 8601) |
| createdAt | string (date-time) | Creation timestamp |

### Notification Object

Represents a system-generated alert related to a task.

```json
{
  "notificationId": "n_001",
  "taskId": "task_004",
  "type": "due-soon",
  "message": "Your task is due tomorrow.",
  "read": false,
  "createdAt": "2025-04-23T08:00:00Z"
}
```

| Field | Type | Description |
| --- | --- | --- |
| notificationId | string | Unique notification ID |
| taskId | string | Related task ID |
| type | NotificationType | Notification category |
| message | string | Human-readable alert message |
| read | boolean | Whether the notification is read |
| createdAt | string (date-time) | Timestamp |

<br>

## Enums

Enumerations define restricted sets of values used by certain fields.

### TaskStatus

Defines the lifecycle stats of a task.

| Value | Description |
| --- | --- |
| `not-started` | Task has not been started. |
| `in-progress` | Task is currently in progress. |
| `completed` | Task has been completed. |
| `delayed`  | Task is past due date. |

### TaskPriority

Defines the urgency level of a task.

| Value | Description |
| --- | --- |
| `low` | Low priority |
| `medium` | Medium priority |
| `high`  | High priority |

### NotificationType

Defines the category of a notification.
| Value | Description |
| --- | --- |
| `due-soon` | Task is approaching due date. |
| `delayed` | Task is overdue. |
| `system`  | System-generated notification. |

<br>

---