<xaiArtifact artifact_id="e38240ef-87d9-4572-90f3-8dd8482312ba" artifact_version_id="ccb195fa-55d7-4f8c-b5ec-1e39571293ea" title="api_documentation.md" contentType="text/markdown">

# API Documentation for Student-Tutor-Admin-Parent System

This document provides detailed information on the API endpoints for the system managing interactions among Students, Sheikhs, Admins, and Parents. The API supports user authentication, session management, progress tracking, badge management, reporting, and notifications. All endpoints are prefixed with `/api`.

## Base URL
`http:localhost:8080/api/`

## Authentication
All endpoints require authentication unless specified. Use an API key in the `Authorization` header or Google OAuth token for specific endpoints.

- **Header**: `Authorization: Bearer <token>`
- **Google OAuth**: Use `access_token` obtained from Google OAuth flow.

## Content Type
All requests and responses use `application/json` unless otherwise specified.

---

## User Authentication

### POST /auth/login
Authenticate a user with email and password.

**Request Body**:
```json
{
  "email": "string",
  "password": "string"
}
```

**Response**:
- **200 OK**:
  ```json
  {
   "type": "Bearer",
    "token": "string"
  }
  ```
- **400 Bad Request**: Invalid credentials.

### POST /auth/signup
Register a new user.

**Request Body** (UserDTO):
```json
some validation:
username : unique notblank and notnull
email: unique  notblank and notnul
password : must complex notblank and notnul
role must be  one of this types by this typing (ADMIN|TUTOR|PARENT|STUDENT) required
age : not null
phone : not null similar to this 11 letter similar to this structre +1234567890 or 123-456-7890 
memorizationLevel : should added for student , nullable

{
  "email": "string",
  "password": "string",
  "username": "string",
  "role": "string (student|sheikh|admin|parent)",
  "name": "string",
  "age": int ,
  "phone": "string (optional)"
}
```

**Response**:
- **201 Created**:
  ```json
  {
    "message": "User created successfully"
  }
  ```
- **400 Bad Request**: Invalid or missing fields.

### POST /auth/oauth2/google/login
Authenticate a user via Google OAuth.
frontend have to redirect to url int this response 

**Response**:
- **200 OK**:
  ```json
  {
     "follow this url in the browser": "http://localhost:8080/oauth2/authorization/google?action=login"
  }
  ```

- after redirect to the following url and register with google 
### GET http://localhost:8080/oauth2/authorization/google?action=login

**Response**:
- **200 OK**:
  ```json
  {
    "success": true
  ,
  "message": "OAuth2 authentication successful"
  ,
  "token": "<Access token>"
  ,
  "email":"<user email>",
  "googleId": "<user google id>"
  
  }
  ```
  - **400 Bad request**
  ```json
  {
    "success": false
  ,
  "error": "string"
  "message": "string"
  
  }
  ```
    

### POST /auth/google/signup
Register a new user via Google OAuth.

frontend have to redirect to url int this response 

**Response**:
- **200 OK**:
  ```json
  {
     "follow this url in the browser": "http://localhost:8080/oauth2/authorization/google?action=login"
  }
  ```
- **401 Unauthorized**: Invalid token.

- after redirect to the url and 
### GET http://localhost:8080/oauth2/authorization/google?action=login

**Response**:
- **200 OK**:
  ```json
  {
    "success": true
  ,
  "message": "OAuth2 authentication successful"
  ,
  "token": "<Access token>"
  ,
  "email":"<user email>",
  "googleId": "<user google id>"
  
  }
  ```
  - **400 Bad request**
  ```json
  {
    "success": false
  ,
  "error": "string"
  "message": "string"
  
  }
  ```

## Student Features

### POST /students/sessions/book
Schedule a session using Calendly.

**Request Body**:
```json
{
  "session_id": "string",
  "student_id": "string"
}
```

**Response**:
- **201 Created**:
  ```json
  {
    "session_id": "string",
    "message": "Session booked successfully"
  }
  ```
- **400 Bad Request**: Invalid session or student ID.

### POST /students/sessions/attend
Join a scheduled session.

**Request Body**:
```json
{
  "session_id": "string"
}
```

**Response**:
- **200 OK**:
  ```json
  {
    "session_url": "string",
    "message": "Session joined"
  }
  ```
- **404 Not Found**: Session not found.

### GET /students/sessions
Display sessions filtered by status and period.

**Query Parameters**:
- `status`: string (upcoming|done, optional)
- `period`: string (month|week, optional)

**Response**:
- **200 OK**:
  ```json
  [
    {
      "session_id": "string",
      "status": "string",
      "date": "ISO8601 string",
      "sheikh_id": "string"
    }
  ]
  ```

### PUT /students/profile
Update student profile.

**Request Body** (UserDTO):
```json
{
  "user_id": "string",
  "email": "string (optional)",
  "username": "string (optional)",
  "name": "string (optional)",
  "phone": "string (optional)"
}
```

**Response**:
- **200 OK**:
  ```json
  {
    "message": "Profile updated successfully"
  }
  ```
