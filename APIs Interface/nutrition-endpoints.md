# Nutrition Endpoints

Nutrition plans are reusable meal templates that a Nutrition Specialist creates and manages. Each plan consists of one or more meals with detailed macronutrient information. Once created, plans can be assigned to individual athletes or to all athletes in a group for a specified date range.

---

## Nutrition Plans (CRUD)

### Create Nutrition Plan

```
POST /api/nutrition-plans
```

**Authentication & Authorization**
- **Authentication**: Bearer token required
- **Authorized Roles**: `NutritionSpecialist` only

**Request Body**

| Field                | Type                                  | Required | Validation Rules                                                                      |
|----------------------|---------------------------------------|----------|---------------------------------------------------------------------------------------|
| Name                 | string                                | Yes      | Cannot be empty. Maximum 150 characters.                                              |
| NutritionalObjective | string                                | No       | Optional description of the plan's goal (e.g., "Weight loss", "Muscle gain").         |
| Schedule             | string                                | No       | Optional schedule information (e.g., "Weekly plan", "Competition prep").              |
| Meals                | List<CreateNutritionPlanMealRequest>  | Yes      | Must contain at least one meal.                                                       |

**Meals Array Structure** (CreateNutritionPlanMealRequest)

| Field         | Type     | Required | Validation Rules                                                                |
|---------------|----------|----------|---------------------------------------------------------------------------------|
| MealType      | MealType | Yes      | Must be a valid enum value: `1` (Breakfast), `2` (Lunch), `3` (Dinner), `4` (Snack), `5` (PreWorkout), `6` (PostWorkout) |
| Calories      | int      | Yes      | Cannot be negative.                                                             |
| ProteinGrams  | int      | Yes      | Cannot be negative.                                                             |
| CarbGrams     | int      | Yes      | Cannot be negative.                                                             |
| FatGrams      | int      | Yes      | Cannot be negative.                                                             |
| Description   | string   | Yes      | Cannot be empty. Describes the meal content.                                    |
| DietaryNotes  | string   | No       | Optional notes about allergies, substitutions, or preparation instructions.     |

**Example Request:**

```json
{
  "name": "High Protein Training Plan",
  "nutritionalObjective": "Muscle building and recovery",
  "schedule": "Daily training days",
  "meals": [
    {
      "mealType": 1,
      "calories": 450,
      "proteinGrams": 30,
      "carbGrams": 40,
      "fatGrams": 15,
      "description": "Oatmeal with banana, whey protein, and almond butter",
      "dietaryNotes": "Can substitute almond butter with peanut butter"
    },
    {
      "mealType": 5,
      "calories": 250,
      "proteinGrams": 20,
      "carbGrams": 30,
      "fatGrams": 5,
      "description": "Pre-workout shake with banana and protein powder",
      "dietaryNotes": "Consume 30-45 minutes before training"
    }
  ]
}
```

**Success Response**
- **Status Code**: `200 OK`

**Response Body Structure**

| Field                | Type                              | Description                                    |
|----------------------|-----------------------------------|------------------------------------------------|
| success              | bool                              | Indicates if the operation succeeded           |
| message              | string                            | Human-readable success message                 |
| data                 | NutritionPlanResponse             | The created nutrition plan                     |
| statusCode           | int?                              | HTTP status code (null for success)            |
| errors               | List<string>?                     | List of error messages (null for success)      |

**NutritionPlanResponse Structure**

| Field                | Type                               | Description                                    |
|----------------------|------------------------------------|------------------------------------------------|
| Id                   | int                                | Unique identifier of the nutrition plan        |
| Name                 | string                             | Plan name                                      |
| NutritionalObjective | string?                            | Optional nutritional goal description          |
| Schedule             | string?                            | Optional schedule information                  |
| Meals                | List<NutritionPlanMealResponse>    | Array of meals in the plan                     |

**NutritionPlanMealResponse Structure**

