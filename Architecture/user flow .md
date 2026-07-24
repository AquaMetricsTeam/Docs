# AquaMetrics – Swimming Training Management Workflow

## Module Overview

The Swimming Training Management module enables Swimming Coaches to create reusable training plans, organize exercises, assign plans to individual athletes or groups, manage daily training sessions, record attendance, and later integrate AI-powered recommendations.

The module is designed around the following philosophy:

> **Create Once → Reuse Many → Customize When Needed**

---

# Complete Workflow

```text
Coach

↓

Training Management

↓

Create Training Plan

↓

Add Exercises

↓

Save Template

↓

Assign Template

↓

Athlete / Group

↓

Athlete Receives Plan

↓

Create Training Session

↓

Record Attendance

↓

AI Recommendation (Future)

↓

Override Plan (Optional)
```

---

# Step 1 — Open Training Management

The coach navigates to:

```
Dashboard
    ↓
Swimming Training
```

The coach should see three primary sections:

- Training Templates
- Assigned Plans
- Training Sessions

This allows the coach to separate planning from execution.

---

# Step 2 — Create a Training Template

The coach clicks:

```
+ New Training Plan
```

The system asks for the plan information.

## Required Information

- Title
- Objectives
- Source
- Domain

Example

```
Title:
Sprint Development Week 1

Objectives:
Improve explosive starts and sprint endurance.

Source:
Coach Ahmed Library

Domain:
Swimming
```

After entering the basic information, the coach proceeds to build the exercises.

---

# Step 3 — Build the Training Plan

The coach adds exercises from the Exercise Library.

For every exercise the coach specifies:

- Exercise
- Sets
- Repetitions
- Intensity
- Duration
- Notes
- Order

Example

```
Exercise:
Freestyle Kick

Sets:
4

Reps:
50m

Intensity:
Medium

Duration:
10 minutes

Notes:
Focus on breathing every 3 strokes.
```

The coach repeats this process until the complete training session is built.

The system stores:

- TrainingPlan
- PlanExercise

---

# Step 4 — Save Training Template

Once all exercises are added, the coach saves the template.

The template now becomes reusable.

The coach should be able to:

- View
- Edit
- Duplicate
- Archive

Example Template List

```
Sprint Training

Technique Training

Endurance Training

Recovery Session
```

---

# Step 5 — Assign the Training Plan

The coach opens a saved template and clicks:

```
Assign
```

The system asks:

```
Assign To:

○ Individual Athlete

○ Athlete Group
```

---

## Option A — Assign to Individual Athlete

The coach searches athletes.

Example

```
Ahmed Hassan

Ali Mohamed

Omar Ibrahim
```

The coach selects:

```
Ahmed Hassan
```

Then presses:

```
Assign
```

Database

```
TrainingPlanAssignment

PlanId

AthleteId

AssignedBy

AssignedDate
```

---

## Option B — Assign to Athlete Group

The coach selects an athlete group.

Example

```
Senior Team

U16 Team

Beginners
```

The coach chooses

```
Senior Team
```

and assigns the plan.

Database

```
TrainingPlanAssignment

PlanId

GroupId

AssignedBy

AssignedDate
```

Each athlete in the group now receives the assigned training plan.

---

# Step 6 — Athlete Receives Assigned Plan

The athlete opens the Mobile Application.

The athlete should immediately see:

```
Today's Training

Sprint Development Week 1

Today
6:00 PM
```

The athlete can only:

- View the plan
- Read exercises
- View notes

The athlete cannot modify the training plan.

---

# Step 7 — Optional Training Override

Sometimes one athlete cannot follow the original plan.

Example

Ahmed has a shoulder injury.

Instead of modifying the template, the coach creates an override.

Coach opens:

```
Ahmed Hassan

↓

Sprint Development Week 1

↓

Override
```

Example Override

Original Exercise

```
Butterfly
```

Override

```
Freestyle
```

Database

```
TrainingPlanOverride

AssignmentId

RecommendationId

OverrideDetails
```

The original template remains unchanged.

Only Ahmed receives the customized version.

---

# Step 8 — Create Daily Training Session

On training day, the coach creates today's session.

Information includes:

- Date
- Coach
- Domain

Database

```
TrainingSession

SessionDate

CoachId

DomainId
```

This session represents today's physical practice.

---

# Step 9 — Record Attendance

Once training begins, the coach opens today's session.

Example

```
Ahmed Hassan

Ali Mohamed

Omar Ibrahim

Youssef Mahmoud
```

For each athlete the coach records:

- Present
- Absent
- Late
- Excused

Database

```
Attendance

AthleteId

SessionId

Status

RecordedBy

RecordedAt
```

---

# Step 10 — AI Recommendation (Future Sprint)

After the training session, the AI analyzes:

- Attendance
- Previous performance
- Previous plans
- Coach notes
- Athlete history

The AI generates recommendations.

Example

```
Reduce sprint volume by 15%.

Increase recovery interval.

Replace Butterfly with Freestyle.
```

The coach reviews the recommendation.

The coach can:

- Approve
- Reject
- Modify

Only approved recommendations affect future training assignments.

---

# Recommended Frontend Wizard

Instead of forcing coaches to save first then assign later, the frontend should implement a wizard.

```
Step 1

Training Information

↓

Step 2

Exercises

↓

Step 3

Assignment

↓

Finish
```

Internally, the backend still creates:

```
TrainingPlan

↓

PlanExercise

↓

TrainingPlanAssignment
```

But the coach experiences one smooth workflow.

---

# User Permissions

## Swimming Coach

Can

- Create templates
- Edit templates
- Duplicate templates
- Assign templates
- Create training sessions
- Record attendance
- Create overrides
- Approve AI recommendations

Cannot

- Delete athlete accounts
- Modify other coach templates

---

## Athlete

Can

- View assigned plans
- View training history

Cannot

- Edit plans
- Delete plans
- Modify attendance
- Approve recommendations

---

## Administrator

Can

- View all plans
- View assignments
- View attendance
- Manage users

Normally does not create training plans.

---

# Database Flow

```
TrainingPlan
        │
        ▼
PlanExercise
        │
        ▼
TrainingPlanAssignment
        │
        ├────────────► Athlete
        │
        └────────────► Group
                            │
                            ▼
                        Athletes

TrainingSession
        │
        ▼
Attendance

TrainingPlanAssignment
        │
        ▼
TrainingPlanOverride

Future

Attendance
Coach Notes
Performance
        │
        ▼
AI Recommendation
        │
        ▼
TrainingPlanOverride
```

---

# Future Enhancements

The following features are intentionally left for future sprints:

- AI-generated training plans
- AI-generated overrides
- Athlete performance analytics
- Training completion tracking
- Session reports
- Coach approval workflow
- Plan version history
- Notifications when new plans are assigned
- Calendar integration
