# Groups API Documentation

## Base Route

---

## Endpoints

### 1. Create Group

**POST** `/api/groups`

Creates a new group within the authenticated coach's domain.

#### Request

**Headers:**

- `Authorization`: Bearer token (required)

**Body:** `CreateGroupRequest`
| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `name` | `string` | ✅ Yes | Group name |
| `description` | `string?` | ❌ No | Group description |

#### Response

**Status:** `200 OK`

**Body:** `ApiResponse<GroupResponse>`

````json
{
  "success": true,
  "message": null,
  "data": {
    "id": 1,
    "domainId": 1,
    "name": "Morning Group",
    "description": "Morning training sessions",
    "isArchived": false,
    "createdBy": "550e8400-e29b-41d4-a716-446655440000",
    "createdAt": "2026-08-01T17:15:00Z",
    "updatedAt": "2026-08-01T17:15:00Z",
    "athleteCount": 0
  }
}

2. Get Groups (Paged)
GET /api/groups
Retrieves a paginated list of groups for the authenticated coach.
Request
Headers:
Authorization: Bearer token (required)
Query Parameters: GroupQueryParameters


| Field             | Type      | Required | Default | Description                                       |
| ----------------- | --------- | -------- | ------- | ------------------------------------------------- |
| `page`            | `int`     | ❌ No     | `1`     | Page number (inherited from PaginationRequest)    |
| `pageSize`        | `int`     | ❌ No     | `20`    | Items per page (inherited from PaginationRequest) |
| `search`          | `string?` | ❌ No     | `null`  | Search by name                                    |
| `includeArchived` | `bool`    | ❌ No     | `false` | Include archived groups                           |
| `onlyArchived`    | `bool`    | ❌ No     | `false` | Show only archived groups                         |


Response
Status: 200 OK
Body:
{
  "success": true,
  "message": null,
  "data": {
    "items": [
      {
        "id": 1,
        "domainId": 1,
        "name": "Morning Group",
        "description": "Morning training sessions",
        "isArchived": false,
        "createdBy": "550e8400-e29b-41d4-a716-446655440000",
        "createdAt": "2026-08-01T17:15:00Z",
        "updatedAt": "2026-08-01T17:15:00Z",
        "athleteCount": 5
      }
    ],
    "page": 1,
    "pageSize": 20,
    "totalCount": 1,
    "totalPages": 1,
    "hasNextPage": false,
    "hasPreviousPage": false
  }
}


3. Get Group by ID
GET /api/groups/{id}
Retrieves a specific group by its ID.
Request
Headers:
Authorization: Bearer token (required)

Response
Status: 200 OK
{
  "success": true,
  "message": null,
  "data": {
    "id": 1,
    "domainId": 1,
    "name": "Morning Group",
    "description": "Morning training sessions",
    "isArchived": false,
    "createdBy": "550e8400-e29b-41d4-a716-446655440000",
    "createdAt": "2026-08-01T17:15:00Z",
    "updatedAt": "2026-08-01T17:15:00Z",
    "athleteCount": 5
  }
}

4. Update Group
PUT /api/groups/{id}
Updates an existing group.
Request
Headers:
Authorization: Bearer token (required)

Body: UpdateGroupRequest
| Field         | Type      | Required | Description               |
| ------------- | --------- | -------- | ------------------------- |
| `name`        | `string`  | ✅ Yes    | Updated group name        |
| `description` | `string?` | ❌ No     | Updated group description |


Response
Status: 200 OK
{
  "success": true,
  "message": null,
  "data": {
    "id": 1,
    "domainId": 1,
    "name": "Updated Group Name",
    "description": "Updated description",
    "isArchived": false,
    "createdBy": "550e8400-e29b-41d4-a716-446655440000",
    "createdAt": "2026-08-01T17:15:00Z",
    "updatedAt": "2026-08-01T17:20:00Z",
    "athleteCount": 5
  }
}

5. Delete Group
DELETE /api/groups/{id}
Deletes (soft-deletes/archives) a group.

Response
Status: 200 OK

{
  "success": true,
  "message": null,
  "data": true
}

# Athlete Groups API Documentation

## Base Route


---

## Endpoints

### 1. Assign Athletes to Group
**POST** `/api/groups/{groupId}/athletes`

Assigns multiple athletes to a specific group.

#### Request
**Headers:**
- `Authorization`: Bearer token (required)

**Route Parameters:**
| Field | Type | Description |
|-------|------|-------------|
| `groupId` | `int` | Group ID |

**Body:** `AssignAthletesToGroupRequest`
| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `athleteIds` | `List<Guid>` | ✅ Yes | List of athlete IDs to assign |

#### Response
**Status:** `200 OK`

**Body:** `ApiResponse<bool>`
```json
{
  "success": true,
  "message": null,
  "data": true
}

2. Get Group Members
GET /api/groups/{groupId}/athletes
Retrieves all members of a specific group.

Response
Status: 200 OK
{
  "success": true,
  "message": null,
  "data": [
    {
      "athleteId": "550e8400-e29b-41d4-a716-446655440000",
      "fullName": "John Doe",
      "email": "john.doe@example.com",
      "profilePictureUrl": "https://cdn.example.com/profiles/john.jpg",
      "registrationStatus": 1,
      "joinedAt": "2026-08-01"
    }
  ]
}

3. Remove Athlete from Group
DELETE /api/groups/{groupId}/athletes/{athleteId}
Removes a specific athlete from a group.

Response
Status: 200 OK

{
  "success": true,
  "message": null,
  "data": true
}

4. Get Available Athletes
GET /api/groups/available-athletes
Retrieves all athletes available to be assigned to groups (not currently assigned).

response
{
  "success": true,
  "message": null,
  "data": [
    {
      "athleteId": "550e8400-e29b-41d4-a716-446655440000",
      "fullName": "Jane Smith",
    }
  ]
}

4. Get Assigned Athletes
GET /api/groups/assigned-athletes
Retrieves all athletes available to be assigned to groups (not currently assigned).

response
{
  "success": true,
  "message": null,
  "data": [
    {
      "athleteId": "550e8400-e29b-41d4-a716-446655440000",
      "fullName": "Jane Smith"
    }
  ]
}
````
