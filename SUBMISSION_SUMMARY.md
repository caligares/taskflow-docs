# Submission Summary — TaskFlow API Documentation

## Process

I started by reading the OpenAPI spec in full before writing any content. The spec revealed five endpoints with a clear dependency chain (projects → tasks), three task statuses, and a consistent error format. I also tested the live API to confirm the spec matched actual behavior, including the cold-start latency on the first request.

From that reading, I identified two distinct documentation needs: a **reference layer** (what each endpoint does, its parameters, its responses) and a **conceptual layer** (why the API is structured this way and how to use it end-to-end). I built both.

## Structure decisions

I organized the documentation into three groups:

**Getting Started** — Introduction, Quickstart, Authentication. These are the first pages a new user hits. The Quickstart walks through all four steps of the real workflow (create project → create task → update status → list tasks) with curl, Python, and JavaScript examples side by side. Authentication is a dedicated page because it applies to every request and has security implications worth explaining separately.

**Guides** — Core Workflow, Task Statuses, Errors. These are the conceptual pages that explain *why*, not just *what*. The Core Workflow guide makes the dependency chain explicit, since it is the most likely source of confusion for first-time users. Task Statuses explains the three values, common invalid values people try, and how to move between them. Errors gives a full reference for the three status codes the API returns, with handling examples in code.

**API Reference** — One page per endpoint, generated from the OpenAPI spec using Mintlify's `openapi:` frontmatter field. These auto-render request parameters, response schemas, and example payloads directly from the spec, so they stay in sync with the source of truth.

## What I would do differently with more time

- **Test more edge cases** on the live API — for example, what happens if you PATCH a task with an empty object, or send extra fields the spec doesn't mention. The answers would be worth documenting.
- **Add a "Common mistakes" section** to the Introduction or a dedicated troubleshooting page, covering things like the 60-second cold start, forgetting to save `project_id` after creating a project, and the `Bearer ` prefix requirement.
- **Add response examples inline** on the endpoint reference pages, beyond what the OpenAPI spec provides, to make the reference pages more self-contained.
- **Consider an SDK or code library section** — even a minimal one showing how to wrap the API in a reusable function — would improve developer experience.

## Challenges

The main challenge was the cold-start latency. The first request to the live API took over 45 seconds, which can look like a failure. I made sure to note this prominently in the Introduction, Quickstart, and API Reference overview so users do not give up before the API responds.

The Mintlify `openapi:` auto-generation is powerful but requires exact matching between the `operationId` or method+path string and what is in the spec. I used the method+path format (e.g. `"GET /projects/{project_id}/tasks"`) for clarity, which is more readable than relying on operationId values.
