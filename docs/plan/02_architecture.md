# Architecture and stack constraints

Use Python 3.12+ standard library: ThreadingHTTPServer, sqlite3, unittest, and pathlib. Use HTML, CSS, and vanilla JavaScript for the UI. Runtime must have no third-party dependencies. Organize code in a taskdesk Python package, static assets, and tests. Do not build the application as a single opaque generated file.

Layers: HTTP request handler → input validation and project/task services → SQLite repository. Frontend calls JSON HTTP API under /api on the same origin. Static files served from a fixed public directory with path traversal prevention. Limit request bodies to 64 KiB, return 413 for excess; reject malformed JSON with 400 and unsupported media type with 415. JSON must be an object; reject unknown writable fields, invalid types (including booleans as IDs), blank required strings, and invalid calendar dates.

A SQLite connection per operation/request supports ThreadingHTTPServer safely. Use busy timeout and transactions; protect schema creation. Document WAL choice and connection cleanup. Do not log secrets or full submitted payloads. Log HTTP request outcome and duration using logging.

Before implementation, codefactory must verify standard-library behavior using official Python and SQLite documentation and record URLs and design implications in docs/design/research.md. Resolve unspecified internal choices in docs/design/low-level-design.md without weakening requirements. Include module boundaries, schema SQL, request validation, response contracts, failure handling, UI state transitions, concurrency, and requirement-to-test traceability.
