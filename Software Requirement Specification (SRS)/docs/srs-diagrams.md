# AquaMetrics SRS Diagrams (Mermaid Source)

## 1. System Architecture Diagram

```mermaid
graph TB
    subgraph "Presentation Layer"
        MA[Flutter Mobile App<br/>Android/iOS]
        WD[React Web Dashboard<br/>Chrome/Edge/Firefox]
    end
    
    subgraph "Application Layer"
        API[ASP.NET Core API<br/>.NET 10<br/>RESTful Services]
        AUTH[Authentication<br/>JWT + Refresh Tokens]
        BL[Business Logic<br/>Services & Validation]
    end
    
    subgraph "AI Layer - Planned"
        SK[Semantic Kernel<br/>Orchestration]
        LLM[Claude Sonnet 4<br/>Recommendation Gen]
        AGENTS[Domain Agents<br/>Swimming/Fitness/Nutrition]
        RERANK[Cohere Rerank<br/>Retrieval Optimization]
        OBS[Langfuse<br/>Observability]
    end
    
    subgraph "AI Layer - Implemented"
        EMB[HuggingFace BGE-M3<br/>1024-dim Embeddings]
        QDRANT[Qdrant Vector DB<br/>Semantic Search]
        CHUNK[Document Chunking<br/>PDF Processing]
        QUEUE[Background Queue<br/>Async Ingestion]
    end
    
    subgraph "Data Layer"
        SQL[(SQL Server<br/>Transactional Data)]
        VECTOR[(Qdrant<br/>Knowledge Vectors)]
    end
    
    subgraph "External Services"
        NOTIFY[SignalR<br/>Real-time Notifications]
        STORAGE[File Storage<br/>Supabase/Local]
    end
    
    MA --> API
    WD --> API
    API --> AUTH
    API --> BL
    BL --> SQL
    BL --> EMB
    BL --> QDRANT
    BL --> CHUNK
    BL --> QUEUE
    BL --> NOTIFY
    BL --> STORAGE
    
    EMB --> QDRANT
    CHUNK --> EMB
    QUEUE --> CHUNK
    QDRANT --> VECTOR
    
    SK -.Future.-> LLM
    SK -.Future.-> AGENTS
    SK -.Future.-> RERANK
    LLM -.Future.-> OBS
    AGENTS -.Future.-> QDRANT
    RERANK -.Future.-> QDRANT
    
    style SK fill:#f9f,stroke:#333,stroke-width:2px,stroke-dasharray: 5 5
    style LLM fill:#f9f,stroke:#333,stroke-width:2px,stroke-dasharray: 5 5
    style AGENTS fill:#f9f,stroke:#333,stroke-width:2px,stroke-dasharray: 5 5
    style RERANK fill:#f9f,stroke:#333,stroke-width:2px,stroke-dasharray: 5 5
    style OBS fill:#f9f,stroke:#333,stroke-width:2px,stroke-dasharray: 5 5
    style EMB fill:#9f9,stroke:#333,stroke-width:2px
    style QDRANT fill:#9f9,stroke:#333,stroke-width:2px
```

## 2. Database Entity Relationship Diagram (Conceptual)