- **400 Bad Request**: Invalid user ID or data.

### DELETE /students/profile
Delete student profile.

**Request Body**:
```json
{
  "user_id": "string"
}
```

**Response**:
- **200 OK**:
  ```json
  {
    "message": "Profile deleted successfully"
  }
  ```
- **404 Not Found**: User not found.

### GET /students/profile/{username}
View user profile details.

**Response**:
- **200 OK**:
  ```json
  {
    "id": "string",
    "username": "string",
    "email": "string",
    "name": "string",
    "role": "string"
  }
  ```
- **404 Not Found**: User not found.

### GET /students/search
Search for user profiles.

**Query Parameters**:
- `query`: string (search term, e.g., username or name)

**Response**:
- **200 OK**:
  ```json
  [
    {
      "id": "string",
      "username": "string",
      "name": "string"
    }
  ]
  ```

### GET /students/progress
Display student progress filtered by period.

**Query Parameters**:
- `period`: string (weekly|daily|monthly, optional)

**Response**:
- **200 OK**:
  ```json
  {
    "sessions_attended": "number",
    "quizzes_completed": "number",
    "pages_learned": "number",
    "period": "string"
  }
  ```

### GET /students/progress/all
Display complete progress history.

**Response**:
- **200 OK**:
  ```json
  [
    {
      "date": "ISO8601 string",
      "sessions_attended": "number",
      "quizzes_completed": "number",
      "pages_learned": "number"
    }
  ]
  ```

### DELETE /students/sessions/cancel
Cancel a session.

**Request Body**:
```json
{
  "session_id": "string"
}
```

**Response**:
- **200 OK**:
  ```json
  {
    "message": "Session cancelled successfully"
  }
  ```
- **404 Not Found**: Session not found.

---

## Sheikh Features

### POST /sheikhs/sessions
Create a new session using Calendly.

**Request Body** (Session):
```json
{
  "sheikh_id": "string",
  "date": "ISO8601 string",
  "duration": "number (minutes)",
  "calendly_event_id": "string"
}
```

**Response**:
- **201 Created**:
  ```json
  {
    "session_id": "string",
    "message": "Session created successfully"
  }
  ```
- **400 Bad Request**: Invalid session data.

### GET /sheikhs/sessions/{session_id}
View session details.

**Response**:
- **200 OK**:
  ```json
  {
    "session_id": "string",
    "date": "ISO8601 string",
    "duration": "number",
    "student_ids": ["string"],
    "calendly_event_id": "string"
  }
  ```
- **404 Not Found**: Session not found.

### GET /sheikhs/students/{sheikh_id}
List all students paired with a Sheikh.

**Response**:
- **200 OK**:
  ```json
  [
    {
      "student_id": "string",
      "username": "string",
      "name": "string"
    }
  ]
  ```

### POST /sheikhs/badges/assign
Assign a badge to a student.

**Request Body**:
```json
{
  "badge_id": "string",
  "student_id": "string"
}
```

**Response**:
- **200 OK**:
  ```json
  {
    "message": "Badge assigned successfully"
  }
  ```
- **404 Not Found**: Badge or student not found.

### GET /sheikhs/badges/{badge_id}
Select a badge.

**Response**:
- **200 OK**:
  ```json
  {
    "badge_id": "string",
    "name": "string",
    "description": "string"
  }
  ```
- **404 Not Found**: Badge not found.

### GET /sheikhs/badges
Display all available badges.

**Response**:
- **200 OK**:
  ```json
  [
    {
      "badge_id": "string",
      "name": "string",
      "description": "string"
    }
  ]
  ```

### POST /sheikhs/badges
Create a new badge.

**Request Body**:
```json
{
  "name": "string",
  "description": "string"
}
```

**Response**:
- **201 Created**:
  ```json
  {
    "badge_id": "string",
    "message": "Badge created successfully"
  }
  ```
- **400 Bad Request**: Invalid badge data.

### GET /sheikhs/sessions/{sheikh_id}
Display all sessions for a Sheikh.

**Query Parameters**:
- `period`: string (weekly|daily|monthly, optional)

**Response**:
- **200 OK**:
  ```json
  [
    {
      "session_id": "string",
      "date": "ISO8601 string",
      "duration": "number",
      "student_ids": ["string"]
    }
  ]
  ```

### GET /sheikhs/students/{student_id}/progress
View a student’s progress.

**Response**:
- **200 OK**:
  ```json
  {
    "student_id": "string",
    "sessions_attended": "number",
    "quizzes_completed": "number",
    "pages_learned": "number"
  }
  ```
- **404 Not Found**: Student not found.

### POST /sheikhs/reports
Send a report to a parent.

**Request Body** (Report):
```json
{
  "student_id": "string",
  "parent_id": "string",
  "content": "string",
  "period": "string (weekly|monthly)"
}
```

**Response**:
- **200 OK**:
  ```json
  {
    "report_id": "string",
    "message": "Report sent successfully"
  }
  ```
- **400 Bad Request**: Invalid report data.

### POST /sheikhs/students/{student_id}/points
Assign points to a student.

