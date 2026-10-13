[README.md](https://github.com/user-attachments/files/33367643/README.md)
# ASMS NextGen — Agile Sprint Management System

A self-contained classroom prototype designed for Google Colab, based on the research paper **“Design and Development of an Agile Sprint Management System for Enhanced Sprint Monitoring and Team Performance Analysis.”**

## Start in Google Colab

1. Open `ASMS_NextGen_Colab.ipynb` in Google Colab.
2. Run each cell from top to bottom.
3. The notebook installs Flask/Werkzeug, writes `app.py` and `templates/index.html`, creates the SQLite database with demo data, starts the web server and opens the application in a new browser tab.
4. The app is served on port **5001** so it can coexist with an older copy of the project on port 5000.

## Demo accounts

| Role | Email | Password |
|---|---|---|
| Administrator | `admin@asms.local` | `admin123` |
| Product Owner | `po@asms.local` | `po123` |
| Scrum Master | `sm@asms.local` | `sm123` |
| Developer | `dev1@asms.local` | `dev123` |
| Developer | `dev2@asms.local` | `dev123` |
| Developer | `dev3@asms.local` | `dev123` |
| Developer | `dev4@asms.local` | `dev123` |

These are demonstration-only credentials. Do not use them for real or sensitive data.

## Working modules

- **Overview dashboard:** sprint progress, story-point velocity, task-status distribution, capacity overrun warnings, sprint-health status, workload, recent activity and tasks needing attention.
- **My Work:** individual tasks for the signed-in contributor.
- **Task Board:** Kanban workflow with drag-and-drop status changes, task search, task editing, due-date indicators and required blocker reasons.
- **Product Backlog:** user stories, descriptions, acceptance criteria, MoSCoW priority, estimates, editing and CSV export.
- **Sprint Planning:** sprint goals, start/end dates, capacity in story points, planned/active/completed lifecycle, backlog-to-task planning and over-capacity warnings.
- **Analytics & Reports:** completion rates, velocity, team workload, health indicators, sprint history and CSV export.
- **Activity Timeline:** audit-style feed of task, story, sprint, project and team changes, with CSV export.
- **Team & Settings:** role list, project workspaces, admin-only account creation/deactivation and project creation.
- **Task discussion:** comments with contributor name, role and timestamp.
- **Security:** hashed passwords, server-side role checks, session authentication, CSRF token validation for state-changing API requests, and basic response security headers.

## Stack and architecture

- Frontend: HTML, CSS and vanilla JavaScript; responsive, Asana-inspired workspace UI with a dark navy sidebar, purple accent gradients and teal status signals.
- Backend: Python and Flask JSON API.
- Persistence: SQLite relational database.
- Visuals: CSS-rendered status distribution/velocity/workload visuals; no chart library is required.

The code follows a layered arrangement: presentation (HTML/CSS/JS), application/API (Flask routes and business rules), security (session, password hashing, role checks, CSRF), and data layer (SQLite tables and relationships).

## Database entities

`users`, `projects`, `project_members`, `backlog_items`, `sprints`, `tasks`, `task_comments`, `activity_logs`.

Relationships include one project to many sprints and backlog items; one sprint to many tasks; tasks can refer to a backlog item and an assignee; one task to many comments; project events to activity-log rows; projects and users have a many-to-many relationship through `project_members`.

## Workflow rules

Allowed task state transitions:

- To Do → In Progress or Blocked
- In Progress → To Do, Testing or Blocked
- Testing → In Progress, Blocked or Done
- Blocked → Testing (after the blocker is resolved)
- Done → In Progress (reopen)

When a task is marked Blocked, the user must provide a reason. The backend rejects invalid state transitions. Dashboard health considers blockers, overdue tasks, stale tasks and a sprint whose estimated scope exceeds its capacity. A task is considered stale when it has not been updated for more than 3 days.

Metric definitions:

- Task completion = completed tasks / sprint tasks × 100.
- Points completion = points on completed tasks / total points × 100.
- Velocity = sum of story points for tasks marked Done.
- Capacity overrun = max(0, sprint task points − sprint capacity).

## Software Engineering concepts demonstrated

1. Agile and Scrum: product backlog, sprint planning, task execution and iteration review.
2. Requirement engineering: user stories, descriptions, acceptance criteria, MoSCoW prioritization and story-point estimates.
3. UML/state-transition modelling: the role interactions and task lifecycle are reflected in the application rules.
4. Layered architecture and separation of concerns: interface, API/business logic, security and database.
5. RBAC: each role has a different set of permissions, checked by the backend and reflected in the UI.
6. Relational database design: entities, keys, foreign keys, relationships and indexed search fields.
7. State machines: valid task transitions are defined in `TRANSITIONS` and enforced by the API.
8. Software metrics: completion, velocity, workload, stale/overdue detection, health and capacity usage.
9. Auditability and collaboration: activity events and task comments.
10. Input validation and defensive security: bounded fields, password hashing, CSRF checks, SQL parameters and CSV formula-injection mitigation.

## Persistence and limitations

Google Colab runtimes are temporary. The database normally lives at `/content/asms_nextgen/asms.db` and can be lost when the runtime is discarded. Use the optional backup cell in the notebook to download a SQLite backup before ending the session.

This is an academic prototype, not a production-hosted multi-user service. It does not implement password-reset/email flows, account lockout/rate limiting, external OAuth, permanent cloud storage, notification email/push delivery or production HTTPS-only cookie/security configuration. Colab's forwarded port is for demonstrations; do not enter personal, confidential or sensitive data.

## Validation notes

The Python backend was syntax-checked with `py_compile`, and the frontend JavaScript was syntax-checked with Node.js. Full live integration was not run in the build sandbox because Flask could not be installed there; the Colab notebook includes the dependency-install and server-start steps.