| Field         | Type     | Description                                                     |
|---------------|----------|-----------------------------------------------------------------|
| Id            | int      | Unique identifier of the meal                                   |
| MealType      | MealType | Meal type enum value (1-6)                                      |
| Calories      | int      | Total calories                                                  |
| ProteinGrams  | int      | Protein in grams                                                |
| CarbGrams     | int      | Carbohydrates in grams                                          |
| FatGrams      | int      | Fat in grams                                                    |
| Description   | string   | Meal content description                                        |
| DietaryNotes  | string?  | Optional dietary notes                                          |

**Example Success Response:**

```json
{
  "success": true,
  "message": "Request completed successfully.",
  "data": {
    "id": 42,
    "name": "High Protein Training Plan",
    "nutritionalObjective": "Muscle building and recovery",
    "schedule": "Daily training days",
    "meals": [
      {
        "id": 101,
        "mealType": 1,
        "calories": 450,
        "proteinGrams": 30,
        "carbGrams": 40,
        "fatGrams": 15,
        "description": "Oatmeal with banana, whey protein, and almond butter",
        "dietaryNotes": "Can substitute almond butter with peanut butter"
      },
      {
        "id": 102,
        "mealType": 5,
        "calories": 250,
        "proteinGrams": 20,
        "carbGrams": 30,
        "fatGrams": 5,
        "description": "Pre-workout shake with banana and protein powder",
        "dietaryNotes": "Consume 30-45 minutes before training"
      }
    ]
  },
  "statusCode": null,
  "errors": null
}
```

**Error Responses**

| Status Code | Scenario                                              | Example Response Body                                           |
|-------------|-------------------------------------------------------|-----------------------------------------------------------------|
| 400         | Validation failure (missing required fields, etc.)    | See example below                                               |
| 401         | Missing or invalid authentication token               | Standard authentication error                                   |
| 403         | User is not a Nutrition Specialist                    | Standard authorization error                                    |

**Example 400 Error Response (Validation Failure):**

```json
{
  "success": false,
  "message": "One or more validation errors occurred.",
  "data": null,
  "statusCode": 400,
  "errors": [
    "Nutrition plan name is required.",
    "A nutrition plan must contain at least one meal.",
    "Meal description is required."
  ]
}
```

---

### List Nutrition Plans (Paged)

```
GET /api/nutrition-plans
```

**Authentication & Authorization**
- **Authentication**: Bearer token required
- **Authorized Roles**: Any authenticated user (no specific role restriction)

**Query Parameters**

| Parameter  | Type   | Required | Default | Validation Rules                                                      |
|------------|--------|----------|---------|-----------------------------------------------------------------------|
| PageNumber | int    | No       | 1       | Page number to retrieve                                               |
| PageSize   | int    | No       | 10      | Number of items per page. Maximum 50.                                 |
| Search     | string | No       | null    | Searches plan names                                                   |
| Filter     | string | No       | null    | Additional filtering criteria (implementation-specific)               |

**Example Request:**

```
GET /api/nutrition-plans?pageNumber=1&pageSize=20&search=protein
```

**Success Response**
- **Status Code**: `200 OK`

**Response Body Structure**

| Field      | Type                                        | Description                                    |
|------------|---------------------------------------------|------------------------------------------------|
| success    | bool                                        | Indicates if the operation succeeded           |
| message    | string                                      | Human-readable success message                 |
| data       | PagedResponse<NutritionPlanResponse>        | Paginated nutrition plans                      |
| statusCode | int?                                        | HTTP status code (null for success)            |
| errors     | List<string>?                               | List of error messages (null for success)      |

**PagedResponse Structure**

| Field       | Type                              | Description                                    |
|-------------|-----------------------------------|------------------------------------------------|
| Items       | List<NutritionPlanResponse>       | Array of nutrition plans for the current page  |
| PageNumber  | int                               | Current page number                            |
| PageSize    | int                               | Number of items per page                       |
| TotalCount  | int                               | Total number of items across all pages         |
| TotalPages  | int                               | Total number of pages                          |
| HasPrevious | bool                              | True if there's a previous page                |
| HasNext     | bool                              | True if there's a next page                    |