**Request Body**:
```json
{
  "points": "number"
}
```

**Response**:
- **200 OK**:
  ```json
  {
    "message": "Points assigned successfully"
  }
  ```
- **404 Not Found**: Student not found.

### PUT /sheikhs/students/{student_id}/points
Modify a student’s points.

**Request Body**:
```json
{
  "points": "number"
}
```

**Response**:
- **200 OK**:
  ```json
  {
    "message": "Points updated successfully"
  }
  ```
- **404 Not Found**: Student not found.

### POST /sheikhs/students/{student_id}/pages
Record pages learned for a student.

**Request Body**:
```json
{
  "pages_learned": "number"
}
```

**Response**:
- **200 OK**:
  ```json
  {
    "message": "Pages recorded successfully"
  }
  ```
- **404 Not Found**: Student not found.

---

## Admin Features

### GET /admin/profiles/{user_id}
View user profile details.

**Response**:
- **200 OK**:
  ```json
  {
    "id": "string",
    "username": "string",
    "email": "string",
    "name": "string",
    "role": "string"
  }
  ```
- **404 Not Found**: User not found.

### GET /admin/students
List all registered students.

**Response**:
- **200 OK**:
  ```json
  [
    {
      "id": "string",
      "username": "string",
      "name": "string"
    }
  ]
  ```

### GET /admin/sheikhs
List all registered Sheikhs.

**Response**:
- **200 OK**:
  ```json
  [
    {
      "id": "string",
      "username": "string",
      "name": "string"
    }
  ]
  ```

### GET /admin/parents
List all registered parents.

**Response**:
- **200 OK**:
  ```json
  [
    {
      "id": "string",
      "username": "string",
      "name": "string"
    }
  ]
  ```

---

## Parent Features

### GET /parents/reports
Receive reports via WhatsApp API or email.

**Response**:
- **200 OK**:
  ```json
  [
    {
      "report_id": "string",
      "student_id": "string",
      "content": "string",
      "date": "ISO8601 string"
    }
  ]
  ```

### GET /parents/reports/{report_id}
View a specific report.

**Response**:
- **200 OK**:
  ```json
  {
    "report_id": "string",
    "student_id": "string",
    "content": "string",
    "date": "ISO8601 string"
  }
  ```
- **404 Not Found**: Report not found.

### POST /parents/students/pair
Pair a student with a parent.

**Request Body**:
```json
{
  "student_username": "string"
}
```

**Response**:
- **200 OK**:
  ```json
  {
    "message": "Student paired successfully"
  }
  ```
- **404 Not Found**: Student not found.

---

## Automatic Features

### POST /auto/reports
Automatically send reports to parents.

**Request Body** (Report):
```json
{
  "student_id": "string",
  "parent_id": "string",
  "content": "string",
  "period": "string (weekly|monthly)"
}
```

**Response**:
- **200 OK**:
  ```json
  {
    "report_id": "string",
    "message": "Report sent successfully"
  }
  ```

### POST /auto/notifications
Send notifications to users.

**Request Body** (Notification):
```json
{
  "user_id": "string",
  "content": "string"
}
```

**Response**:
- **200 OK**:
  ```json
  {
    "notification_id": "string",
    "message": "Notification sent successfully"
  }
  ```

### GET /auto/badges/{badge_id}
Display a badge assigned by a Sheikh.

**Response**:
- **200 OK**:
  ```json
  {
    "badge_id": "string",
    "name": "string",
    "description": "string"
  }
  ```
- **404 Not Found**: Badge not found.

### GET /auto/notifications
View received notifications.

**Response**:
- **200 OK**:
  ```json
  [
    {
      "notification_id": "string",
      "content": "string",
      "date": "ISO8601 string"
    }
  ]
  ```

---

## Error Codes
- **400 Bad Request**: Invalid or missing request parameters.
- **401 Unauthorized**: Missing or invalid authentication.
- **404 Not Found**: Resource (e.g., user, session, badge) not found.
- **500 Internal Server Error**: Server-side issue.

## Rate Limits
- 100 requests per minute per user.
- Contact support for higher limits.

## Notes
- **Progress Tracking**: Progress is tracked via sessions attended, quizzes completed, and pages learned, with filtering by daily, weekly, or monthly periods.
- **Integrations**: 
  - Calendly is used for session scheduling (`session_id` maps to Calendly event IDs).
  - WhatsApp API or email is used for report delivery.
- **Shared Features**: Profile management (`update profile`, `delete profile`, `show profile`) and session cancellation are available to multiple roles.
- **Examples**:
  - **Login**:
    ```bash
    curl -X POST https://api.example.com/auth/login \
    -H "Content-Type: application/json" \
    -d '{"email": "user@example.com", "password": "password123"}'
    ```
  - **Book Session**:
    ```bash
    curl -X POST https://api.example.com/students/sessions/book \
    -H "Authorization: Bearer <API_KEY>" \
    -H "Content-Type: application/json" \
    -d '{"session_id": "123", "student_id": "456"}'
    ```

</xaiArtifact>
