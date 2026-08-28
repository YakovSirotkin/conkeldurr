# AGENTS.md

Guidance for AI coding agents working in this repository.

## Project Overview

This project provides a **queue-over-database** system with parameter names
inspired by Amazon SQS. There is no support for FIFO queues. The queue is backed
by a database table rather than a dedicated messaging broker.

- The system can have multiple implementations. Each implementation consists of
  **a library + a database**.
- The **primary implementation** is a **Python library** backed by
  **PostgreSQL**.
- **License:** MIT (see `LICENSE`)

## Issue Tracker

Work is tracked as GitHub issues in
[YakovSirotkin/conkeldurr](https://github.com/YakovSirotkin/conkeldurr/issues).

Use the `gh` CLI (already authenticated) rather than the web UI:

```bash
gh issue list                # open work
gh issue view <n>            # read an issue
gh issue comment <n> -b ...  # record progress
gh issue close <n>           # close when the work is merged
```

- Before starting a task, check whether an issue already covers it; if not,
  open one so the work is traceable.
- Reference the issue in commit messages (e.g. `Fixes #2`) so it closes on merge.
- Issue bodies are often empty — the title is the spec. Ask before inferring
  additional scope.

## Architecture

- **Library:** Encapsulates the queue-over-database logic.
- **Microservice:** Wraps the library and exposes the REST API defined in
  `openapi.yaml`. Used to exercise and test the implementation.
- **Test suite:** Implemented in Python; uses **Testcontainers** to spin up the
  microservice and the database, then runs the tests against them.

## API (`openapi.yaml`)

The API contract is defined in `openapi.yaml`.

- **Central schema — `message`:** Carries the parameters with names inspired by Amazon SQS.
  By design there is **no `queue` schema**; the queue name is a
  **required parameter of the message**.
- **Main endpoints:**
  - `sendMessage` — add messages to a queue.
  - `getMessage` — used by workers to receive/process messages.
  - `deleteMessage` — delete a message after it has been successfully processed.
- **Admin endpoints:** Allow direct querying and modification of the database
  table. Intended for testing purposes.

## Conventions

- Treat `openapi.yaml` as the source of truth for the API contract; keep the
  microservice and tests in sync with it.
- Remember there is intentionally no separate queue entity — the queue name
  lives on the message.
- Preserve the MIT license notice.

## Build / Test / Lint

Document the canonical commands here as the implementation lands, for example:

```bash
# run the test suite (Python + Testcontainers)
# lint / format the Python code
```