**Example Success Response:**

```json
{
  "success": true,
  "message": "Request completed successfully.",
  "data": {
    "items": [
      {
        "id": 42,
        "name": "High Protein Training Plan",
        "nutritionalObjective": "Muscle building and recovery",
        "schedule": "Daily training days",
        "meals": [
          {
            "id": 101,
            "mealType": 1,
            "calories": 450,
            "proteinGrams": 30,
            "carbGrams": 40,
            "fatGrams": 15,
            "description": "Oatmeal with banana, whey protein, and almond butter",
            "dietaryNotes": "Can substitute almond butter with peanut butter"
          }
        ]
      }
    ],
    "pageNumber": 1,
    "pageSize": 20,
    "totalCount": 15,
    "totalPages": 1,
    "hasPrevious": false,
    "hasNext": false
  },
  "statusCode": null,
  "errors": null
}
```

**Error Responses**

| Status Code | Scenario                                              | Example Response Body                                           |
|-------------|-------------------------------------------------------|-----------------------------------------------------------------|
| 401         | Missing or invalid authentication token               | Standard authentication error                                   |

---

### Get Nutrition Plan by ID

```
GET /api/nutrition-plans/{id}
```

**Authentication & Authorization**
- **Authentication**: Bearer token required
- **Authorized Roles**: Any authenticated user (no specific role restriction)

**Path Parameters**

| Name | Type | Description                                    |
|------|------|------------------------------------------------|
| id   | int  | The unique identifier of the nutrition plan    |

**Example Request:**

```
GET /api/nutrition-plans/42
```

**Success Response**
- **Status Code**: `200 OK`

**Response Body Structure**

Same structure as Create Nutrition Plan success response.

**Example Success Response:**

```json
{
  "success": true,
  "message": "Request completed successfully.",
  "data": {
    "id": 42,
    "name": "High Protein Training Plan",
    "nutritionalObjective": "Muscle building and recovery",
    "schedule": "Daily training days",
    "meals": [
      {
        "id": 101,
        "mealType": 1,
        "calories": 450,
        "proteinGrams": 30,
        "carbGrams": 40,
        "fatGrams": 15,
        "description": "Oatmeal with banana, whey protein, and almond butter",
        "dietaryNotes": "Can substitute almond butter with peanut butter"
      }
    ]
  },
  "statusCode": null,
  "errors": null
}
```

**Error Responses**

| Status Code | Scenario                                              | Example Response Body                                           |
|-------------|-------------------------------------------------------|-----------------------------------------------------------------|
| 401         | Missing or invalid authentication token               | Standard authentication error                                   |
| 404         | Nutrition plan with the specified ID does not exist   | See example below                                               |

**Example 404 Error Response:**

```json
{
  "success": false,
  "message": "Nutrition plan 42 was not found.",
  "data": null,
  "statusCode": 404,
  "errors": null
}
```

---

### Update Nutrition Plan

```
PUT /api/nutrition-plans/{id}
```

**Authentication & Authorization**
- **Authentication**: Bearer token required
- **Authorized Roles**: `NutritionSpecialist` only

**Path Parameters**

| Name | Type | Description                                    |
|------|------|------------------------------------------------|
| id   | int  | The unique identifier of the nutrition plan    |

**Request Body**

Same structure and validation rules as Create Nutrition Plan. The entire plan is replaced with the provided data (full update, not partial).

**Example Request:**

```json
{
  "name": "High Protein Training Plan - Updated",
  "nutritionalObjective": "Muscle building and recovery",
  "schedule": "Daily training and rest days",
  "meals": [
    {
      "mealType": 1,
      "calories": 500,
      "proteinGrams": 35,
      "carbGrams": 45,
      "fatGrams": 18,
      "description": "Oatmeal with banana, whey protein, and almond butter - increased portion",
      "dietaryNotes": "Can substitute almond butter with peanut butter"
    }
  ]
}
```

