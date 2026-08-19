# Software Requirements Specification (SRS)
# AquaMetrics
## AI-Powered Decision Support Platform for Competitive Swimming Academies

---

**Document Type:** Software Requirements Specification (SRS)  
**Standard:** IEEE 29148 Software and Systems Engineering — Life Cycle Processes — Requirements Engineering  
**Version:** V2.0  
**Document Status:** Final Draft  

**Prepared For:**  
Information Technology Institute  
Graduation Project Committee

**Prepared By:**
1. Mustafa Amr Allam
2. Ziad Tamer Abd El Hafeez
3. Ahmed Abobaker Ahmed Elzomor
4. Ali Alaa Eldin Elsayed
5. Belal Hesham Osman
6. Mohamed Awadallah Serry
7. Ziad Alaa Ezzat

**Academic Year:** 2026  
**Date:** January 2026

---

## Document Revision History

| Version | Date | Description | Author |
|---------|------|-------------|--------|
| V1.0 | July 2026 | Initial SRS (175 pages, text-heavy) | Team |
| V2.0 | January 2026 | Rewritten based on actual implementation: concise, diagram-first, competitive analysis added | Team |

---

## Table of Contents

1. Introduction
2. Overall Description
3. Competitive Analysis
4. Selling Points & Unique Value Proposition
5. System Features
6. External Interface Requirements
7. Non-Functional Requirements
8. Database Requirements
9. AI System Requirements
10. Deployment Requirements
11. Future Enhancements
12. Appendices

---

## 1. Introduction

### 1.1 Purpose

This Software Requirements Specification defines the functional and non-functional requirements for **AquaMetrics**, an AI-powered decision support platform for competitive swimming academies. AquaMetrics digitalizes athlete management while leveraging Artificial Intelligence with Retrieval-Augmented Generation (RAG) to provide evidence-based coaching recommendations across swimming, fitness, and nutrition domains. The system maintains a human-in-the-loop approach where all AI recommendations require coach approval before implementation.

### 1.2 Scope

AquaMetrics is a multi-platform system consisting of:
- **Flutter mobile application** for athletes and coaches
- **React web dashboard** for administrators and coaching staff
- **ASP.NET Core (.NET 10) backend** with RESTful APIs
- **RAG-powered AI infrastructure** (currently HuggingFace BGE-M3 embeddings + Qdrant vector database)

**Core Capabilities:**
- Athlete profile and performance management across three domains (Swimming, Fitness, Nutrition)
- Training plan creation, assignment, and tracking with session-based performance recording
- Group-based bulk assignment for efficient team management
- Knowledge base management with background PDF ingestion
- Real-time notifications via SignalR
- Comprehensive performance analytics with graded evaluations

**AI Roadmap:**
- **Phase 1 (Implemented):** RAG infrastructure with semantic search over curated knowledge base
- **Phase 2 (Planned):** Full AI recommendation engine with Semantic Kernel agents, Claude Sonnet 4, Cohere reranking, and Langfuse observability

**Target Users:** Competitive swimming academies with coaching staff across swimming, fitness, and nutrition specializations.

### 1.3 Definitions, Acronyms, and Abbreviations

See Appendix A (Glossary) and Appendix B (Acronyms).

---

## 2. Overall Description

### 2.1 Product Perspective

AquaMetrics is a standalone cloud-native platform that combines traditional athlete management with AI-assisted decision support. Unlike conventional swim team management systems (TeamUnify, SwimTopia), AquaMetrics integrates multi-domain training (swimming + fitness + nutrition) with evidence-based AI recommendations grounded in scientific literature.

**System Architecture Diagram:**

![System Architecture](diagrams will be inserted here - see srs-diagrams.md)

**Key Architectural Layers:**
- **Presentation:** Flutter mobile (Android/iOS), React web dashboard
- **Application:** ASP.NET Core API with JWT authentication, business logic, and SignalR notifications
- **AI (Implemented):** HuggingFace BGE-M3 embeddings (1024-dim), Qdrant vector database, PDF processing pipeline
- **AI (Planned):** Semantic Kernel orchestration, Claude Sonnet 4 LLM, Cohere reranking, Langfuse observability
- **Data:** SQL Server (transactional), Qdrant (vector embeddings)
- **Infrastructure:** Azure Cloud, Docker containers, GitHub Actions CI/CD

### 2.2 Product Functions Summary

| Function Category | Key Capabilities |
|-------------------|------------------|
| **User Management** | Role-based access (Admin, Swimming Coach, Fitness Coach, Nutrition Specialist, Athlete), JWT + refresh tokens |
| **Athlete Management** | Profile management with 1-to-1 user account, coach assignments per domain, group memberships |
| **Training Plans** | Exercise library, plan templates, bulk assignment to athletes/groups, archive/restore pattern |
| **Training Sessions** | Scheduled sessions with attendance tracking and detailed performance recording |
| **Performance Tracking** | Session-level ratings (performance 1-10, fatigue 1-10), exercise-by-exercise tracking (planned vs actual), swimming-specific analysis (stroke technique grading) |
| **Nutrition Management** | Meal-based plans with macros, assignment to athletes/groups |
| **Knowledge Base** | Background PDF ingestion with status polling, semantic embedding generation |
| **Notifications** | Real-time SignalR push notifications |
| **AI (Planned)** | Evidence-based recommendations with human approval workflow |


### 2.3 User Classes and Characteristics

