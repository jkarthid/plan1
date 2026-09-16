# Executable acceptance specification

Implement automated tests and run them with: python3 -m unittest discover -s tests -v.
Test against the application's real HTTP server and a temporary SQLite database; no mocked successful service responses. Also test repository and validation boundaries when appropriate.

A1 (R1): create project, list it, assert original fields, positive ID, valid UTC created_at, and zero summaries.
A2 (R2): create task with default status; list it under its project; assert all fields and summaries. Unknown parent → 404.
A3 (R3): patch owner/status/date, clear date with null, assert ID and project unchanged; delete → 204 then PATCH/DELETE → 404.
A4 (R4): two projects, several task statuses; verify project-only, status-only, combined filters, and empty results. Unknown project → 404, invalid status/duplicate or unknown query parameter → 400.
A5 (R5): freeze UTC date: yesterday todo overdue; today todo not overdue; yesterday done not overdue. Counts reflect task changes and deletion.
A6 (R6): restart the server with the same database; assert projects/tasks and values remain. Concurrent independent writes do not lose data and leave valid foreign keys.
A7 (R1–3): reject whitespace-only or overlength title/name, numeric strings where IDs are invalid, boolean/object/null field values, malformed calendar dates, invalid statuses, unknown fields, empty PATCH, array JSON, malformed JSON. Database remains unchanged after rejected writes.
A8: oversized body → 413; non-JSON POST → 415; unknown route → 404; wrong HTTP method on known route → 405; traversal attempts cannot read outside static directory; SQL-like names store literally; HTML-like titles display as text.
A9 (R8): health endpoint passes, configurable host/port/db work. Fresh temporary runtime database needs no external credentials or dependencies.
A10 (R7): browser exercise at desktop and 390px: create project, add task, edit status, filter, delete with confirmation, reload and verify persistence. Confirm labels and visible keyboard focus. Capture screenshots and record results in docs/validation. Browser automation may be run by the supervising factory harness without adding a runtime dependency.

Delivery gate: passing tests, actual browser results, documented test counts/commands, no secrets or runtime DB committed. Do not mark incomplete tests as passing. Include a requirement-to-test mapping.