**Success Response**
- **Status Code**: `200 OK`

**Response Body Structure**

Same structure as Create Nutrition Plan success response, with updated values.

**Error Responses**

| Status Code | Scenario                                              | Example Response Body                                           |
|-------------|-------------------------------------------------------|-----------------------------------------------------------------|
| 400         | Validation failure (missing required fields, etc.)    | Same format as Create endpoint                                  |
| 401         | Missing or invalid authentication token               | Standard authentication error                                   |
| 403         | User is not a Nutrition Specialist                    | Standard authorization error                                    |
| 404         | Nutrition plan with the specified ID does not exist   | Same format as Get by ID endpoint                               |

---

### Delete Nutrition Plan

```
DELETE /api/nutrition-plans/{id}
```

**Authentication & Authorization**
- **Authentication**: Bearer token required
- **Authorized Roles**: `NutritionSpecialist` only

**Path Parameters**

| Name | Type | Description                                    |
|------|------|------------------------------------------------|
| id   | int  | The unique identifier of the nutrition plan    |

**Example Request:**

```
DELETE /api/nutrition-plans/42
```

**Success Response**
- **Status Code**: `200 OK`

**Response Body Structure**

| Field      | Type             | Description                                    |
|------------|------------------|------------------------------------------------|
| success    | bool             | Indicates if the operation succeeded           |
| message    | string           | Human-readable success message                 |
| data       | bool             | Returns `true` when plan is deleted            |
| statusCode | int?             | HTTP status code (null for success)            |
| errors     | List<string>?    | List of error messages (null for success)      |

**Example Success Response:**

```json
{
  "success": true,
  "message": "Request completed successfully.",
  "data": true,
  "statusCode": null,
  "errors": null
}
```

**Error Responses**

| Status Code | Scenario                                              | Example Response Body                                           |
|-------------|-------------------------------------------------------|-----------------------------------------------------------------|
| 401         | Missing or invalid authentication token               | Standard authentication error                                   |
| 403         | User is not a Nutrition Specialist                    | Standard authorization error                                    |
| 404         | Nutrition plan with the specified ID does not exist   | Same format as Get by ID endpoint                               |

---

## Nutrition Plan Assignment

### Assign Nutrition Plan to Athlete

```
POST /api/nutrition-plan-assignments/athlete
```

**Authentication & Authorization**
- **Authentication**: Bearer token required
- **Authorized Roles**: `NutritionSpecialist` only

**Request Body**

| Field            | Type     | Required | Validation Rules                                                                              |
|------------------|----------|----------|-----------------------------------------------------------------------------------------------|
| NutritionPlanId  | int      | Yes      | Must be greater than 0.                                                                       |
| AthleteId        | Guid?    | Yes*     | Required when assigning to an athlete. Must be null when GroupId is provided.                 |
| GroupId          | int?     | No       | Must be null when assigning to an individual athlete.                                         |
| StartDate        | DateOnly | Yes      | Cannot be empty. Start date of the assignment.                                                |
| EndDate          | DateOnly?| No       | Optional end date. If provided, must be after StartDate.                                      |

*Exactly one of `AthleteId` or `GroupId` must be provided (mutually exclusive).

**Example Request:**

```json
{
  "nutritionPlanId": 42,
  "athleteId": "3fa85f64-5717-4562-b3fc-2c963f66afa6",
  "groupId": null,
  "startDate": "2026-08-01",
  "endDate": "2026-08-31"
}
```

**Success Response**
- **Status Code**: `200 OK`

**Response Body Structure**

| Field      | Type                              | Description                                    |
|------------|-----------------------------------|------------------------------------------------|
| success    | bool                              | Indicates if the operation succeeded           |
| message    | string                            | Human-readable success message                 |
| data       | NutritionPlanAssignmentResponse   | The created assignment                         |
| statusCode | int?                              | HTTP status code (null for success)            |
| errors     | List<string>?                     | List of error messages (null for success)      |