```mermaid
erDiagram
    ApplicationUser ||--o{ Athlete : "creates (admin)"
    ApplicationUser ||--o| Athlete : "is (1-to-1)"
    ApplicationUser ||--o{ CoachAssignment : "coaches"
    ApplicationUser ||--o{ Group : creates
    ApplicationUser ||--o{ Exercise : creates
    ApplicationUser ||--o{ TrainingPlan : creates
    ApplicationUser ||--o{ NutritionPlan : creates
    ApplicationUser ||--o{ TrainingSession : conducts
    ApplicationUser ||--o{ CoachNote : authors
    ApplicationUser ||--o{ KnowledgeDocument : uploads
    
    Domain ||--o{ CoachAssignment : scopes
    Domain ||--o{ Group : scopes
    Domain ||--o{ Exercise : scopes
    Domain ||--o{ TrainingPlan : scopes
    Domain ||--o{ NutritionPlan : scopes
    Domain ||--o{ TrainingSession : scopes
    Domain ||--o{ KnowledgeDocument : scopes
    Domain ||--o{ AiRecommendation : scopes
    
    Athlete ||--o{ CoachAssignment : "assigned to"
    Athlete ||--o{ AthleteGroup : "member of"
    Athlete ||--o{ Attendance : attends
    Athlete ||--o{ TrainingRecord : performs
    Athlete ||--o{ TrainingPlanAssignment : "assigned"
    Athlete ||--o{ NutritionPlanAssignment : "assigned"
    Athlete ||--o{ CoachNote : "about"
    Athlete ||--o{ AiRecommendation : "receives"
    
    Group ||--o{ AthleteGroup : contains
    Group ||--o{ TrainingPlanAssignment : "bulk assign"
    Group ||--o{ NutritionPlanAssignment : "bulk assign"
    
    Exercise ||--o{ PlanExercise : "used in"
    TrainingPlan ||--o{ PlanExercise : includes
    TrainingPlan ||--o{ TrainingPlanAssignment : "assigned as"
    TrainingPlan ||--o{ TrainingSession : "basis for"
    
    TrainingPlanAssignment ||--o{ TrainingPlanOverride : "customized by"
    TrainingPlanAssignment ||--o{ AiRecommendation : "triggers"
    
    NutritionPlan ||--o{ NutritionPlanMeal : contains
    NutritionPlan ||--o{ NutritionPlanAssignment : "assigned as"
    NutritionPlanAssignment ||--o{ NutritionPlanOverride : "customized by"
    NutritionPlanAssignment ||--o{ AiRecommendation : "triggers"
    
    TrainingSession ||--o{ Attendance : "records"
    TrainingSession ||--o{ TrainingRecord : "generates"
    
    TrainingRecord ||--o{ ExercisePerformance : "tracks"
    TrainingRecord ||--o{ SwimmingPerformance : "tracks"
    
    PlanExercise ||--o{ ExercisePerformance : "planned vs actual"
    
    AiRecommendation ||--o{ RecommendationReview : "reviewed by"
    AiRecommendation ||--o{ RecommendationEvidence : "supported by"
    AiRecommendation ||--o{ TrainingPlanOverride : "creates"
    AiRecommendation ||--o{ NutritionPlanOverride : "creates"
    
    KnowledgeDocument ||--o{ RecommendationEvidence : "cited in"
    
    ApplicationUser {
        Guid Id PK
        string Email
        string FullName
        bool IsActive
        string Role
    }
    
    Athlete {
        Guid Id PK
        Guid UserId FK
        DateOnly DateOfBirth
        Gender Gender
        string MedicalNotes
        RegistrationStatus Status
    }
    
    Domain {
        int Id PK
        string Name
    }
    
    TrainingPlan {
        int Id PK
        string Title
        PlanSource Source
        ApprovalStatus Status
        int DomainId FK
    }
    
    TrainingSession {
        int Id PK
        int TrainingPlanId FK
        DateOnly SessionDate
        TimeOnly StartTime
        string Location
    }
    
    TrainingRecord {
        int Id PK
        Guid AthleteId FK
        int TrainingSessionId FK
        int PerformanceRating
        int FatigueLevel
        bool SessionCompleted
    }
    
    AiRecommendation {
        int Id PK
        Guid AthleteId FK
        int DomainId FK
        string RecommendationText
        RecommendationStatus Status
    }
```

## 3. RAG Pipeline Diagram (Implemented)

```mermaid
sequenceDiagram
    participant Admin
    participant API
    participant Queue as Background Queue
    participant Worker as Ingestion Worker
    participant PDF as PDF Processor
    participant Chunk as Chunking Service
    participant HF as HuggingFace BGE-M3
    participant Qdrant as Qdrant Vector DB
    participant DB as SQL Server
    
    Admin->>API: Upload PDF Document
    API->>DB: Create KnowledgeDocument<br/>(Status: Pending)
    API->>Queue: Enqueue Work Item<br/>(DocumentId + FileBytes)
    API-->>Admin: 202 Accepted<br/>(Document ID)
    
    Note over Worker: Background Processing
    
    Worker->>Queue: Dequeue Work Item
    Worker->>DB: Update Status: Processing
    Worker->>PDF: Extract Text from PDF
    PDF-->>Worker: Full Text
    Worker->>Chunk: Split into Chunks<br/>(Overlap + Size limits)
    Chunk-->>Worker: Text Chunks[]
    
    loop For Each Chunk
        Worker->>HF: Generate Embedding<br/>(1024-dim vector)
        HF-->>Worker: Embedding Vector
        Worker->>Worker: Build VectorRecord<br/>(Vector + Metadata)
    end
    
    Worker->>Qdrant: Batch Upsert<br/>(All Chunk Vectors)
    Qdrant-->>Worker: Success
    Worker->>DB: Update Status: Indexed
    
    Admin->>API: Poll Status GET /documents/{id}
    API->>DB: Fetch Document
    DB-->>API: Status: Indexed
    API-->>Admin: Document Ready
```

