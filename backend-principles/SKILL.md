---
name: backend-principles
description: "Use when: designing backend structure, organizing business logic, choosing layers, separating controller/service/repository responsibilities, or deciding whether DDD is warranted."
user-invocable: false
---

# Backend Architecture Preferences

Follow the project's existing architecture and explicit task constraints. For a prototype or a small direct endpoint, keep the implementation together until separate responsibilities justify layers. Do not introduce layers just to satisfy a folder pattern.

## My Preferred Layered Structure

When a backend needs layers, I prefer Controller / Service / Repository over traditional MVC:

| Layer | Responsibility |
| --- | --- |
| Controller | HTTP input/output and short orchestration of services |
| Service | One business capability; accesses repositories/gateways in its domain |
| Repository | Data access and persistence queries |
| Use case / workflow | Multi-step orchestration that is complex or reused beyond HTTP |

Within this structure, keep services from calling other services. Compose them in a controller or use case so the business flow stays visible and service dependencies stay acyclic. When extending a project with a different established structure, follow it rather than restructuring unrelated code.

Extract orchestration when its responsibilities or reuse justify it, not at a fixed line count. Keep transaction ownership, partial failure recovery, and retry behavior explicit in the orchestrating operation; moving calls between layers does not solve consistency.

## DDD

Adopt domain modeling when it helps express complex business rules, state transitions, consistency boundaries, or conflicting meanings across subsystems. Start with the specific domain concept that clarifies the problem; add bounded contexts or other DDD structure as those boundaries become real.

For CRUD, prototypes, and stable simple rules, skip DDD machinery. Team size, file length, and the number of folders are not reasons to adopt it.