**NutritionPlanAssignmentResponse Structure**

| Field              | Type     | Description                                                       |
|--------------------|----------|-------------------------------------------------------------------|
| Id                 | int      | Unique identifier of the assignment                               |
| NutritionPlanId    | int      | ID of the assigned nutrition plan                                 |
| NutritionPlanName  | string   | Name of the assigned nutrition plan                               |
| AthleteId          | Guid?    | ID of the athlete (null for group assignments shown individually) |
| GroupId            | int?     | ID of the group (populated when assigned via group endpoint)      |
| StartDate          | DateOnly | Start date of the assignment                                      |
| EndDate            | DateOnly?| Optional end date of the assignment                               |
| AssignedAt         | DateTime | UTC timestamp when the assignment was created                     |

**Example Success Response:**

```json
{
  "success": true,
  "message": "Nutrition plan assigned successfully.",
  "data": {
    "id": 201,
    "nutritionPlanId": 42,
    "nutritionPlanName": "High Protein Training Plan",
    "athleteId": "3fa85f64-5717-4562-b3fc-2c963f66afa6",
    "groupId": null,
    "startDate": "2026-08-01",
    "endDate": "2026-08-31",
    "assignedAt": "2026-08-01T14:30:00Z"
  },
  "statusCode": null,
  "errors": null
}
```

**Error Responses**

| Status Code | Scenario                                                          | Example Response Body                                           |
|-------------|-------------------------------------------------------------------|-----------------------------------------------------------------|
| 400         | Validation failure (missing AthleteId, invalid date range, etc.)  | See example below                                               |
| 400         | Athlete is not assigned to the requesting Nutrition Specialist    | See example below                                               |
| 401         | Missing or invalid authentication token                           | Standard authentication error                                   |
| 403         | User is not a Nutrition Specialist                                | Standard authorization error                                    |
| 404         | Nutrition plan does not exist                                     | See example below                                               |
| 404         | Athlete does not exist                                            | See example below                                               |
| 409         | Active assignment already exists for this athlete and date range  | See example below                                               |

**Example 400 Error Response (Validation Failure):**

```json
{
  "success": false,
  "message": "Exactly one of AthleteId or GroupId must be provided.",
  "data": null,
  "statusCode": 400,
  "errors": null
}
```

**Example 400 Error Response (Athlete Not Assigned to Coach):**

```json
{
  "success": false,
  "message": "Athlete 3fa85f64-5717-4562-b3fc-2c963f66afa6 is not assigned to this coach.",
  "data": null,
  "statusCode": 400,
  "errors": null
}
```

**Example 404 Error Response (Nutrition Plan Not Found):**

```json
{
  "success": false,
  "message": "Nutrition plan 42 was not found.",
  "data": null,
  "statusCode": 404,
  "errors": null
}
```

**Example 409 Error Response (Conflict):**

```json
{
  "success": false,
  "message": "An active assignment for this athlete and nutrition plan already exists for the provided date range.",
  "data": null,
  "statusCode": 409,
  "errors": null
}
```

---

### Assign Nutrition Plan to Group

```
POST /api/nutrition-plan-assignments/group
```

**Authentication & Authorization**
- **Authentication**: Bearer token required
- **Authorized Roles**: `NutritionSpecialist` only

**Request Body**

Same structure as Assign to Athlete, but with `GroupId` populated and `AthleteId` set to null.

| Field            | Type     | Required | Validation Rules                                                                              |
|------------------|----------|----------|-----------------------------------------------------------------------------------------------|
| NutritionPlanId  | int      | Yes      | Must be greater than 0.                                                                       |
| AthleteId        | Guid?    | No       | Must be null when assigning to a group.                                                       |
| GroupId          | int?     | Yes*     | Required when assigning to a group. Must be null when AthleteId is provided.                  |
| StartDate        | DateOnly | Yes      | Cannot be empty. Start date of the assignment.                                                |
| EndDate          | DateOnly?| No       | Optional end date. If provided, must be after StartDate.                                      |