## 4. Competitive Analysis Matrix

```mermaid
graph TB
    subgraph Competitors["Market Landscape"]
        TU["<b>TeamUnify</b><br/>Market Leader<br/>❌ No AI<br/>❌ Swimming Only<br/>✓ Meet Management"]
        ST["<b>SwimTopia</b><br/>Modern Alternative<br/>❌ No AI<br/>❌ Swimming Only<br/>✓ Better UX"]
        MSP["<b>MySwimPro</b><br/>Consumer App<br/>⚠️ Basic AI<br/>❌ Swimming Only<br/>✓ Wearables"]
        CA["<b>Club Assistant</b><br/>Budget Option<br/>❌ No AI<br/>❌ Swimming Only<br/>✓ Affordable"]
        HT["<b>Hy-Tek</b><br/>Meet Timing<br/>❌ No AI<br/>⚠️ Swim + Track<br/>✓ Industry Standard"]
    end
    
    subgraph AquaMetrics["<b>AquaMetrics</b><br/>AI-Powered Decision Support"]
        RAG["✓ RAG-Grounded AI<br/>Evidence-Based"]
        MULTI["✓ Multi-Domain<br/>Swim + Fit + Nutrition"]
        HITL["✓ Human-in-the-Loop<br/>Coach Approval"]
        OBS["✓ Full Observability<br/>Traceability"]
        PERF["✓ Performance Tracking<br/>Session Analytics"]
    end
    
    TU -.competitor.-> AquaMetrics
    ST -.competitor.-> AquaMetrics
    MSP -.competitor.-> AquaMetrics
    CA -.competitor.-> AquaMetrics
    HT -.competitor.-> AquaMetrics
    
    style AquaMetrics fill:#9f9,stroke:#333,stroke-width:3px
    style TU fill:#fcc,stroke:#333,stroke-width:1px
    style ST fill:#fcc,stroke:#333,stroke-width:1px
    style MSP fill:#ffc,stroke:#333,stroke-width:1px
    style CA fill:#fcc,stroke:#333,stroke-width:1px
    style HT fill:#fcc,stroke:#333,stroke-width:1px
```

## 5. Authentication Flow

```mermaid
sequenceDiagram
    participant User
    participant Client as Mobile/Web
    participant API
    participant Auth as Auth Service
    participant DB as SQL Server
    
    User->>Client: Enter Credentials
    Client->>API: POST /api/auth/login<br/>{email, password}
    API->>Auth: ValidateCredentials()
    Auth->>DB: Query User + Role
    DB-->>Auth: User Found
    Auth->>Auth: Verify Password Hash
    Auth->>Auth: Generate JWT Access Token<br/>Generate Refresh Token
    Auth->>DB: Store Refresh Token
    Auth-->>API: LoginResponse<br/>(Access + Refresh Tokens)
    API-->>Client: 200 OK + Tokens
    Client->>Client: Store Tokens Securely
    Client-->>User: Redirect to Dashboard
    
    Note over Client,API: Protected API Call
    Client->>API: GET /api/athletes<br/>Authorization: Bearer {token}
    API->>API: Validate JWT
    API->>API: Check Role Claims
    API-->>Client: 200 OK + Data
    
    Note over Client,API: Token Refresh
    Client->>API: POST /api/auth/refresh<br/>{refreshToken}
    API->>Auth: ValidateRefreshToken()
    Auth->>DB: Verify Token
    Auth->>Auth: Generate New Tokens
    Auth->>DB: Update Refresh Token
    Auth-->>API: New Tokens
    API-->>Client: 200 OK + Tokens
```

## 6. Training Workflow (Actual Implementation)