![User Roles Diagram](see srs-diagrams.md - Diagram #7)

| Role | Responsibilities | Technical Expertise | Primary Interface |
|------|------------------|---------------------|-------------------|
| **Administrator** | User management, athlete registration, coach assignments, knowledge base uploads, system configuration | Intermediate | Web Dashboard |
| **Swimming Coach** | Create training plans/exercises, schedule sessions, record attendance/performance, assign plans, manage coach notes | Basic-Intermediate | Mobile + Web |
| **Fitness Coach** | Create fitness plans/exercises, schedule sessions, record attendance/performance, assign plans | Basic-Intermediate | Mobile + Web |
| **Nutrition Specialist** | Create nutrition plans with meals, assign plans, manage dietary notes | Basic-Intermediate | Web Dashboard |
| **Athlete** | View profile, training plans, performance history, notifications | Basic | Mobile App |

### 2.4 Operating Environment

**Client Platforms:**
- Mobile: Flutter on Android (primary MVP target) and iOS
- Web: Modern browsers (Chrome, Edge, Firefox)
- Internet connection required

**Server Infrastructure:**
- ASP.NET Core .NET 10 on Azure App Service
- SQL Server (Azure SQL Database)
- Qdrant Cloud for vector storage
- Docker containerization
- GitHub Actions for CI/CD

**External Services:**
- HuggingFace Inference API (BGE-M3 embeddings)
- SignalR for real-time notifications
- Supabase/Local file storage

### 2.5 Design and Implementation Constraints

**Technology Stack (Fixed):**
- Backend: ASP.NET Core .NET 10
- Frontend: Flutter + React
- Database: SQL Server + Qdrant
- Embeddings: HuggingFace BGE-M3 (1024-dim)
- Deployment: Azure + Docker + GitHub Actions

**AI Design Principles:**
- RAG-first: All AI outputs must be grounded in retrieved evidence from curated knowledge base
- Human-in-the-loop: No AI recommendation can be applied without explicit coach approval
- Explainable: All recommendations must include rationale and supporting references
- Domain-scoped: Separate agents/logic for Swimming, Fitness, and Nutrition domains

**Security Requirements:**
- HTTPS only
- JWT authentication with refresh tokens
- Role-based authorization enforced at API level
- Secure password hashing (ASP.NET Core Identity)

---

## 3. Competitive Analysis

![Competitive Analysis Matrix](see srs-diagrams.md - Diagram #4)

### Comparison Against Market Leaders

| Platform | AI Capabilities | Multi-Domain | Evidence-Based | Human-in-Loop | Target Market | Key Strengths | Key Gaps |
|----------|----------------|--------------|----------------|---------------|---------------|---------------|----------|
| **TeamUnify** | None | Swimming only | No | N/A | Medium-large competitive clubs | Market leader, meet management, Hy-Tek integration, billing/registration | No AI, no fitness/nutrition, no performance analytics |
| **SwimTopia** | None | Swimming only | No | N/A | Small-medium clubs | Modern UX, easier than TeamUnify, concierge migration | No AI, no cross-domain training, no decision support |
| **MySwimPro** | Basic (rule-based) | Swimming + some fitness | No | No (consumer app) | Individual swimmers | Wearable integration (Apple Watch, Garmin), technique videos | Not for academy management, no coach workflow, no RAG |
| **Club Assistant** | None | Swimming only | No | N/A | Budget-conscious small clubs | Affordable, covers admin essentials | No AI, limited reporting, no performance tracking |
| **Hy-Tek Meet Manager** | None | Swimming + Track | No | N/A | Meet timing operators | Industry standard for meet execution, timing hardware integration | Not a management platform, no training features, no AI |
| **AquaMetrics** | **RAG-grounded LLM (planned)** | **Swimming + Fitness + Nutrition** | **Yes** | **Yes (coach approval)** | Competitive academies | Only AI-powered, only multi-domain, evidence-based, full observability | Smaller market share (new entrant) |

### Key Differentiators

**AquaMetrics is the only platform that:**
1. Uses Retrieval-Augmented Generation to ground AI recommendations in scientific literature
2. Integrates swimming, fitness, and nutrition in a unified coaching workflow
3. Requires human coach approval for all AI suggestions (safety + accountability)
4. Provides full observability and traceability for AI decisions (Langfuse integration planned)
5. Combines athlete management + AI decision support in one system
6. Backed by academic research on RAG for competitive swimming (arXiv papers)

**Content rephrased for compliance with licensing restrictions. Sources: [GitNux 2026 Review](https://gitnux.org/best/swim-team-software/), [SwimTopia Comparison](https://www.swimtopia.com/swimtopia-teamunify-compared/), [MySwimPro Features](https://www.myswimpro.com/).**

---

## 4. Selling Points & Unique Value Proposition

### What Makes AquaMetrics Different

```
┌─────────────────────────────────────────────────────────────┐
│  Traditional Swim Management     vs    AquaMetrics          │
├─────────────────────────────────────────────────────────────┤
│  ❌ Data repository only         ✅ AI decision support     │
│  ❌ Swimming domain only         ✅ Multi-domain integration│
│  ❌ No scientific grounding      ✅ RAG-based evidence      │
│  ❌ Manual decision-making       ✅ AI-assisted coaching    │
│  ❌ Basic reporting              ✅ Performance analytics   │
│  ❌ No recommendation workflow   ✅ Human-in-the-loop       │
└─────────────────────────────────────────────────────────────┘
```

### Core Value Propositions

**1. Evidence-Based AI Recommendations**
- Retrieval-Augmented Generation grounds all suggestions in peer-reviewed sports science literature
- Reduces guesswork and increases consistency in coaching decisions
- Every recommendation includes supporting references and rationale

**2. Multi-Domain Holistic Approach**
- First platform to integrate swimming, fitness, and nutrition coaching in one workflow
- Recognizes that athlete performance depends on coordinated training across all three domains
- Eliminates data silos between coaching specializations

**3. Human-in-the-Loop Safety**
- AI serves as assistant, not replacement
- All recommendations require explicit coach approval
- Maintains professional accountability and regulatory compliance
- Coach can approve, modify, or reject every suggestion

**4. Full Observability & Traceability**
- Complete audit trail from athlete data → knowledge retrieval → AI generation → coach decision
- Langfuse integration (planned) for prompt versioning, token tracking, and performance monitoring
- Enables continuous improvement of AI quality

**5. Session-Level Performance Tracking**
- Detailed recording of actual vs planned execution (sets, reps, weight, RPE)
- Swimming-specific grading (technique, start, turns, finish, pace consistency)
- Performance rating (1-10) + fatigue level (1-10) + completion status + injury flags
- Enables data-driven coaching adjustments

**6. Research-Backed Approach**
- Aligned with emerging academic work on RAG for swimming coaching (see arXiv papers)
- Modern AI architecture avoids pitfalls of pure LLM approaches (hallucinations, outdated knowledge)
- Designed for trustworthy AI in high-stakes sports contexts

---

## 5. System Features

### Feature Overview

AquaMetrics implements 13 core feature areas. Each feature includes a flow diagram, brief description, and key functional requirements.

---

### 5.1 Authentication & Authorization

![Authentication Flow](see srs-diagrams.md - Diagram #5)

**Description:**  
Secure user authentication using JWT access tokens with refresh tokens. Role-based authorization controls access to domain-specific features.

**Main Flow:**
1. User submits credentials → API validates → JWT generated with role claims → tokens returned
2. Protected requests include Bearer token → API validates token + role → request processed or denied
3. Expired access token → client uses refresh token → new tokens issued

**Key Requirements:**
- JWT authentication with role claims (Admin, SwimmingCoach, FitnessCoach, NutritionSpecialist, Athlete)
- Refresh token rotation for security
- Password hashing via ASP.NET Core Identity
- Token validation on all protected endpoints
- HTTPS only

---

### 5.2 User & Athlete Management

**Description:**  
Administrators register athletes with user accounts (1-to-1 relationship), assign coaches per domain, and manage system users.

**Main Flow:**
1. Admin creates athlete → user account created → athlete profile linked → coaches assigned (one per domain)
2. Coach views only assigned athletes filtered by their domain
3. Athlete can view own profile, training plans, and performance history

**Key Requirements:**
- Athlete has mandatory UserId (1-to-1 with ApplicationUser)
- Each athlete assigned to exactly one Swimming Coach, one Fitness Coach, one Nutrition Specialist
- Coach assignments are domain-scoped and filterable
- Athlete profile includes demographics, medical notes, emergency contacts
- Role-based access enforced

---

### 5.3 Group Management

**Description:**  
Coaches create groups (e.g., "Junior Squad", "Senior Team") to organize athletes for bulk training plan assignment.

**Main Flow:**
1. Coach creates group → assigns athletes to group → assigns training/nutrition plan to group
2. System creates individual plan assignments for each athlete in group (assignment retains GroupId as provenance)
3. AI evaluates each athlete individually despite bulk assignment

**Key Requirements:**
- Groups are domain-scoped (Swimming, Fitness, Nutrition)
- Many-to-many relationship: athlete can be in multiple groups
- AthleteGroup tracks join date and archive status
- Bulk assignment creates individual TrainingPlanAssignment records (AthleteId mandatory, GroupId nullable)

---

### 5.4 Exercise & Training Plan Management

![Training Workflow](see srs-diagrams.md - Diagram #6)

**Description:**  
Coaches build reusable exercise libraries and assemble them into training plans. Plans have Source (Manual | AIAssisted) and ApprovalStatus (Draft | Approved).

**Main Flow:**
1. Coach creates exercises (domain-scoped) → builds training plan by selecting exercises → configures exercise parameters (sets, reps, intensity, duration, order, rest)
2. Plan saved as template (can be reused across multiple athletes/groups)
3. Plan assignment → links plan to athlete(s) with optional group provenance

**Key Requirements:**
- Exercise library is domain-specific and coach-created
- PlanExercise junction table stores exercise-specific config (sets, reps, intensity, duration, order, rest, notes)
- TrainingPlan has Source (Manual/AIAssisted) and ApprovalStatus (Draft/Approved)
- Archive/restore pattern (soft delete via IsArchived flag)
- Plans can be assigned to individual athletes or groups (bulk assignment)

---

### 5.5 Training Sessions & Attendance

**Description:**  
Coaches schedule training sessions linked directly to training plans, record athlete attendance (Present/Absent/Late/Excused), and track session completion.

**Main Flow:**
1. Coach creates session (date, time, location, training plan) → athletes notified
2. During/after session → coach marks attendance per athlete
3. Session serves as container for attendance records and training records

**Key Requirements:**
- TrainingSession links directly to TrainingPlan (not through assignment)
- Session includes coach, domain, date/time, location
- Attendance tracked per athlete per session with status enum
- Attendance history used for performance analytics and AI context

---

### 5.6 Training Records & Performance Tracking

**Description:**  
After each session, coach records detailed performance data per athlete: overall session rating, exercise-by-exercise execution, and swimming-specific analysis.

**Main Flow:**
1. Coach selects session + athlete → creates TrainingRecord (performance rating 1-10, fatigue 1-10, completion status, injury flag)
2. For each exercise → records ExercisePerformance (actual sets/reps/weight/RPE vs planned)
3. For swimming → records SwimmingPerformance (stroke type, distance, timing, graded technique/start/turns/finish)
4. Data stored for trend analysis and AI context

**Key Requirements:**
- TrainingRecord tracks: PerformanceRating (1-10), FatigueLevel (1-10), SessionCompleted (bool), InjuryOccurred (bool), OverallComment
- ExercisePerformance links to PlanExercise and tracks: CompletedSets, CompletedReps, CompletedDuration, WeightUsed, RPE, Status, CoachComment
- SwimmingPerformance tracks: Stroke, Distance, Repetitions, RestInterval, BestRepTime, AverageRepTime, WorstRepTime, Technique grade, Start grade, Turns grade, Finish grade, PaceConsistency grade, RPE, Status, CoachComment
- All performance data contributes to athlete AI context

---

### 5.7 Nutrition Management

**Description:**  
Nutrition specialists create meal-based nutrition plans with macros, assign them to athletes/groups, and track dietary compliance.

**Main Flow:**
1. Specialist creates nutrition plan (title, daily calories, macros) → adds meals (meal type, description, macros, timing, water intake, order)
2. Plan assigned to athlete(s) with start/end dates
3. NutritionPlanAssignment can trigger AI recommendations (planned feature)

**Key Requirements:**
- NutritionPlan includes: Title, Objectives, DailyCalories, ProteinGrams, CarbGrams, FatGrams, Source (Manual/AIAssisted), ApprovalStatus (Draft/Approved)
- NutritionPlanMeal specifies: MealType (Breakfast/Lunch/Dinner/Snack/PreWorkout/PostWorkout), Calories, Macros, TimingNotes, WaterMl, OrderIndex
- Assignment includes StartDate and optional EndDate
- Override mechanism for athlete-specific modifications (linked to AI recommendations)

---

### 5.8 Coach Notes

**Description:**  
Coaches document qualitative observations about athletes (injuries, behavior, motivation, technique notes) that supplement quantitative performance data.

**Main Flow:**
1. Coach selects athlete → creates note with free-text content
2. Note stored with author, timestamp, athlete reference
3. Notes accessible to all coaches assigned to that athlete
4. Notes contribute to AI context during recommendation generation

**Key Requirements:**
- CoachNote stores: AthleteId, AuthorId, Content, CreatedAt, UpdatedAt, IsDeleted (soft delete)
- Only author can edit their notes
- All assigned coaches can read notes for their athletes
- Notes included in athlete AI context

---

### 5.9 Knowledge Base Management

![RAG Pipeline](see srs-diagrams.md - Diagram #3)

**Description:**  
Administrators upload PDF documents (research papers, coaching guidelines) that are processed into searchable vector embeddings for AI retrieval.

**Main Flow:**
1. Admin uploads PDF → document record created (Status: Pending) → work item queued
2. Background worker processes PDF → extracts text (PdfPig) → chunks text → generates embeddings (HuggingFace BGE-M3) → upserts to Qdrant
3. Document status updated: Pending → Processing → Indexed (or Failed)
4. Admin polls status endpoint to confirm completion

**Key Requirements:**
- Background processing via IBackgroundIngestionQueue (async, non-blocking)
- PDF extraction using PdfPig library
- Chunking with overlap for context preservation
- HuggingFace BGE-M3 embeddings (1024-dim) via Inference API
- Qdrant vector database with domain filtering
- Status tracking: Pending, Processing, Indexed, Failed
- Only Admin role can upload documents

---

### 5.10 AI Recommendation Workflow (Planned - Not Yet Implemented)

![AI Recommendation Workflow](see srs-diagrams.md - Diagram #9)

**Description:**  
When implemented, this feature will use Semantic Kernel agents to analyze athlete data, retrieve relevant scientific evidence via RAG, generate personalized recommendations using Claude Sonnet 4, and present them to coaches for approval.

**Planned Flow:**
1. Coach requests recommendation for athlete + training plan
2. System collects athlete context (performance, attendance, notes, medical, history)
3. Domain agent (Swimming/Fitness/Nutrition) selected
4. Semantic search in Qdrant → retrieve top-K documents → Cohere rerank
5. Claude Sonnet 4 generates recommendation with evidence + rationale
6. Langfuse logs execution trace
7. Recommendation stored (Status: Pending) → coach reviews → approves/modifies/rejects
8. If approved → creates plan override for that athlete only

**Key Requirements (Design):**
- AiRecommendation entity: AthleteId, DomainId, TrainingPlanAssignmentId, NutritionPlanAssignmentId, RecommendationText, Rationale, Status (Pending/Approved/Rejected/Modified)
- RecommendationReview: ReviewerId, Decision, Comments, ReviewedAt
- RecommendationEvidence: many-to-many with KnowledgeDocument (tracks which sources were cited)
- TrainingPlanOverride / NutritionPlanOverride: linked to approved recommendations only
- Human approval mandatory before application

**Current Status:**  
⚠️ Entities and database schema exist, but API endpoints and AI orchestration not yet implemented. Only RAG infrastructure (embeddings + Qdrant) is operational.

---

### 5.11 Notifications

**Description:**  
Real-time push notifications via SignalR inform users of important events (training sessions scheduled, recommendations pending, performance recorded).

**Main Flow:**
1. System event occurs (session created, recommendation generated, attendance recorded)
2. Notification created with type, title, message, target user
3. SignalR pushes to connected clients in real-time
4. User receives in-app notification + marks as read

**Key Requirements:**
- Notification entity: UserId, Type (enum), Title, Message, IsRead, CreatedAt
- SignalR hub for real-time push
- Notification types: Session scheduled, Attendance recorded, Recommendation pending, Recommendation reviewed, Plan assigned, etc.
- Notifications filterable by user and read status

---

### 5.12 Analytics & Reporting

**Description:**  
Coaches and administrators view aggregated performance metrics, attendance trends, and athlete progress dashboards.

**Key Capabilities:**
- Performance trend analysis (rating, fatigue over time)
- Exercise completion rates
- Attendance statistics per athlete/group
- AI usage metrics (planned: recommendations generated/approved/rejected)
- Session status tracking (athletes with/without recorded performance)

---

### 5.13 Profile Management

**Description:**  
Athletes view their own profile, training plans, performance history, and notifications via mobile app.

**Key Requirements:**
- Athlete role restricted to read-only access to own data
- View assigned training/nutrition plans
- View performance history with charts
- View attendance records
- Receive and manage notifications

---

## 6. External Interface Requirements

### 6.1 User Interfaces

**Flutter Mobile Application:**
- Target: Android (primary MVP), iOS
- Users: Athletes (view-only), Coaches (full workflow)
- Key screens: Login, athlete list, training plan viewer, session scheduler, attendance recorder, performance recorder, notifications
- Responsive design for phones and tablets

**React Web Dashboard:**
- Target: Modern browsers (Chrome, Edge, Firefox)
- Users: Administrators, Coaches, Nutrition Specialists
- Key screens: User management, athlete registration, group management, plan builder, knowledge base uploader, analytics dashboards, recommendation review (planned)
- Material Design-inspired UI

### 6.2 Hardware Interfaces

- No specialized hardware required for MVP
- Standard smartphones, tablets, desktop computers
- **Future:** Wearable integration (Apple Watch, Garmin, Polar, WHOOP) for automated performance data capture

### 6.3 Software Interfaces

| Interface | Technology | Purpose |
|-----------|-----------|---------|
| Backend API | ASP.NET Core .NET 10 REST | Business logic, authentication, data persistence |
| Database | SQL Server via Entity Framework Core | Transactional data storage |
| Vector Database | Qdrant Cloud | Semantic search over knowledge embeddings |
| Embeddings | HuggingFace Inference API (BGE-M3) | Generate 1024-dim vectors for documents/queries |
| Real-time | SignalR | Push notifications |
| File Storage | Supabase / Local | PDF storage |
| LLM (Planned) | Anthropic Claude Sonnet 4 | Recommendation generation |
| Reranking (Planned) | Cohere Rerank 3.5 | Optimize retrieval results |
| Observability (Planned) | Langfuse | AI execution tracing, prompt versioning |

### 6.4 Communication Interfaces

- All client-server communication via HTTPS
- RESTful JSON APIs for CRUD operations
- WebSocket (SignalR) for real-time notifications
- JWT Bearer tokens for authentication
- CORS configured for web dashboard origin

---

## 7. Non-Functional Requirements

### Summary Table

| Category | Requirement | Target Metric |
|----------|-------------|---------------|
| **Performance** | API response time | < 2s for standard CRUD, < 15s for AI recommendations |
| **Performance** | Concurrent users | Support 100+ simultaneous users |
| **Scalability** | Horizontal scaling | Docker containers on Azure App Service |
| **Scalability** | Database growth | Support 10,000+ athletes, 100,000+ training records |
| **Availability** | Uptime | 99% (excluding scheduled maintenance) |
| **Reliability** | Data integrity | ACID transactions, no data loss on failure |
| **Security** | Authentication | JWT with refresh tokens, HTTPS only |
| **Security** | Authorization | Role-based access control enforced at API level |
| **Security** | Password storage | Hashed via ASP.NET Core Identity (PBKDF2) |
| **Privacy** | Data protection | Medical notes, performance data restricted to authorized coaches |
| **Privacy** | AI data usage | Athlete data used only for recommendations, not retained by external AI services |
| **Usability** | Learning curve | < 1 hour for coaches to become productive |
| **Usability** | Mobile responsiveness | Touch-friendly, optimized for 5-10" screens |
| **Maintainability** | Architecture | Clean Architecture with separated layers |
| **Maintainability** | Code quality | Unit tests for critical business logic |
| **Portability** | Platform independence | .NET 10 (cross-platform), Docker containers |
| **Observability** | Logging | Application Insights, structured logging |
| **Observability** | AI tracing (planned) | Langfuse for prompt versioning, token tracking, latency |

### Key Constraints

- **AI Safety:** No AI-generated content applied without human approval
- **Data Residency:** All athlete data stored in compliant Azure regions
- **Backward Compatibility:** Database migrations preserve historical data
- **Soft Delete Pattern:** Archive instead of delete for training plans, exercises, groups, records

---

## 8. Database Requirements

![Database ER Diagram](see srs-diagrams.md - Diagram #2)

### 8.1 Conceptual Data Model

**28 Core Entities:**

**Identity & Users:**
- ApplicationUser (extends IdentityUser, includes FullName, IsActive, role via ASP.NET Core Identity)
- RefreshToken (for JWT refresh mechanism)

**Athlete Management:**
- Athlete (1-to-1 with ApplicationUser, includes DateOfBirth, Gender, MedicalNotes, RegistrationStatus)
- Domain (Swimming=1, Fitness=2, Nutrition=3)
- CoachAssignment (athlete-to-coach per domain, enforces one active coach per domain via unique constraint)

**Organizational:**
- Group (domain-scoped athlete groups)
- AthleteGroup (many-to-many junction with join date, archive flag)

**Training Plans:**
- Exercise (domain-scoped library)
- TrainingPlan (includes Source: Manual/AIAssisted, ApprovalStatus: Draft/Approved)
- PlanExercise (junction with sets, reps, intensity, duration, order, rest, notes)
- TrainingPlanAssignment (AthleteId mandatory, GroupId nullable for provenance)
- TrainingPlanOverride (athlete-specific modifications, links to approved AI recommendations)

**Nutrition Plans:**
- NutritionPlan (includes macros, Source, ApprovalStatus)
- NutritionPlanMeal (meal type, macros, timing, water intake, order)
- NutritionPlanAssignment (includes StartDate, EndDate)
- NutritionPlanOverride (athlete-specific modifications)

**Sessions & Performance:**
- TrainingSession (links to TrainingPlan, includes coach, domain, date/time, location)
- Attendance (tracks Present/Absent/Late/Excused per athlete per session)
- TrainingRecord (per-athlete per-session: PerformanceRating, FatigueLevel, SessionCompleted, InjuryOccurred)
- ExercisePerformance (actual vs planned per exercise)
- SwimmingPerformance (stroke analysis with grading)

**Observations:**
- CoachNote (qualitative observations, soft delete)
- Assessment (domain-scoped, planned feature)

**AI System:**
- KnowledgeDocument (PDF metadata, IndexStatus: Pending/Processing/Indexed/Failed, VectorRef)
- AiRecommendation (athlete-specific, includes RecommendationText, Rationale, Status)
- RecommendationReview (coach decision: Approved/Rejected/NeedsModification)
- RecommendationEvidence (many-to-many: which docs cited in which recommendation)

**System:**
- Notification (user-specific, type, message, IsRead)
- AuditLog (system activity logging)

### 8.2 Key Relationships

- ApplicationUser → Athlete (1-to-1 via UserId)
- Athlete → CoachAssignment (many-to-many with Domain scope, one active per domain)
- TrainingPlan → PlanExercise → Exercise (many-to-many junction)
- TrainingSession → TrainingPlan (direct link, not through assignment)
- TrainingSession → TrainingRecord → ExercisePerformance, SwimmingPerformance
- AiRecommendation → RecommendationEvidence → KnowledgeDocument (many-to-many)
- AiRecommendation → TrainingPlanOverride / NutritionPlanOverride (one-to-many, only if approved)

### 8.3 Data Integrity

- Foreign keys enforced for all relationships
- Unique constraints: User email, (AthleteId, DomainId, IsActive=true) for coach assignments
- Soft delete via IsArchived or IsDeleted flags (preserves history)
- ACID transactions for multi-entity operations
- Cascading rules: prevent orphaned records

---

## 9. AI System Requirements

### 9.1 AI System Overview

AquaMetrics is designed as a two-phase AI implementation:

**Phase 1 (Implemented):** RAG Infrastructure
- PDF ingestion with background processing
- Text extraction (PdfPig) and chunking
- Embedding generation (HuggingFace BGE-M3, 1024-dim)
- Vector storage and semantic search (Qdrant)
- Domain-filtered retrieval

**Phase 2 (Planned):** Full AI Recommendation Engine
- Semantic Kernel orchestration
- Domain-specific AI agents (Swimming, Fitness, Nutrition)
- Claude Sonnet 4 for recommendation generation
- Cohere Rerank 3.5 for retrieval optimization
- Langfuse for observability and prompt versioning
- Human-in-the-loop approval workflow

### 9.2 RAG Pipeline (Implemented)

![RAG Pipeline Diagram](see srs-diagrams.md - Diagram #3)

**Architecture:**
1. **Document Upload:** Admin uploads PDF → KnowledgeDocument record created (Status: Pending)
2. **Background Processing:** Ingestion worker dequeues work item, updates status to Processing
3. **Text Extraction:** PdfPig extracts text from all pages
4. **Chunking:** DocumentChunkingService splits text into overlapping chunks (preserves context)
5. **Embedding Generation:** HuggingFace BGE-M3 API generates 1024-dim vector per chunk
6. **Vector Storage:** Batch upsert to Qdrant with metadata (DocumentId, ChunkId, Content, DomainId, Title, UploadedBy, IndexStatus, CreatedAt)
7. **Status Update:** Document status → Indexed (or Failed if error)
8. **Retrieval:** Semantic search via Qdrant with domain filtering returns top-K chunks

**Key Technologies:**
- **Embeddings:** HuggingFace BGE-M3 via Inference API (multilingual, 1024-dim, optimized for retrieval)
- **Vector DB:** Qdrant Cloud with collection per deployment
- **PDF Processing:** PdfPig (open-source, .NET-native)
- **Chunking:** Custom service with configurable size/overlap
- **Background Queue:** In-memory queue with hosted service worker

**Benefits:**
- Non-blocking PDF processing (API returns 202 Accepted immediately)
- Status polling for completion tracking
- Fault tolerance (failed documents marked, don't block queue)
- Domain filtering ensures relevant retrieval per coaching area

### 9.3 AI Recommendation Workflow (Planned)

![AI Workflow Diagram](see srs-diagrams.md - Diagram #9)

**Design:**
1. **Request:** Coach initiates recommendation for athlete + plan
2. **Context Collection:** System gathers athlete data (performance, notes, attendance, medical, history)
3. **Agent Selection:** Semantic Kernel selects domain agent (Swimming/Fitness/Nutrition)
4. **Retrieval:** Agent queries Qdrant with athlete context + domain filter → top-K documents
5. **Reranking:** Cohere Rerank 3.5 optimizes relevance ordering
6. **Prompt Assembly:** Agent constructs prompt with athlete context + retrieved evidence + domain instructions
7. **Generation:** Claude Sonnet 4 generates recommendation with rationale
8. **Observability:** Langfuse logs execution trace (prompt version, tokens, latency, retrieved docs)
9. **Storage:** Recommendation saved (Status: Pending) with linked evidence documents
10. **Human Review:** Coach reviews recommendation + evidence → approves/modifies/rejects
11. **Application:** If approved → TrainingPlanOverride or NutritionPlanOverride created for that athlete only

**Hallucination Mitigation:**
- RAG grounds output in curated knowledge base (no pure LLM generation)
- Domain agents limit scope and improve prompt quality
- Evidence-based: every recommendation cites supporting documents
- Human-in-the-loop prevents unsafe application
- Continuous monitoring via Langfuse for quality assurance

**Planned Stack:**
- **Orchestration:** Microsoft Semantic Kernel (open-source, .NET-native, plugin architecture)
- **LLM:** Anthropic Claude Sonnet 4 (strong reasoning, large context window, safety-focused)
- **Reranking:** Cohere Rerank 3.5 (improves retrieval precision)
- **Observability:** Langfuse (open-source, prompt versioning, token tracking, evaluation)

---

## 10. Deployment Requirements

![Deployment Architecture](see srs-diagrams.md - Diagram #8)

### 10.1 Deployment Overview

AquaMetrics follows a cloud-native, containerized deployment model with automated CI/CD.

**Infrastructure:**
- **Cloud Provider:** Microsoft Azure
- **Compute:** Azure App Service (Docker containers)
- **Database:** Azure SQL Database (managed SQL Server)
- **Vector DB:** Qdrant Cloud (managed service)
- **File Storage:** Supabase or Azure Blob Storage
- **CI/CD:** GitHub Actions
- **Monitoring:** Azure Application Insights

### 10.2 Deployment Architecture

```
GitHub Repository
    ↓
GitHub Actions (CI/CD Pipeline)
    ↓ (on push to main)
Build → Test → Docker Image → Azure App Service
    ↓
Azure App Service (ASP.NET Core Container)
    ├─→ Azure SQL Database
    ├─→ Qdrant Cloud
    ├─→ HuggingFace Inference API
    ├─→ Azure Application Insights
    └─→ Supabase (File Storage)
```

### 10.3 CI/CD Pipeline

**GitHub Actions Workflow:**
1. **Trigger:** Push to main branch or pull request
2. **Build:** Restore NuGet packages, compile .NET solution
3. **Test:** Run unit tests (if configured)
4. **Docker Build:** Create container image with app + dependencies
5. **Push:** Upload image to Azure Container Registry
6. **Deploy:** Azure App Service pulls latest image and restarts
7. **Health Check:** Verify /health endpoint responds

### 10.4 Environment Configuration

**Secrets (Azure Key Vault or GitHub Secrets):**
- Database connection string
- HuggingFace API key
- Qdrant API key and endpoint
- JWT signing key
- Supabase credentials
- Application Insights instrumentation key
- Claude API key (planned)
- Cohere API key (planned)
- Langfuse API key (planned)

**Environment Variables:**
- ASPNETCORE_ENVIRONMENT (Development, Staging, Production)
- Qdrant collection name
- Embedding model ID (HuggingFace)
- File storage provider (Supabase or Local)
- CORS allowed origins

### 10.5 Scalability & Availability

- **Horizontal Scaling:** Azure App Service supports multiple container instances
- **Database:** Azure SQL with automated backups, point-in-time restore
- **Vector DB:** Qdrant Cloud managed service with replication
- **Monitoring:** Application Insights tracks exceptions, performance, availability
- **Logging:** Structured logging to Application Insights for diagnostics

---

## 11. Future Enhancements

### 11.1 Multi-Sport Support
Extend beyond swimming to other competitive sports (track & field, gymnastics, martial arts) by adding sport-specific domains and performance metrics.

### 11.2 Wearable Integration
Integrate with Apple Watch, Garmin, Polar, and WHOOP devices to automatically capture heart rate, GPS, sleep, and recovery data for more comprehensive athlete monitoring.

### 11.3 Athlete Mobile Portal Enhancement
Expand athlete mobile app to include workout tracking, self-reported wellness surveys, nutrition logging, and social features (team chat, achievements).

### 11.4 Predictive Analytics
Machine learning models to predict injury risk, optimal recovery periods, performance plateaus, and competition readiness based on historical data.

### 11.5 Explainable AI Dashboard
Visual interface showing how AI recommendations were generated: which data points were considered, which knowledge sources were retrieved, confidence scores, alternative suggestions.

### 11.6 Voice Assistant
Voice-controlled interface for coaches to record performance data hands-free during training sessions ("Alexa, record Ahmed's performance: 8 out of 10, completed all sets").

### 11.7 Real-Time IoT Integration
Direct integration with pool timing systems, heart rate monitors, and swim stroke sensors for automatic performance capture without manual entry.

### 11.8 Advanced Assessment Tools
Structured assessment templates (FMS, VO2 max, lactate threshold) with automatic scheduling, reminders, and trend analysis.

### 11.9 Competition Management
Meet entry management, heat sheets, results tracking, personal best tracking, and integration with Hy-Tek Meet Manager.

### 11.10 Parent Portal
Read-only access for parents/guardians to view their athlete's training schedule, attendance, performance summaries, and coach communications.

---

## 12. Appendices

### Appendix A: Glossary

| Term | Definition |
|------|------------|
| **Academy** | A competitive swimming organization using AquaMetrics to manage athletes and coaching |
| **Athlete** | A swimmer registered in the system with associated user account and performance data |
| **Coach** | Qualified professional responsible for training athletes in a specific domain (Swimming, Fitness, or Nutrition) |
| **Domain** | Coaching specialization area: Swimming (ID=1), Fitness (ID=2), or Nutrition (ID=3) |
| **RAG** | Retrieval-Augmented Generation: AI architecture that grounds LLM outputs in retrieved documents |
| **Embedding** | Numerical vector representation (1024-dim) of text used for semantic similarity search |
| **Vector Database** | Specialized database (Qdrant) for storing and querying high-dimensional vectors |
| **Human-in-the-Loop** | AI design pattern requiring human approval before AI suggestions are applied |
| **Hallucination** | AI-generated content that is factually incorrect or unsupported by evidence |
| **Training Record** | Detailed performance data recorded after a training session, including ratings and exercise execution |
| **Plan Override** | Athlete-specific modification to a shared training/nutrition plan based on AI recommendation |
| **Archive Pattern** | Soft delete approach using IsArchived flag to preserve history while hiding from active use |

### Appendix B: Acronyms

| Acronym | Full Form |
|---------|-----------|
| AI | Artificial Intelligence |
| API | Application Programming Interface |
| BGE-M3 | BAAI General Embedding, Multilingual, Multitask, Model 3 |
| CI/CD | Continuous Integration / Continuous Deployment |
| CRUD | Create, Read, Update, Delete |
| ER | Entity-Relationship |
| HITL | Human-in-the-Loop |
| JWT | JSON Web Token |
| LLM | Large Language Model |
| MVP | Minimum Viable Product |
| RAG | Retrieval-Augmented Generation |
| RBAC | Role-Based Access Control |
| REST | Representational State Transfer |
| RPE | Rate of Perceived Exertion |
| SRS | Software Requirements Specification |
| UX | User Experience |

### Appendix C: Technology Stack Summary

![Technology Stack](see srs-diagrams.md - Diagram #10)

**Implemented:**
- Frontend: Flutter (Mobile), React (Web Dashboard)
- Backend: ASP.NET Core .NET 10, Entity Framework Core
- Authentication: JWT + Refresh Tokens (ASP.NET Core Identity)
- Database: SQL Server (Azure SQL)
- Vector DB: Qdrant Cloud
- Embeddings: HuggingFace BGE-M3 (1024-dim)
- PDF Processing: PdfPig
- Real-time: SignalR
- File Storage: Supabase / Local
- Deployment: Azure App Service, Docker, GitHub Actions
- Monitoring: Azure Application Insights

**Planned:**
- AI Orchestration: Microsoft Semantic Kernel
- LLM: Anthropic Claude Sonnet 4
- Reranking: Cohere Rerank 3.5
- Observability: Langfuse

### Appendix D: References

1. IEEE 29148:2018 - Systems and Software Engineering — Life Cycle Processes — Requirements Engineering
2. Anthropic Claude Documentation - https://docs.anthropic.com/
3. Microsoft Semantic Kernel Documentation - https://learn.microsoft.com/semantic-kernel
4. Qdrant Vector Database Documentation - https://qdrant.tech/documentation/
5. HuggingFace Inference API - https://huggingface.co/docs/api-inference
6. Cohere Rerank Documentation - https://docs.cohere.com/docs/rerank
7. Langfuse Documentation - https://langfuse.com/docs
8. "A Validated Multimodal Dataset for Trustworthy AI-Assisted Swimming Coaching" - arXiv:2605.12799
9. "AI for swimming recommendation systems exploring the current landscape" - Springer (2025)
10. GitNux "Top 10 Best Swim Team Software (2026 Review)" - https://gitnux.org/best/swim-team-software/

### Appendix E: Implementation Status Summary

**Fully Implemented:**
✅ Authentication & Authorization (JWT + Refresh Tokens)  
✅ User & Athlete Management  
✅ Group Management  
✅ Exercise & Training Plan Management  
✅ Training Sessions & Attendance  
✅ Training Records & Performance Tracking (detailed metrics)  
✅ Nutrition Management  
✅ Coach Notes  
✅ Knowledge Base Management (PDF ingestion with RAG pipeline)  
✅ Notifications (SignalR)  
✅ Analytics & Reporting  
✅ Profile Management  

**Partially Implemented (Infrastructure Only):**
⚠️ AI Recommendation Workflow (entities exist, no APIs)  
⚠️ Assessment Recording (entity exists, no APIs)  

**Planned (Not Yet Started):**
🔜 Semantic Kernel Integration  
🔜 Claude Sonnet 4 LLM Integration  
🔜 Cohere Rerank Integration  
🔜 Langfuse Observability Integration  
🔜 AI Recommendation Approval Workflow  

---

## Document End

**For diagram sources:** See `docs/srs-diagrams.md`  
**For diagram rendering:** Use [Mermaid Live Editor](https://mermaid.live) or VS Code Mermaid extension

---

**Document Prepared By:** AquaMetrics Development Team  
**Date:** January 2026  
**Version:** 2.0 (Concise, Diagram-First Edition)