*Exactly one of `AthleteId` or `GroupId` must be provided (mutually exclusive).

**Example Request:**

```json
{
  "nutritionPlanId": 42,
  "athleteId": null,
  "groupId": 15,
  "startDate": "2026-08-01",
  "endDate": "2026-08-31"
}
```

**Success Response**
- **Status Code**: `200 OK`

**Response Body Structure**

| Field      | Type                                     | Description                                    |
|------------|------------------------------------------|------------------------------------------------|
| success    | bool                                     | Indicates if the operation succeeded           |
| message    | string                                   | Human-readable success message                 |
| data       | List<NutritionPlanAssignmentResponse>    | Array of created assignments (one per athlete) |
| statusCode | int?                                     | HTTP status code (null for success)            |
| errors     | List<string>?                            | List of error messages (null for success)      |

**Example Success Response:**

```json
{
  "success": true,
  "message": "Nutrition plan assignments created successfully.",
  "data": [
    {
      "id": 201,
      "nutritionPlanId": 42,
      "nutritionPlanName": "High Protein Training Plan",
      "athleteId": "3fa85f64-5717-4562-b3fc-2c963f66afa6",
      "groupId": 15,
      "startDate": "2026-08-01",
      "endDate": "2026-08-31",
      "assignedAt": "2026-08-01T14:30:00Z"
    },
    {
      "id": 202,
      "nutritionPlanId": 42,
      "nutritionPlanName": "High Protein Training Plan",
      "athleteId": "7b9f2c3d-8e4a-4f1b-9a5d-6c8e3b2a1f4e",
      "groupId": 15,
      "startDate": "2026-08-01",
      "endDate": "2026-08-31",
      "assignedAt": "2026-08-01T14:30:00Z"
    }
  ],
  "statusCode": null,
  "errors": null
}
```

**Error Responses**

Same error scenarios as Assign to Athlete, plus:

| Status Code | Scenario                                              | Example Response Body                                           |
|-------------|-------------------------------------------------------|-----------------------------------------------------------------|
| 404         | Group does not exist or doesn't belong to the coach   | See example below                                               |

**Example 404 Error Response (Group Not Found):**

```json
{
  "success": false,
  "message": "Group 15 was not found.",
  "data": null,
  "statusCode": 404,
  "errors": null
}
```

**Important Behavior Notes:**

1. **Bulk Individual Assignments**: This endpoint does NOT create a single group-level assignment. Instead, it creates one individual assignment per active (non-archived) athlete currently in the group. Each assignment record includes the `GroupId` to indicate it was assigned via a group operation.

2. **Partial Success**: If some athletes in the group already have a conflicting assignment for the same nutrition plan and date range, those athletes are **silently skipped** and the operation succeeds for the remaining athletes. The response will only include assignments that were successfully created. This is a **partial success** model, not an atomic all-or-nothing transaction.

3. **Active Members Only**: Only athletes with non-archived membership in the group receive the assignment. Archived/removed members are excluded.

---

### Get Assignments by Nutrition Plan

```
GET /api/nutrition-plan-assignments/plan/{nutritionPlanId}
```

**Authentication & Authorization**
- **Authentication**: Bearer token required
- **Authorized Roles**: `NutritionSpecialist` only

**Path Parameters**

| Name            | Type | Description                                    |
|-----------------|------|------------------------------------------------|
| nutritionPlanId | int  | The unique identifier of the nutrition plan    |

**Example Request:**

```
GET /api/nutrition-plan-assignments/plan/42
```

**Success Response**
- **Status Code**: `200 OK`

**Response Body Structure**