```mermaid
flowchart TD
    Start([Coach Plans Training]) --> CreateEx[Create Exercises<br/>Domain-Specific Library]
    CreateEx --> CreatePlan[Create Training Plan<br/>Add Exercises + Config]
    CreatePlan --> Assign{Assign To}
    Assign -->|Individual| AssignAthlete[Assign to Athlete]
    Assign -->|Bulk| AssignGroup[Assign to Group]
    AssignGroup --> AssignAthlete
    AssignAthlete --> CreateSession[Create Training Session<br/>Date, Time, Location]
    CreateSession --> Conduct[Conduct Session]
    Conduct --> MarkAtt[Mark Attendance<br/>Present/Absent/Late]
    Conduct --> RecordPerf[Record Training Record<br/>Per Athlete]
    RecordPerf --> ExPerf[Exercise Performance<br/>Sets, Reps, Weight, RPE]
    RecordPerf --> SwimPerf[Swimming Performance<br/>Stroke, Timing, Grades]
    ExPerf --> Rate[Overall Rating<br/>Performance, Fatigue<br/>Completion, Injury]
    SwimPerf --> Rate
    Rate --> Analytics[Performance Analytics<br/>Trend Analysis]
    MarkAtt --> Analytics
    Analytics --> Review[Coach Review]
    Review --> NextCycle[Plan Next Cycle]
    
    style CreateEx fill:#e1f5ff
    style CreatePlan fill:#e1f5ff
    style AssignAthlete fill:#fff4e1
    style CreateSession fill:#ffe1f5
    style RecordPerf fill:#e1ffe1
    style Analytics fill:#f5e1ff
```

## 7. User Roles & Permissions

```mermaid
graph TB
    subgraph Roles
        Admin[<b>Administrator</b><br/>Full System Access]
        SwimCoach[<b>Swimming Coach</b><br/>Swimming Domain]
        FitCoach[<b>Fitness Coach</b><br/>Fitness Domain]
        NutSpec[<b>Nutrition Specialist</b><br/>Nutrition Domain]
        Athlete[<b>Athlete</b><br/>View Only]
    end
    
    subgraph AdminPerms["Admin Permissions"]
        A1[User Management]
        A2[Athlete Registration]
        A3[Coach Assignments]
        A4[Knowledge Base Management]
        A5[System Configuration]
    end
    
    subgraph CoachPerms["Coach Permissions"]
        C1[Create Training Plans]
        C2[Create Exercises]
        C3[Create Groups]
        C4[Assign Plans]
        C5[Schedule Sessions]
        C6[Record Attendance]
        C7[Record Performance]
        C8[Coach Notes]
        C9[View Assigned Athletes]
    end
    
    subgraph AthletePerms["Athlete Permissions"]
        AT1[View Own Profile]
        AT2[View Training Plans]
        AT3[View Performance History]
        AT4[View Notifications]
    end
    
    Admin --> A1
    Admin --> A2
    Admin --> A3
    Admin --> A4
    Admin --> A5
    
    SwimCoach --> C1
    SwimCoach --> C2
    SwimCoach --> C3
    SwimCoach --> C4
    SwimCoach --> C5
    SwimCoach --> C6
    SwimCoach --> C7
    SwimCoach --> C8
    SwimCoach --> C9
    
    FitCoach --> C1
    FitCoach --> C2
    FitCoach --> C3
    FitCoach --> C4
    FitCoach --> C5
    FitCoach --> C6
    FitCoach --> C7
    FitCoach --> C8
    FitCoach --> C9
    
    NutSpec --> C1
    NutSpec --> C4
    NutSpec --> C8
    NutSpec --> C9
    
    Athlete --> AT1
    Athlete --> AT2
    Athlete --> AT3
    Athlete --> AT4
    
    style Admin fill:#ff9999
    style SwimCoach fill:#99ccff
    style FitCoach fill:#99ff99
    style NutSpec fill:#ffcc99
    style Athlete fill:#cc99ff
```

## 8. Deployment Architecture

```mermaid
graph TB
    subgraph GitHub
        CODE[Source Code Repository]
        ACTIONS[GitHub Actions<br/>CI/CD Pipeline]
    end
    
    subgraph AzureCloud["Microsoft Azure Cloud"]
        subgraph AppService["App Service"]
            API[ASP.NET Core API<br/>Docker Container]
        end
        
        subgraph Database["Database Services"]
            SQL[(Azure SQL Database<br/>Transactional Data)]
        end
        
        subgraph Storage["Storage"]
            BLOB[Blob Storage<br/>File Storage]
        end
        
        subgraph Monitoring["Monitoring"]
            INSIGHTS[Application Insights<br/>Logging & Metrics]
        end
    end
    
    subgraph External["External Services"]
        QDRANT[Qdrant Cloud<br/>Vector Database]
        HF[HuggingFace API<br/>Embeddings]
    end
    
    subgraph Clients
        MOBILE[Flutter Mobile Apps]
        WEB[React Web Dashboard]
    end
    
    CODE --> ACTIONS
    ACTIONS -->|Build & Deploy| API
    API --> SQL
    API --> BLOB
    API --> QDRANT
    API --> HF
    API --> INSIGHTS
    
    MOBILE --> API
    WEB --> API
    
    style ACTIONS fill:#4CAF50
    style API fill:#2196F3
    style SQL fill:#FF9800
    style QDRANT fill:#9C27B0
```

