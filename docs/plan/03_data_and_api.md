# Data model and HTTP contract

Project: id (positive integer PK), name, description, created_at (UTC ISO timestamp).
Task: id (positive integer PK), project_id (FK), title, owner, due_date (nullable ISO date), status (enum), created_at, updated_at (UTC ISO timestamps).

Response shape: individual resources are bare objects with all their fields; lists are JSON arrays. Errors: {"error": {"code": "validation_error", "message": "Human readable explanation"}} (code may vary by error category). DELETE success returns 204 with no body. All other successful API responses are JSON. Never return stack traces.

- GET /health → 200 {"status":"ok"}.
- POST /api/projects with {"name":"Website refresh","description":"Q4 launch"} → 201 project.
- GET /api/projects → 200 project array, including total_tasks, completed_tasks, overdue_tasks integer summary fields on each project.
- POST /api/projects/{id}/tasks with {"title":"Draft homepage","owner":"Alex","due_date":"2026-10-01","status":"todo"} → 201 task. Only title is mandatory.
- GET /api/tasks?project_id={id}&status=todo → 200 task array. Both filters optional; each may appear at most once. Reject unknown query parameters.
- PATCH /api/tasks/{id} with any nonempty subset of title/owner/due_date/status → 200 updated task.
- DELETE /api/tasks/{id} → 204.

Return 404 for absent resources and unknown paths, 405 for unsupported methods on known routes. Do not expose SQLite files or repository documents as static content. Dates and summaries should share an injectable UTC clock for deterministic tests.