| Field      | Type                                     | Description                                    |
|------------|------------------------------------------|------------------------------------------------|
| success    | bool                                     | Indicates if the operation succeeded           |
| message    | string                                   | Human-readable success message                 |
| data       | List<NutritionPlanAssignmentResponse>    | Array of all assignments for this plan         |
| statusCode | int?                                     | HTTP status code (null for success)            |
| errors     | List<string>?                            | List of error messages (null for success)      |

**Example Success Response:**

```json
{
  "success": true,
  "message": "Request completed successfully.",
  "data": [
    {
      "id": 201,
      "nutritionPlanId": 42,
      "nutritionPlanName": "High Protein Training Plan",
      "athleteId": "3fa85f64-5717-4562-b3fc-2c963f66afa6",
      "groupId": null,
      "startDate": "2026-08-01",
      "endDate": "2026-08-31",
      "assignedAt": "2026-08-01T14:30:00Z"
    },
    {
      "id": 202,
      "nutritionPlanId": 42,
      "nutritionPlanName": "High Protein Training Plan",
      "athleteId": "7b9f2c3d-8e4a-4f1b-9a5d-6c8e3b2a1f4e",
      "groupId": 15,
      "startDate": "2026-08-01",
      "endDate": "2026-08-31",
      "assignedAt": "2026-08-01T14:30:00Z"
    }
  ],
  "statusCode": null,
  "errors": null
}
```

**Error Responses**

| Status Code | Scenario                                              | Example Response Body                                           |
|-------------|-------------------------------------------------------|-----------------------------------------------------------------|
| 401         | Missing or invalid authentication token               | Standard authentication error                                   |
| 403         | User is not a Nutrition Specialist                    | Standard authorization error                                    |
| 404         | Nutrition plan does not exist                         | Same format as Create Assignment endpoint                       |

---

## Notes for Frontend

### Business Rules

1. **Meal Requirement**: A nutrition plan must contain at least one meal. The UI should prevent creating or updating a plan with an empty meals array.

2. **Name Length**: Plan names are limited to 150 characters. Consider displaying a character counter in the form.

3. **Macronutrient Validation**: All macronutrient values (calories, protein, carbs, fat) must be non-negative integers. The UI should prevent entering negative numbers.

4. **MealType Enum Values**: When displaying meal types, use the numeric values (1-6) in API requests. For display purposes:
   - `1` = Breakfast
   - `2` = Lunch
   - `3` = Dinner
   - `4` = Snack
   - `5` = Pre-Workout
   - `6` = Post-Workout

5. **Date Range Validation**: When assigning a plan, if an `EndDate` is provided, it must be after the `StartDate`. Consider using a date range picker that enforces this constraint.

6. **Mutually Exclusive Assignment Targets**: When building the assignment form, ensure only one of `AthleteId` or `GroupId` is populated. Consider using radio buttons or tabs to switch between "Assign to Athlete" and "Assign to Group" modes.

7. **Group Assignment Behavior**: When assigning to a group, the frontend should communicate to the user that:
   - Individual assignments will be created for each active member
   - Athletes who already have a conflicting assignment will be skipped
   - The response will show which athletes received the assignment

8. **Pagination Limits**: The maximum page size is 50 items. If the user requests more, the API will cap it at 50.

9. **Domain Scoping**: All operations are automatically scoped to the authenticated Nutrition Specialist's domain. A specialist can only view and manage their own domain's nutrition plans and assignments.

10. **Full Update Semantics**: The update endpoint replaces the entire nutrition plan (including all meals). When building an edit form, pre-populate all existing data and send the complete updated object. Consider warning users that updating will replace all meals.

### Implementation Tips

- Cache the list of nutrition plans in the assignment form to avoid repeated lookups.
- Consider implementing a meal template library in the frontend to speed up plan creation.
- Display total daily macronutrients (sum of all meals) in the plan view for quick reference.
- When displaying assignments by plan, group them by athlete or group to improve readability.
- Consider showing a confirmation dialog before deleting a nutrition plan, especially if it has active assignments.
- For group assignments, consider showing a preview of which athletes will receive the plan before confirming.
