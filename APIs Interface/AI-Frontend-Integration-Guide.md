# AI — Frontend Integration Guide (Angular)

> **Audience:** Angular developers. You did **not** build this backend. You just need to know what screens to build, what endpoints to call, and what to do with the responses.
>
> **Base URL:** `https://<api-host>/api` — everything below is relative to this. All routes referenced with the `/api` prefix, e.g. `POST /api/ai/chat/sessions`.
>
> **JSON convention:** all request and response bodies are `camelCase`.

---

## Table of Contents

1. [Overview & Role Matrix](#1-overview--role-matrix)
2. [Authentication & Headers](#2-authentication--headers)
3. [AI Chat Feature](#3-ai-chat-feature-coachspecialist-only)
4. [AI Plan Generation Flow](#4-ai-plan-generation-flow-coachspecialist-only)
5. [Athlete's Current Plan View](#5-athletes-current-plan-view-all-roles-including-athlete)
6. [Enums & Status Values Reference](#6-enums--status-values-reference)
7. [Error Handling Reference](#7-error-handling-reference)
8. [Angular Implementation Notes](#8-angular-implementation-notes)

---

## 1. Overview & Role Matrix

The AI feature set has **four independent capabilities**: AI Chat, AI Plan Generation, AI Recommendation review, and read-only access to an athlete's current plan.

### Role Matrix

| Role              | AI Chat | Generate AI Plan Draft | Review Recommendation | View athlete's current plan |
|-------------------|---------|------------------------|------------------------|------------------------------|
| **SwimmingCoach** | ✅ (domain 1) | ✅ Training plans (domain 1) | ✅ (domain 1) | ✅ `/plans/training` — ❌ `/plans/nutrition` (403) |
| **FitnessCoach**  | ✅ (domain 2) | ✅ Training plans (domain 2) | ✅ (domain 2) | ✅ `/plans/training` — ❌ `/plans/nutrition` (403) |
| **NutritionSpecialist** | ✅ (domain 3) | ✅ Nutrition plans (domain 3) | ✅ (domain 3) | ✅ `/plans/nutrition` — ❌ `/plans/training` (403) |
| **Athlete**       | ❌ | ❌ | ❌ | ✅ Read **only** their **own** plans — both `/plans/training` and `/plans/nutrition`, plus read-only view of their own recommendations (`GET /api/ai-recommendations/my`) |
| **Admin**         | ❌ 403 | ❌ 403 | ❌ 403 | ❌ No AI endpoints at all |

Key rules that drive which screens you build:

- A **coach/specialist only ever operates inside the single domain bound to their role** (Swimming = 1, Fitness = 2, Nutrition = 3). The server enforces this on every AI call — do not build UI that lets a user pick a domain different from their own.
- An **Athlete** never writes anything AI-related. They only *view* their own current plans and their own recommendation history. There is no athlete-facing AI chat.
- **Admin** has zero AI functionality. Hide all AI navigation for the `Admin` role.
- There is **no "view all coaches's sessions / recommendations" screen**. Coaches can only ever see their **own** sessions, and recommendations are always scoped to athletes they are actively assigned to.

### Screens to build

| Role | Screens |
|------|---------|
| SwimmingCoach / FitnessCoach / NutritionSpecialist | AI Chat screen (general + athlete-scoped), AI Plan Draft generator (with approve/reject review), Recommendation inbox, athlete profile that shows the current effective plan (`training` for coaches, `nutrition` for specialists) |
| Athlete | Profile/dashboard showing their own current training + nutrition plans and their own recommendation history |
| Admin | None |

---

## 2. Authentication & Headers

### Getting the token

`POST /api/Auth/login` with JSON body:

```json
{ "email": "coach@aquametrics.io", "password": "secret" }
```

Response (`200 OK`) — the `data` payload:

```json
{
  "accessToken": "eyJhbGciOi...",
  "refreshToken": "d4f9...",
  "expiration": "2026-08-19T14:30:00Z",
  "userId": "a1b2c3d4-...",
  "fullName": "Sarah Coach",
  "email": "coach@aquametrics.io",
  "roles": ["SwimmingCoach"]
}
```

- Store `accessToken` in memory + `refreshToken` in a safe place (e.g. `localStorage`), and attach `Authorization: Bearer <accessToken>` to **every** AI request.
- To refresh: `POST /api/Auth/refresh` with `{ "refreshToken": "..." }` — response shape is the same `LoginResponseDto`.
- On any `401`, call refresh once; if that fails, redirect to the login route.

The JWT also carries a `role` claim and — for coach/specialist roles only — a `domainId` claim (`1 | 2 | 3`). The frontend never needs to read `domainId` from the token for AI calls; you only need it if you want to localize labels, and it is always derived server-side.

### The domain is never chosen by the client

The backend resolves the *identity* and the *domain authority* from the JWT, not from the client. This means:

- **Chat** — the session's `domainId` is taken entirely from your JWT. The request body has **no** `domainId` field at all. Do not invent one.
- **Read endpoints** (session lists, recommendation lists, current plans) — any `domainId` is optional and, when supplied, is verified against your JWT's domain. You can simply omit it and the server uses your own domain.
- **Generation endpoints** (`POST /api/ai-recommendations/generate-plan` and `/generate`) are the one exception: they *do* take a `domainId` in the body because they create an artifact in a specific domain. However, that value must **always match your own role's domain** — the server rejects a mismatch. A `SwimmingCoach` can only ever send `domainId: 1`.

> **Rule of thumb for the Angular app:** never let the user pick or send an arbitrary `domainId`. Hardcode it per feature from the logged-in role (Swimming → 1, Fitness → 2, Nutrition → 3) or derive it from the `roles` array in the login response. When in doubt, omit it and let the server use your domain.

### `Idempotency-Key` header (required for plan generation)

`POST /api/ai-recommendations/generate-plan` triggers a slow LLM round-trip. If the user double-clicks, or the network drops and the client retries, the backend could generate **two** plan drafts and burn a second LLM call. The `Idempotency-Key` header exists to make one logical "generate" request exactly-once:

- **What it is:** a UUID that the Angular app mints client-side, one per logical submit.
- **Where it goes:** `Idempotency-Key: <uuid>` — a request **header**, not a body field.
- **How to generate it:** `crypto.randomUUID()` (browser-built-in, no library needed).
- **Lifecycle in the component:**
  1. User clicks *Generate* → create `const key = crypto.randomUUID()`, store in component state, send it as the header.
  2. While the request is in flight (or after a *network* failure from which you want to retry the *same* logical request), keep the **same** key — retrying with the same key + same body returns the stored result instead of re-running the LLM.
  3. Once you receive a `success: true` response, **discard the key** — the next Generate click mints a brand-new UUID.

> Why reset after success: re-using a completed key replays the *old* cached response instead of generating again, so a page refresh / second submit must produce a new key or the user would see a stale plan.

### The `ApiResponse<T>` envelope — every endpoint returns it

Every response body is wrapped in the same envelope. Your HTTP interceptor should unwrap it once so all services deal with `data`.

```ts
interface ApiResponse<T> {
  success: boolean;          // true = happy path
  message: string;           // human-readable summary
  data: T | null;            // the payload — ALWAYS read from here
  statusCode: number | null; // 200 on success; on failure often null (the real code is the HTTP status)
  errors: string[] | null;   // ALWAYS read from here for validation failures (400 with errors[])
}
```

- On success: `success: true`, `data` populated.
- On failure: the HTTP status code carries the real code, `success: false`, `message` carries a summary, and `errors[]` (when present) carries individual field/message errors. Never parse `message` for data.

---

## 3. AI Chat Feature (Coach/Specialist only)

Two flows share the exact same endpoints:

- **Flow A — General chat.** Create a session with **no** `athleteId`. The assistant answers from general domain knowledge (RAG over knowledge documents only).
- **Flow B — Personalized chat.** Create a session **with** `athleteId`. The backend automatically injects that athlete's current plan, metrics and performance data into every message — **the Angular app just passes `athleteId` and does NOT assemble any context**. The UI can label these sessions "Chat about <athlete name>".

Endpoints:

| Method | URL | Purpose |
|--------|-----|---------|
| `POST` | `/api/ai/chat/sessions` | Create a session |
| `GET`  | `/api/ai/chat/sessions?pageNumber=1&pageSize=10` | List **your** sessions, newest first |
| `GET`  | `/api/ai/chat/sessions/{sessionId}` | Get one session |
| `POST` | `/api/ai/chat/sessions/{sessionId}/messages` | Send a message, get the AI reply |
| `GET`  | `/api/ai/chat/sessions/{sessionId}/messages` | Load message history (chronological) |

**Session ownership rule:** a coach/specialist can only read/reply to **their own** sessions. Attempting to read another coach's session returns `404` (it is as if it does not exist). Do **not** build any "browse all coaches' sessions" screen.

### 3.1 Create a session

`POST /api/ai/chat/sessions`

Request body:

```json
{
  "athleteId": "3fa85f64-5717-4562-b3fc-2c963f66afa6",   // optional — Flow B (personalized). Omit for Flow A (general).
  "title": "Pre-competition taper questions"              // optional — max 200 chars
}
```

```ts
interface CreateChatSessionRequest {
  athleteId?: string;   // UUID — only for personalized chat
  title?: string;
}
```

Response `200` — `data`:

```json
{
  "id": 42,
  "coachId": "a1b2c3d4-e5f6-7890-abcd-ef1234567890",
  "domainId": 1,
  "athleteId": "3fa85f64-5717-4562-b3fc-2c963f66afa6",
  "athleteName": "Liam Parker",
  "title": "Pre-competition taper questions",
  "createdAt": "2026-08-19T10:00:00Z",
  "updatedAt": null,
  "messageCount": 0
}
```

```ts
interface ChatSessionResponse {
  id: number;
  coachId: string;          // your own user id — always matches the authenticated coach
  domainId: number;         // 1 | 2 | 3 — your role's domain
  athleteId: string | null; // null for a general session
  athleteName: string | null;
  title: string | null;
  createdAt: string;        // ISO 8601
  updatedAt: string | null;
  messageCount: number;
}
```

> **Carry-forward:** keep `session.id` — every subsequent message call in this conversation uses it.

**UI:** on mobile-style chat you may auto-name the session from the first message; simpler, display `title` (or fall back to `athleteName` / "General chat"). Show a badge when `athleteId` is set (e.g. "Personalized · Liam Parker").

### 3.2 Send a message

`POST /api/ai/chat/sessions/{sessionId}/messages`

Request body:

```json
{ "message": "Build a 4-week taper leading into nationals." }
```

```ts
interface SendChatMessageRequest {
  message: string; // required
}
```

Response `200` — `data` (returns **both** persisted turns in one call):

```json
{
  "session": { "id": 42, "coachId": "a1b2c3d4-...", "domainId": 1, "athleteId": "3fa85f64-...", "athleteName": "Liam Parker", "title": "Pre-competition taper questions", "createdAt": "2026-08-19T10:00:00Z", "updatedAt": "2026-08-19T10:00:05Z", "messageCount": 2 },
  "userMessage": {
    "id": 127, "chatSessionId": 42, "role": "user", "content": "Build a 4-week taper leading into nationals.", "evidence": [], "createdAt": "2026-08-19T10:00:01Z"
  },
  "assistantMessage": {
    "id": 128,
    "chatSessionId": 42,
    "role": "assistant",
    "content": "Here is a 4-week taper plan for Liam...",
    "evidence": [
      {
        "documentId": 8,
        "chunkId": "01d5c6b2-...",
        "content": "Tapering strategies for sprint swimmers...",
        "score": 0.91,
        "metadata": { "title": "Swim Taper Handbook" }
      }
    ],
    "createdAt": "2026-08-19T10:00:04Z"
  }
}
```

```ts
interface ChatReplyResponse {
  session: ChatSessionResponse;
  userMessage: ChatMessageResponse;
  assistantMessage: ChatMessageResponse;
}

interface ChatMessageResponse {
  id: number;
  chatSessionId: number;
  role: 'user' | 'assistant';     // string on the wire
  content: string;                // the visible text
  evidence: RetrievedDocument[];  // citations — only on assistant messages, may be empty
  createdAt: string;
}

interface RetrievedDocument {
  documentId: number;
  chunkId: string;
  content: string;      // snippet text — usable as a citation chip label
  score: number;        // 0..1 relevance
  metadata: Record<string, string | null>; // e.g. { title, domainId, ... }
}
```

**UI instructions:**

- Render `assistantMessage.content` as the AI reply — this is the canonical message text.
- Render `userMessage.content` as the bubble the user just typed.
- `evidence[]` is **optional** garnish: when non-empty, render small citation chips under the assistant bubble, using `evidence[].metadata.title` (fall back to a snippet of `content`). Clicking a chip is out of scope — just show the count/source. `evidence` on a **user** message is always empty; only render chips on `assistant` messages.
- **Do not re-fetch history after sending.** Append `userMessage` then `assistantMessage` to your local in-memory conversation array *immediately* and re-render (see [Section 8](#8-angular-implementation-notes)).

### 3.3 List sessions

`GET /api/ai/chat/sessions?pageNumber=1&pageSize=10`

Response `200` — `data` is a paginated list:

```json
{
  "items": [
    { "id": 42, "coachId": "a1b2c3d4-...", "domainId": 1, "athleteId": "3fa85f64-...", "athleteName": "Liam Parker", "title": "Pre-competition taper questions", "createdAt": "2026-08-19T10:00:00Z", "updatedAt": "2026-08-19T10:00:05Z", "messageCount": 2 },
    { "id": 37, "coachId": "a1b2c3d4-...", "domainId": 1, "athleteId": null, "athleteName": null, "title": "General technique Q&A", "createdAt": "2026-08-18T09:00:00Z", "updatedAt": null, "messageCount": 5 }
  ],
  "pageNumber": 1,
  "pageSize": 10,
  "totalCount": 2,
  "totalPages": 1,
  "hasPrevious": false,
  "hasNext": false
}
```

```ts
interface PagedResponse<T> {
  items: T[];
  pageNumber: number;
  pageSize: number;
  totalCount: number;
  totalPages: number;
  hasPrevious: boolean;
  hasNext: boolean;
}
```

**UI:** build the conversation sidebar from this list. Show `title ?? athleteName ?? 'Chat'`, a "Personalized" badge when `athleteId` is set, and `messageCount`. Only **your** sessions ever appear.

### 3.4 Load conversation history

`GET /api/ai/chat/sessions/{sessionId}/messages`

Response `200` — `data` is a flat **array**, chronological (oldest → newest):

```json
[
  { "id": 1, "chatSessionId": 42, "role": "user", "content": "Build a 4-week taper...", "evidence": [], "createdAt": "..." },
  { "id": 2, "chatSessionId": 42, "role": "assistant", "content": "Here is a 4-week taper plan...", "evidence": [/* ... */], "createdAt": "..." }
]
```

**UI:** render these top-down as the existing conversation when a session is opened. Implementation is identical to rendering the incremental reply in [3.2](#32-send-a-message).

---

## 4. AI Plan Generation Flow (Coach/Specialist only)

This is the most important flow. Read it end to end — the UI has **four distinct steps** and a lot of correctness depends on carrying IDs between them.

```
 Step 1                     Step 2                     Step 3                    Step 4
 Generate draft  ─────────▶ View pending  ───────────▶ Approve / Reject  ──────▶ Read current plan
 POST .../generate-plan     GET .../{id}                 POST .../{id}/review     GET /api/athletes/{id}/plans/...
 + Idempotency-Key          (review screen)              decision: 1 | 2           (confirm it is active)
```

State/IDs to carry forward in this flow (keep in a service/route store):

| Carry forward | From | Used in |
|---------------|------|---------|
| `athleteId` | the athlete you selected | Steps 1, 2, 3, 4 |
| `domainId` | your role (1/2/3) | Step 1 body |
| `recommendationId` | Step 1 response | Steps 2 & 3 (URL path) |
| `Idempotency-Key` | generated per submit | Step 1 header only |

### Step 1 — Generate a draft plan

`POST /api/ai-recommendations/generate-plan`

**Required header:** `Idempotency-Key: <uuid>`

```http
Authorization: Bearer <token>
Idempotency-Key: 9f2b0c3a-5d6e-4f7a-8b9c-1d2e3f4a5b6c
Content-Type: application/json
```

Request body:

```json
{
  "athleteId": "3fa85f64-5717-4562-b3fc-2c963f66afa6",
  "domainId": 1,                // 1=Swimming, 2=Fitness, 3=Nutrition — MUST match your role
  "query": "4-week taper for a 100m freestyle sprinter"
}
```

```ts
interface GenerateAiPlanRequest {
  athleteId: string;  // UUID
  domainId: number;   // your role's domain only
  query: string;      // natural-language coaching request
}
```

Response `200` — `data`:

```json
{
  "recommendationId": 88,
  "trainingPlanId": 104,        // set for domain 1/2; nutrition generates null here
  "nutritionPlanId": null,      // set for domain 3; training generates null here
  "assignmentId": 512,
  "athleteId": "3fa85f64-5717-4562-b3fc-2c963f66afa6",
  "domainId": 1,
  "planTitle": "4-Week Taper to Nationals",
  "recommendation": "A progressive 4-week taper that reduces volume...",
  "status": 1,                  // RecommendationStatus.Pending
  "assignmentStatus": "Assigned",
  "isActive": false,            // the plan is NOT active yet
  "generatedAt": "2026-08-19T11:00:00Z",
  "lineCount": 12,
  "missingExerciseNotes": null  // string | null — see warning below
}
```

```ts
interface AiPlanResponse {
  recommendationId: number;
  trainingPlanId: number | null;   // only when domainId is 1 or 2
  nutritionPlanId: number | null;  // only when domainId is 3
  assignmentId: number;
  athleteId: string;
  domainId: number;
  planTitle: string;
  recommendation: string;
  status: number;                  // 1 = Pending
  assignmentStatus: string;        // "Assigned" (draft assignment, not yet active)
  isActive: boolean;               // always false right after generation
  generatedAt: string;
  lineCount: number;
  missingExerciseNotes: string | null;
}
```

**UI state after this call — a "Plan Draft Generated — Pending Review" state:**

- The plan is a **draft**. It is **NOT** the athlete's active plan yet.
- Show a prominent banner/card: *"Plan draft generated — awaiting your review."* Do NOT show it as the athlete's plan.
- Show `planTitle` and `recommendation` as the draft summary.
- **`missingExerciseNotes` is a warning signal:** when it is non-null, the AI could not find a perfect exercise match in the catalog for some part of the request. Always render it as a **yellow warning banner** — never silently discard it.
- Store `recommendationId` and navigate to the review screen (Step 2).

> **Performance:** generation is a full LLM round-trip and can take **up to 120 seconds**. Show a full-screen spinner, **disable** the Generate button the entire time, and set a generous HTTP timeout (≥ 120 s). Do not allow double-clicks — the `Idempotency-Key` protects against duplicates, but a clean UI should prevent the attempt anyway.

### Step 2 — View the pending recommendation (review screen)

`GET /api/ai-recommendations/{recommendationId}`

Response `200` — `data`:

```json
{
  "id": 88,
  "athleteId": "3fa85f64-5717-4562-b3fc-2c963f66afa6",
  "domainId": 1,
  "recommendation": "A progressive 4-week taper that reduces volume...",
  "rationale": "Based on Liam's current 200m PB and 3-targeted rebuilds...",
  "status": 1,                          // 1 = Pending, 2 = Approved, 3 = Rejected
  "generatedAt": "2026-08-19T11:00:00Z",
  "requestedById": "a1b2c3d4-...",
  "evidence": [
    { "documentId": 8, "documentTitle": "Swim Taper Handbook", "chunkId": "01d5c6b2-...", "score": 0.91 }
  ]
}
```

```ts
interface AiRecommendationResponse {
  id: number;
  athleteId: string;
  domainId: number;
  recommendation: string;   // the plan summary
  rationale: string;        // why the AI proposed this
  status: number;           // RecommendationStatus — see the approve/reject logic
  generatedAt: string;
  requestedById: string;
  evidence: RecommendationEvidenceResponse[]; // optional RAG source list
}

interface RecommendationEvidenceResponse {
  documentId: number;
  documentTitle: string;
  chunkId: string;
  score: number;
}
```

**UI:** this is the review screen. Show `recommendation`, `rationale`, and optionally `evidence[]` as "Sources" chips. The **Approve / Reject buttons are only rendered when `status === 1` (Pending)**.

Optional (but recommended for the coach's daily workflow) — the recommendation inbox:

- `GET /api/ai-recommendations?domainId=1&pageNumber=1&pageSize=10` — pending + history across all your assigned athletes.
- `GET /api/ai-recommendations/athlete/{athleteId}?pageNumber=1&pageSize=10` — a single athlete's recommendation history.
- Each list item: `{ id, athleteId, athleteName, domainId, recommendation, status, generatedAt }`. The athlete can also `GET /api/ai-recommendations/my?pageNumber=1&pageSize=10` to see their own history (read-only).

### Step 3 — Approve or Reject

`POST /api/ai-recommendations/{recommendationId}/review`

Request body:

```json
{ "decision": 1, "comments": "Looks good, but reduce dryland to 2x/week." }
```

```ts
interface RecommendationReviewRequest {
  decision: 1 | 2;        // 1 = Approved, 2 = Rejected — the ONLY valid values
  comments?: string;      // optional, max 2000 chars
}
```

Response `200` — `data` is the updated `AiRecommendationResponse`:

- On decision `1` (Approved): `status` becomes `2`. The draft becomes the athlete's **active plan**.
- On decision `2` (Rejected): `status` becomes `3`. The draft is stamped Rejected; the athlete's **previous plan stays active**.

**UI:** show the appropriate result state:

| Decision | Message to show |
|----------|-----------------|
| Approved | Green success: *"Plan approved — this is now the athlete's active plan."* |
| Rejected | Neutral/grey: *"Plan rejected — the previous plan remains active."* |

**Edge case — already decided:** if the recommendation is *already* Approved or Rejected, the API returns `400 Bad Request` (`"AI recommendation 88 is already Approved and cannot be reviewed."`). Because of this, your UI must **disable the Approve/Reject buttons once a decision has been made** (i.e., `status !== 1`). Coming back to a review screen for an already-decided recommendation should render a read-only "Decision" chip instead of buttons.

### Step 4 — Read the athlete's current effective plan (after approval)

After a successful approval the source of truth for "what is this athlete currently on" is:

- `GET /api/athletes/{id}/plans/training`
- `GET /api/athletes/{id}/plans/nutrition`

These return the same `AthleteCurrentPlanDto?` shape documented in [Section 5](#5-athletes-current-plan-view-all-roles-including-athlete). Refresh the athlete's plan panel with the latest result and confirm `isActive: true` and `approvalStatus: 2`. This is where you show the **"AI Personalized"** badge (`isAiGenerated: true`).

---

## 5. Athlete's Current Plan View (All roles including Athlete)

Two read-only endpoints power the athlete-facing profile/dashboard and the post-approval confirmation:

| Method | URL | Domain |
|--------|-----|--------|
| `GET` | `/api/athletes/{athleteId}/plans/training` | Swimming / Fitness |
| `GET` | `/api/athletes/{athleteId}/plans/nutrition` | Nutrition |

Response `200` — `data` may be **`null`** (that is a valid success state, see below):

```json
{
  "assignmentId": 512,
  "planId": 104,
  "domainId": 1,
  "title": "4-Week Taper to Nationals",
  "objectives": "Peak for nationals...",
  "description": "Progressive taper...",
  "dailyCalories": null,
  "proteinGrams": null,
  "carbGrams": null,
  "fatGrams": null,
  "startDate": "2026-08-19",
  "endDate": null,
  "isActive": true,
  "approvalStatus": 2,          // 1=Draft, 2=Approved, 3=Rejected
  "source": 2,                  // 1=Manual, 2=AiAssisted
  "isAiGenerated": true,        // derived = source === 2
  "overrideOfAssignmentId": 499 // nullable — see lineage note
}
```

```ts
interface AthleteCurrentPlanDto {
  assignmentId: number;
  planId: number;
  domainId: number;
  title: string;
  objectives: string | null;
  description: string | null;
  dailyCalories: number | null;   // nutrition plans only
  proteinGrams: number | null;
  carbGrams: number | null;
  fatGrams: number | null;
  startDate: string | null;       // "YYYY-MM-DD"
  endDate: string | null;
  isActive: boolean;
  approvalStatus: number;         // ApprovalStatus
  source: number;                 // PlanSource
  isAiGenerated: boolean;         // computed by the API, just display it
  overrideOfAssignmentId: number | null;
}
```

### Role access rules

| Caller | `/plans/training` | `/plans/nutrition` |
|--------|-------------------|--------------------|
| SwimmingCoach | ✅ (own swimming domain) | ❌ 403 |
| FitnessCoach | ✅ (own fitness domain) | ❌ 403 |
| NutritionSpecialist | ❌ 403 | ✅ |
| Athlete | ✅ their own only | ✅ their own only |
| Admin | ❌ no access to AI routes | ❌ |

- **Coaches/specialists** must also have an **active assignment** to the athlete to read their plans — otherwise `403`.
- **Athletes** can only read their **own** plans; the server resolves their identity from the token, never from a client-supplied id.

### What to render

- **Plan title** (`title`) — always show.
- **"AI Personalized" badge** — show when `isAiGenerated === true`. This distinguishes AI-assisted plans (`source: 2`) from manually authored plans (`source: 1`).
- **Approval status chip** — from `approvalStatus`: `1` → *Draft*, `2` → *Approved*, `3` → *Rejected*.
- **Source note** — from `source`: `1` → *Manual*, `2` → *AI-assisted*.
- **Lineage / override note** — when `overrideOfAssignmentId` is non-null, this plan replaced another assignment; optionally render *"Personalized override of group plan"*. When null, the plan is either manual or the base plan with no override lineage.
- **Nutrition-specific metrics** — when `domainId === 3`, show `dailyCalories`, `proteinGrams`, `carbGrams`, `fatGrams`, `startDate`, `endDate`.

### When `data` is `null`

`data: null` (with `success: true`) is the **"no plan"** state. Render a friendly empty state:

> **"No active plan assigned."**

Do not treat it as an error — the HTTP status is still 200.

---

## 6. Enums & Status Values Reference

These are the integer values you will encounter on the wire. Define them as `const`/`enum` in the frontend so comparisons are readable:

```ts
export enum ApprovalStatus { Draft = 1, Approved = 2, Rejected = 3 }
export enum PlanSource { Manual = 1, AiAssisted = 2 }
export enum RecommendationStatus { Pending = 1, Approved = 2, Rejected = 3 }
export enum RecommendationDecision { Approved = 1, Rejected = 2 }
export enum DomainId { Swimming = 1, Fitness = 2, Nutrition = 3 }
export enum ChatMessageRole { User = 1, Assistant = 2 }
```

| Enum | Value | Meaning | Where you see it |
|------|-------|---------|------------------|
| **ApprovalStatus** | `1` | Draft (pending human approval) | `AthleteCurrentPlanDto.approvalStatus`, plan entities |
| | `2` | Approved (the plan is in effect) | "
| | `3` | Rejected | "
| **PlanSource** | `1` | Manual (created by a human) | `AthleteCurrentPlanDto.source` |
| | `2` | AiAssisted (produced by the AI generation flow) | "
| **RecommendationStatus** | `1` | Pending — review buttons enabled | `AiPlanResponse.status`, `AiRecommendationResponse.status`, list items |
| | `2` | Approved — buttons disabled | "
| | `3` | Rejected — buttons disabled | "
| **RecommendationDecision** | `1` | Approved | Only ever as the `decision` value sent to the review endpoint |
| | `2` | Rejected | "
| **DomainId** | `1` | Swimming | `generate-plan` body, response `domainId`, session `domainId`, plan `domainId` |
| | `2` | Fitness | "
| | `3` | Nutrition | "
| **ChatMessageRole** | `1` / `2` | Internal enum (User / Assistant) — **not what you see on the wire** | — |
| | `"user"` / `"assistant"` | **Wire format** of `ChatMessageResponse.role` (a string) | Every chat message |

> **Watch out — two gotchas:**
> 1. `RecommendationDecision` also has an internal value `3 = NeedsModification`, but it is **NOT accepted** by the review endpoint. Only `1` (Approved) and `2` (Rejected) are valid. Never send `3`.
> 2. `ChatMessageResponse.role` is a **string** (`"user"` / `"assistant"`) on the wire, even though the internal enum is `1`/`2`. Use the string values for all UI comparisons.

---

## 7. Error Handling Reference

The API uses standard HTTP status codes plus the `ApiResponse` envelope. Wire these up in an Angular HTTP interceptor so every feature gets consistent behaviour. `data` is always `null` on errors — read `errors[]` (validation) or `message`.

| Status | Condition / message | Frontend action |
|--------|---------------------|-----------------|
| **400** with `errors[]` | Field-level validation failure (e.g. empty `query`, bad `decision`, invalid `domainId`) | Render `errors[]` under the offending form fields (`errors` is an array of strings — show each). |
| **400** | *"AI recommendation {id} is already Approved and cannot be reviewed."* | This is the "already decided" case. **Disable Approve/Reject buttons** and show the current decision chip as read-only. |
| **400** | *"Idempotency-Key header is required."* / *"Idempotency-Key cannot be empty."* | Frontend bug — you always send the header. Log `console.error`, and if it ever occurs, fix the interceptor; never work around it by omitting the header. |
| **400** | *"The AI could not produce a valid structured plan after multiple attempts."* | Show an inline error: *"The AI couldn't generate a valid plan — try rephrasing your request."* Let the user edit `query` and retry (new `Idempotency-Key`). |
| **401** | Unauthorized (missing/invalid/expired token) | Attempt a token refresh once; on failure **redirect to login**. |
| **403** | *"You are not authorized…"* — role mismatch OR not assigned to this athlete | Show: **"You don't have access to this resource."** Likely causes: `Admin` on an AI route, `NutritionSpecialist` on a training route, or the coach is not assigned to the athlete. Hide/disable the AI UI for roles that can never use it. |
| **404** | *"Athlete with ID … was not found."*, *"AI chat session with ID … was not found."*, recommendation not found | Show: **"Not found."** For chat sessions, a 404 on a session you think you own means the session belongs to another coach or was never created. |
| **409** | *"A request with this Idempotency-Key is already being processed."* | The same generation request is already in flight (double submit, or a previous network retry). Keep the spinner — navigate/reload to the pending result, or wait and retry. |
| **409** | *"The Idempotency-Key has already been used for a different request."* | The key was reused with a **different** body. Frontend should **generate a new UUID and retry only if the coach explicitly re-submits** (e.g. they edited the query). Never auto-retry with a mutated key/body. |
| **500** | *"An unexpected error occurred."* (+ your own logged server trace) | Show a generic error message, and `console.error(err)` with the full response for debugging. |

> **401 vs 403:** *401* = you are not (or no longer) recognized — re-authenticate. *403* = you are recognized but not allowed to do this — keep the user logged in, show the access message, and prevent the action. The generate/review endpoints return `403` both when the role is wrong and when the coach is not assigned to the requested athlete.

---

## 8. Angular Implementation Notes

### 8.1 `Idempotency-Key` lifecycle (generate-plan)

```ts
private idempotencyKey: string | null = null;

onGeneratePlan(athleteId: string, domainId: number, query: string) {
  // ONE new UUID per logical submit
  this.idempotencyKey = this.idempotencyKey ?? crypto.randomUUID();
  this.generating.set(true);
  this.planService.generatePlan(
    { athleteId, domainId, query },
    { 'Idempotency-Key': this.idempotencyKey }
  ).subscribe({
    next: (res) => {
      this.idempotencyKey = null;        // ← reset AFTER a successful response
      this.generating.set(false);
      this.router.navigate(['review', res.data.recommendationId]);
    },
    error: (err) => {
      this.generating.set(false);
      // keep the key ONLY if you intend to retry the exact same request (network failure)
      if (err.status === 409) this.idempotencyKey = null; // user must re-submit → new UUID
    }
  });
}
```

- **Always** reset the key after a successful response so a page refresh or a new click mints a new UUID (and does not accidentally replay the cached old plan).
- If the request fails with a **network error / timeout**, you may keep the same key and auto-retry once — the backend replays the stored result rather than doubling the LLM call.
- On a **409 "already been used for a different request"**, drop the key and **only** retry after the coach explicitly re-submits.

### 8.2 Chat message flow — append, don't re-fetch

The send response already contains both persisted turns. Append them immediately:

```ts
onSend(sessionId: number, text: string) {
  this.messages.update(m => [...m, { role: 'user', content: text, pending: true }]); // optimistic bubble
  this.chatService.postMessage(sessionId, { message: text }).subscribe((res) => {
    this.messages.update(m => {
      const withoutOptimistic = m.filter(msg => !('pending' in msg));
      return [...withoutOptimistic, res.data.userMessage, res.data.assistantMessage];
    });
  });
}
```

No history re-fetch is needed after a send. Only `GET /sessions/{id}/messages` when the session is first opened.

### 8.3 Generation latency & double-submit protection

- The generate endpoint is LLM-backed — **up to 120 s**. Configure the HTTP client timeout accordingly (a default 30 s will misfire).
- Show a full-screen spinner and **disable** the Generate button while `generating` is true.
- Because the endpoint is idempotency-protected, even a race cannot create duplicate plans, but you should still block double-clicks for good UX.

### 8.4 Approve/reject gating

- Fetch the recommendation (Step 2). **Render the Approve/Reject buttons only when `status === RecommendationStatus.Pending (1)`**.
- After `POST .../{id}/review` succeeds, the returned `data.status` tells you the outcome (2 or 3) — switch the UI to the decided state immediately.

### 8.5 Don't silently drop `missingExerciseNotes`

`missingExerciseNotes` on the generate response is a deliberate signal that the AI could not find a perfect exercise match in the catalog. **Whenever it is non-null, surface it** (yellow warning banner) — never filter it out because it is `null` most of the time.

### 8.6 Typed service sketch (optional reference)

```ts
@Injectable({ providedIn: 'root' })
export class AiApiService {
  private base = environment.apiUrl; // https://<api-host>/api

  constructor(private http: HttpClient) {}

  private unwrap<T>() {
    return this.http.get<ApiResponse<T>>(url).pipe(map(r => r.data!));
  }
}
```

All services should unwrap `data` once and handle errors centrally via your interceptor — components should never see the `ApiResponse` envelope.

### 8.7 Guarding UI by role

Drive AI navigation visibility from the logged-in user's `roles` array (from the login response):

- `SwimmingCoach | FitnessCoach | NutritionSpecialist` → show AI Chat + AI Plan Generator + Recommendations.
- `Athlete` → show only their own plan panel + their own recommendation history.
- `Admin` → no AI surfaces at all.