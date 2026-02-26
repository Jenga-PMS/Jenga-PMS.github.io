# Frontend Architecture

## Overview
This section documents the architecture of `frontend/jenga`. It focuses on runtime structure, state and data flow, and the design rationale behind the current implementation.

## Core Technologies
The frontend is implemented as a SolidJS single-page application with a generated OpenAPI client and a small set of cross-cutting providers.

- **Language:** TypeScript
- **UI framework:** SolidJS
- **Routing:** @solidjs/router
- **UI components:** SUID Material
- **Build tooling:** Vite
- **API client:** OpenAPI-generated TypeScript client (`src/api`)
- **Localization:** i18next
- **Guided tour:** Shepherd.js
- **PWA:** vite-plugin-pwa

## Read This Section Efficiently
This page is an index. Detailed topics are documented separately:

- [Web App Walkthrough](frontend/walkthrough.md): user-facing walkthrough of the UI and typical workflows
- [System Design](frontend/system_design.md): static structure, provider order, module boundaries
- [State and Data Flow](frontend/state_data_flow.md): reactive state lifecycle, update flows, rollback behavior
- [API Integration](frontend/api_integration.md): OpenAPI generation, update orchestration, relation sync logic
- [Testing](frontend/testing.md): frontend test setup, run commands, and testing libraries
- [UI and PWA](frontend/ui_i18n_pwa.md): shell rendering model, component responsibilities, PWA baseline
- [Internationalization (i18n)](frontend/i18n.md): initialization lifecycle, translation boundaries, localization behavior
- [Guided Tour](frontend/guided_tour.md): route-aware Shepherd orchestration, anchor strategy, failure modes

## Architectural Intent
The frontend follows one core rule:

`UI interaction -> provider action -> API update -> state reconciliation -> reactive rerender`

This keeps API orchestration centralized and prevents domain logic from being duplicated in view components.

## Primary Constraints
- API client is generated and treated as build output (no manual edits).
- Backend contract is built first and then consumed by frontend generation.
- Shared cross-page logic is implemented in providers rather than pages/components.

## Current Focus Areas
In the current implementation, the following areas are the most active complexity points:

- consistency of ticket link synchronization (`related`, `blocking`, `blocked`)
- minimizing optimistic-update drift after failed requests
- expanding PWA behavior beyond installability baseline
