## Coach Notes

Coach-authored notes about an athlete. A coach can only edit/delete their **own** notes; any coach assigned to the athlete can view them.

### Get Athlete Notes (paginated)
`GET /api/coachnotes?athleteId={athleteId}&pageNumber=1&pageSize=10`

Requires the calling coach to be actively assigned to that athlete.

### Create Note
`POST /api/coachnotes`

```json
{
  "athleteId": "GUID",
  "content": "Athlete showed strong improvement in freestyle turn technique."
}
```

Returns `201` with the created note (`id`, `athleteId`, `authorId`, `authorName`, `content`, `createdAt`, `updatedAt`).

### Update Own Note
`PUT /api/coachnotes/{noteId}`

```json
{
  "content": "Updated note text"
}
```

Returns `200`. `updatedAt` changes, `createdAt` stays the same. Returns `403` if you're not the note's author.

### Delete Note (soft delete)
`DELETE /api/coachnotes/{noteId}`

Returns `403` if you're not the author. Deleted notes are excluded from the Get list automatically.

---

## Error Responses

| Status | Meaning |
|---|---|
| `400` | Validation error — check `errors` array in the response |
| `401` | Missing/expired/invalid token |
| `403` | Authenticated, but not allowed to do this (wrong role, not assigned, not the author, etc.) |
| `404` | Resource not found (or soft-deleted) |
| `409` | Conflict (e.g., duplicate) |

---

## Test-only Endpoints (no auth)

| Action | Endpoint |
|---|---|
| Send test email | `POST /api/test/email` |
| Confirm email | `GET /api/test/confirm-email?email=...&token=...` |

Not meant for production use — ignore unless debugging email flows.

---

## Notes for Frontend/Mobile

- **Pagination**: any endpoint with `PageNumber`/`PageSize` returns a paged wrapper — expect `data.items` (or similar), `totalCount`, `pageNumber`, `pageSize`. Confirm exact field names against a live response before building list UIs.
- **GUIDs**: `athleteId`, `coachId`, and user ids are GUIDs, not ints. `noteId`, `exerciseId`, training plan ids are ints.
- **Soft delete**: Coach Notes, Exercises, and Training Plans all use soft delete (archive/restore or `IsDeleted`) rather than hard delete — deleted/archived items just won't show up in normal list calls.
- **Realtime**: there's a SignalR hub for notifications; ask backend for the hub URL/connection details if you want live updates instead of polling `GET /api/notifications`.
- A ready-to-import Postman collection is available — ask the backend dev for `AquaMetrics_API_Collection_Merged.postman_collection.json`.

---