# Frontend API Integration

## Contract Pipeline
The frontend depends on backend OpenAPI contract updates.

Pipeline:
1. build/start backend
2. export or copy latest OpenAPI schema
3. update `frontend/jenga/openapi.yaml`
4. run `npm --prefix frontend/jenga run gen:api`
5. build frontend

Command details are documented in [Build Frontend](../../installation/BuildFrontend.md).

## Generated Client Policy
`frontend/jenga/src/api` is generated code.

Characteristics:
- generated files are treated as build artifacts and are not edited manually
- regeneration happens on every DTO/endpoint change
- business logic is implemented outside the generated layer (providers/pages/components)

## Runtime API Wiring
At runtime:
- `OpenAPI.BASE` is set from browser origin
- auth token/user are assigned by `AuthProvider`
- Vite dev proxy forwards `/api` to backend (`localhost:8080`)

## Request Mapping Logic
Before ticket update, provider maps frontend ticket model into request DTO.

Normalization currently includes:
- acceptance criteria: trim and filter empty descriptions before sending
- relation ids: relation synchronization deduplicates and removes invalid/self ids before computing diffs and calling add/remove endpoints

This keeps acceptance criteria payloads clean and prevents invalid/self edges from being requested through relation endpoints.

## Why Update Is Multi-Step
A ticket save is not a single endpoint operation.

### Step 1: Base ticket fields
`PUT /api/tickets/{id}` updates core fields (title, description, status, etc.).

### Step 2: Acceptance criteria synchronization
Provider currently performs replace-style synchronization:
- read existing criteria
- delete existing entries
- create new entries from current frontend state

Rationale:
- deterministic server state from full frontend snapshot

Consequence:
- more API calls than patch-style updates

### Step 3: Relation synchronization
For `related`, `blocking`, and `blocked` IDs provider computes set differences.

Procedure:
- fetch current relation state
- compare `current` vs `next`
- call add/remove endpoints only for changed edges

This minimizes unnecessary requests while ensuring bidirectional consistency rules are applied through backend relation endpoints.

## Failure and Rollback Model
Provider keeps pre-update snapshots of ticket collections.

On failure:
- local optimistic state is restored
- error is rethrown to UI layer
- UI shows domain-specific feedback (e.g. save failed alert)

## Why This Integration Design Exists
- one centralized write orchestrator (`ProjectProvider`)
- consistent mutation semantics from all entry points (form, DnD, dialogs)
- strong typing and enum safety via generated DTO client
