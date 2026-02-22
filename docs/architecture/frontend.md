# Frontend Architecture

## Scope
This section documents the architecture of `frontend/jenga`.

Goal:
- avoid duplicate descriptions across pages
- explain why current design decisions exist
- describe the critical runtime logic and tradeoffs

## Read This Section Efficiently
Use this page as index only. Detailed explanations are intentionally separated:

- [System Design](frontend/system_design.md): static structure, provider order, module boundaries
- [State and Data Flow](frontend/state_data_flow.md): reactive state lifecycle, update flows, rollback behavior
- [API Integration](frontend/api_integration.md): OpenAPI generation, update orchestration, relation sync logic
- [UI, i18n and PWA](frontend/ui_i18n_pwa.md): rendering model, localization lifecycle, guide behavior, PWA limits

## Architectural Intent
The frontend follows one core rule:

`UI interaction -> provider action -> API update -> state reconciliation -> reactive rerender`

This keeps API orchestration centralized and prevents domain logic from being duplicated in view components.

## Primary Constraints
- API client is generated and must not be manually edited.
- Backend contract must be built first, then consumed by frontend generation.
- Shared cross-page logic must live in providers, not in pages/components.

## Current Focus Areas
- consistency of ticket link synchronization (`related`, `blocking`, `blocked`)
- minimizing optimistic-update drift after failed requests
- expanding PWA behavior beyond installability baseline
