# Python Flask API Documentation Exercise

## ✅ Selected Option
**Python/Flask API Example – User Registration Endpoint**

**Endpoint Code:**
```python
@app.route('/api/users/register', methods=['POST'])
def register_user():
    """Register a new user"""
    # ... code ...
```

---

## 🔹 PROMPT 1 — Comprehensive Endpoint Documentation

### User Registration API Documentation

#### Endpoint
`POST /api/users/register`

#### Description
This endpoint allows a new user to register an account in the system by providing basic personal and authentication details. Upon successful registration, the API creates a new user record and returns confirmation details.

#### Request Body Parameters

| Field     | Type   | Required | Description                        |
|-----------|--------|----------|-----------------------------------|
| username  | String | Yes      | Unique username for the user       |
| email     | String | Yes      | User's email address               |
| password  | String | Yes      | Password for the account           |
| full_name | String | No       | User's full name                   |

#### Example Request Body
```json
{
  "username": "julius123",
  "email": "julius@example.com",
  "password": "SecurePass123",
  "full_name": "Julius Kakai"
}
```

#### Response Format

**Success Response (201 Created)**
```json
{
  "message": "User registered successfully",
  "user_id": 42
}
```

**Error Responses**

| Status Code | Message               | Description                                   |
|-------------|-----------------------|-----------------------------------------------|
| 400         | Invalid input data    | Missing or malformed request fields           |
| 409         | User already exists   | Username or email already registered          |
| 500         | Internal server error | Unexpected server failure                     |

#### Authentication
No authentication is required for this endpoint.

#### Example Requests

**Example 1: Successful Registration**

*Request:*
```
POST /api/users/register
Content-Type: application/json
```

*Response:*
```json
{
  "message": "User registered successfully",
  "user_id": 42
}
```

**Example 2: Duplicate User**

*Response:*
```json
{
  "error": "User already exists"
}
```

#### Special Considerations
- Passwords should be hashed before storage
- Input validation should be enforced server-side
- Rate limiting is recommended to prevent abuse

---

## 🔹 PROMPT 2 — Convert to OpenAPI (Swagger)

```yaml
openapi: 3.0.0
info:
  title: User Registration API
  version: 1.0.0
paths:
  /api/users/register:
    post:
      summary: Register a new user
      requestBody:
        required: true
        content:
          application/json:
            schema:
              type: object
              required:
                - username
                - email
                - password
              properties:
                username:
                  type: string
                email:
                  type: string
                password:
                  type: string
                full_name:
                  type: string
      responses:
        "201":
          description: User registered successfully
          content:
            application/json:
              schema:
                type: object
                properties:
                  message:
                    type: string
                  user_id:
                    type: integer
        "400":
          description: Invalid input data
        "409":
          description: User already exists
        "500":
          description: Internal server error
```

---

## 🔹 PROMPT 3 — Developer Usage Guide

### Developer Guide: User Registration API

#### Audience
Junior to mid-level developers integrating user authentication into a web or mobile application.

#### Authentication
This endpoint does not require authentication.

#### Making a Request
Send a `POST` request to `/api/users/register` with a JSON body containing user details.

#### Python Example (requests)
```python
import requests

url = "https://api.example.com/api/users/register"
payload = {
    "username": "julius123",
    "email": "julius@example.com",
    "password": "SecurePass123"
}

response = requests.post(url, json=payload)
print(response.json())
```

#### Handling Responses
- 201 → Registration successful
- 400 → Check request payload
- 409 → User already exists
- 500 → Retry or contact support

#### Common Errors
- Missing required fields
- Duplicate email or username
- Invalid email format

#### Best Practices
- Always validate inputs before sending requests
- Use HTTPS to protect credentials
- Handle errors gracefully in the UI

---

## 🔹 Reflection & Learning (Required)

### Most Challenging Parts
Defining error responses without seeing the full implementation required making reasonable assumptions based on common API patterns.

### Prompt Adjustments
More specific prompts produced clearer documentation, especially when requesting examples and structured formats.

### Most Effective Documentation Format
OpenAPI was the most effective format because it is both human-readable and machine-consumable.

### Workflow Integration
This approach can be used early in development to ensure APIs are well-documented before frontend or external integration begins.