## 9. AI Recommendation Workflow (Planned - Not Yet Implemented)

```mermaid
sequenceDiagram
    participant Coach
    participant API
    participant SK as Semantic Kernel
    participant Agent as Domain Agent
    participant Qdrant
    participant Rerank as Cohere Rerank
    participant LLM as Claude Sonnet 4
    participant Langfuse
    participant DB
    
    Note over Coach,LLM: ⚠️ PLANNED FEATURE - NOT YET IMPLEMENTED
    
    Coach->>API: Request AI Recommendation<br/>(Athlete + Plan)
    API->>SK: Generate Recommendation
    SK->>Agent: Select Domain Agent<br/>(Swim/Fit/Nutrition)
    Agent->>API: Collect Athlete Context
    API->>DB: Query Athlete Data
    DB-->>API: Performance, Notes, History
    API-->>Agent: Athlete Context
    
    Agent->>Qdrant: Semantic Search<br/>(Query + Domain Filter)
    Qdrant-->>Agent: Top K Documents
    Agent->>Rerank: Re-rank Results
    Rerank-->>Agent: Optimized Evidence
    
    Agent->>LLM: Generate Recommendation<br/>(Context + Evidence)
    LLM->>Langfuse: Log Execution Trace
    LLM-->>Agent: Recommendation + Rationale
    
    Agent->>DB: Store Recommendation<br/>(Status: Pending)
    Agent-->>Coach: Recommendation Ready
    
    Coach->>API: Review Recommendation
    API-->>Coach: Show Evidence + Rationale
    Coach->>API: Approve/Modify/Reject
    API->>DB: Update Status
    
    alt Approved
        API->>DB: Create Plan Override
        API-->>Coach: Applied to Athlete
    else Rejected
        API->>DB: Mark Rejected
        API-->>Coach: Discarded
    end
    
    rect rgb(255, 220, 220)
        Note over SK,Langfuse: This workflow is designed but not<br/>yet implemented in the codebase
    end
```

## 10. Technology Stack Summary

```mermaid
graph LR
    subgraph Frontend["Presentation Layer"]
        F1[Flutter<br/>Mobile]
        F2[React<br/>Web Dashboard]
    end
    
    subgraph Backend["Application Layer"]
        B1[ASP.NET Core<br/>.NET 10]
        B2[Entity Framework<br/>Core]
        B3[JWT Auth]
        B4[SignalR]
    end
    
    subgraph Data["Data Layer"]
        D1[(SQL Server)]
        D2[(Qdrant<br/>Vector DB)]
    end
    
    subgraph AI["AI Services - Implemented"]
        A1[HuggingFace<br/>BGE-M3]
        A2[PdfPig<br/>Extraction]
    end
    
    subgraph AIPlanned["AI Services - Planned"]
        P1[Semantic Kernel]
        P2[Claude Sonnet 4]
        P3[Cohere Rerank]
        P4[Langfuse]
    end
    
    subgraph Infra["Infrastructure"]
        I1[Azure Cloud]
        I2[Docker]
        I3[GitHub Actions]
    end
    
    F1 --> B1
    F2 --> B1
    B1 --> B2
    B1 --> B3
    B1 --> B4
    B2 --> D1
    B1 --> A1
    B1 --> A2
    A1 --> D2
    
    B1 -.future.-> P1
    P1 -.future.-> P2
    P1 -.future.-> P3
    P2 -.future.-> P4
    
    B1 --> I1
    I1 --> I2
    I2 --> I3
    
    style AIPlanned fill:#ffe6e6,stroke:#333,stroke-width:2px,stroke-dasharray: 5 5
    style AI fill:#e6ffe6,stroke:#333,stroke-width:2px
```
