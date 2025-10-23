# Invitation Module Documentation

## Overview
The Invitation Module provides comprehensive functionality for managing invitations, including creation, updates, acceptance, and analytics. It supports various invitation types and includes department-level usage statistics.

## Authentication
- **Protected Endpoints**: Require Bearer token authentication
- **Public Endpoints**: No authentication required

---

## Public Endpoints

### 1. Get Invitation by Token
**Endpoint**: `GET /invitation/token/:token`

**Description**: Retrieves an invitation by its token (public access).

**Parameters**:
- `token` (path, required): The invitation token

**Response**: Returns invitation details including agenda, dates, type, and guest information.

---

### 2. Accept Invitation
**Endpoint**: `POST /invitation/accept`

**Description**: Allows guests to accept an invitation by providing their information.

**Request Body**:
```json
{
  "invitation_id": "uuid",
  "email": "string",
  "name": "string", 
  "phone": "string",
  "channel": "string"
}
```

**Parameters**:
- `invitation_id` (required): UUID of the invitation
- `email` (optional): Guest email
- `name` (optional): Guest name
- `phone` (optional): Guest phone number
- `channel` (optional): How the invitation was received

---

### 3. Get Invitation Details
**Endpoint**: `GET /invitation/details/:id`

**Description**: Retrieves detailed information about a specific invitation.

**Parameters**:
- `id` (path, required): The invitation ID (UUID)

---

## Protected Endpoints

### 4. Create Invitation
**Endpoint**: `POST /invitation`

**Description**: Creates a new invitation with guest list.

**Request Body**:
```json
{
  "agenda": "string",
  "date_start": "2024-01-01T10:00:00Z",
  "date_end": "2024-01-01T18:00:00Z",
  "type": "invitation",
  "area": "uuid",
  "token": "string",
  "token_expire": "2024-01-01T23:59:59Z",
  "pax": 100,
  "invite_by": "string",
  "guests": [
    {
      "id": "string",
      "email": "guest@example.com",
      "name": "Guest Name",
      "phone": "+1234567890",
      "status": "pending"
    }
  ]
}
```

**Parameters**:
- `date_start` (required): Start date and time
- `date_end` (optional): End date and time
- `agenda` (optional): Event agenda/description
- `type` (optional): Invitation type (default: "invitation")
- `area` (optional): Area UUID
- `token` (optional): Custom token
- `token_expire` (optional): Token expiration date
- `pax` (optional): Maximum number of guests
- `invite_by` (optional): Who is inviting
- `guests` (optional): Array of guest information

---

### 5. Update Invitation
**Endpoint**: `PUT /invitation`

**Description**: Updates an existing invitation.

**Request Body**:
```json
{
  "id": "uuid",
  "agenda": "string",
  "date_start": "2024-01-01T10:00:00Z",
  "date_end": "2024-01-01T18:00:00Z",
  "type": "invitation",
  "area": "uuid",
  "token": "string",
  "token_expire": "2024-01-01T23:59:59Z",
  "pax": 100,
  "invite_by": "string",
  "guests": [...]
}
```

**Parameters**: Same as Create Invitation, plus:
- `id` (required): UUID of the invitation to update

---

### 6. Get All Invitations
**Endpoint**: `GET /invitation`

**Description**: Retrieves all invitations with filtering and pagination options.

**Query Parameters**:
- `timerange` (optional): Filter by time range
  - Values: `"past"` or `"upcoming"`
- `type` (optional): Filter by invitation type
  - Values: `"invitation"`, `"invitation-private"`, `"invitation-massive"`, `"invitation-internal"`, `"invitation-promoter"`
- `search` (optional): Search term for agenda (case-insensitive partial match)
- `page` (optional): Page number (default: 1)
- `limit` (optional): Items per page (default: 100, max: 1000)

**Example**: `/invitation?timerange=upcoming&type=invitation&search=meeting&page=1&limit=50`

---

### 7. Delete Invitation
**Endpoint**: `DELETE /invitation/:id`

**Description**: Deletes an invitation by ID.

**Parameters**:
- `id` (path, required): The invitation ID (UUID)

---

### 8. Set Invitation Token
**Endpoint**: `POST /invitation/token`

**Description**: Sets or updates the token for an invitation.

**Request Body**:
```json
{
  "id": "uuid",
  "token": "string"
}
```

**Parameters**:
- `id` (required): UUID of the invitation
- `token` (required): New token value

---

### 9. Upsert Invitation
**Endpoint**: `POST /invitation/upsert`

**Description**: Creates or updates an invitation (upsert operation).

**Request Body**: Same as Create Invitation

**Query Parameters**:
- `reinvite` (optional): Whether to reinvite guests (default: false)

---

## Analytics Endpoints

### 10. Get Department Usage Statistics
**Endpoint**: `GET /invitation/usage/department`

**Description**: Retrieves department usage statistics with optional filtering.

**Query Parameters**:
- `access_type` (optional): Filter by access type
- `date_start` (optional): Start date (YYYY-MM-DD format)
- `date_end` (optional): End date (YYYY-MM-DD format)
- `search` (optional): Search term
- `groupby` (optional): Group results by field

**Example**: `/invitation/usage/department?date_start=2024-01-01&date_end=2024-01-31&access_type=single`

---

### 11. Get Department Summary Statistics
**Endpoint**: `GET /invitation/summary/department`

**Description**: Retrieves department summary statistics as a single aggregated result.

**Query Parameters**:
- `access_type` (optional): Filter by access type
- `date_start` (optional): Start date (YYYY-MM-DD format)
- `date_end` (optional): End date (YYYY-MM-DD format)
- `search` (optional): Search term

**Response**: Returns aggregated statistics with breakdown by access type (single, multiple, massive, internal, promoter).

---

## Response Format

All endpoints return responses in the following format:

```json
{
  "status": 200,
  "message": "Success",
  "data": {
    // Response data here
  },
  "errors": null,
  "logs": null
}
```

## Error Codes

- `400`: Bad Request - Invalid parameters or request format
- `401`: Unauthorized - Missing or invalid authentication
- `403`: Forbidden - Insufficient permissions
- `404`: Not Found - Resource not found
- `500`: Internal Server Error - Server-side error

## Invitation Types

- `invitation`: Standard invitation
- `invitation-private`: Private invitation
- `invitation-massive`: Mass invitation
- `invitation-internal`: Internal invitation
- `invitation-promoter`: Promoter invitation

## Date Formats

- **Input**: Supports both RFC3339 and '2006-01-02T15:04:05' formats
- **Query Parameters**: Use 'YYYY-MM-DD' format for date filters
- **Response**: Returns dates in RFC3339 format

## Pagination

For paginated endpoints:
- `page`: Page number (starts from 1)
- `limit`: Items per page (max 1000)
- Response includes pagination metadata with total pages, current page, and navigation info

## Department Context

Department-related endpoints automatically use the authenticated user's department for filtering and authorization.
