# Frontend Guided Tour

## Overview
The guided tour introduces key workflows (projects, import, sprint board, ticket details) and is designed to work reliably across route changes.

Implementation lives in `frontend/jenga/src/provider/GuideProvider.tsx` using Shepherd.js.

## Library: Shepherd.js
Shepherd.js is a library for guided tours. It models a tour as a sequence of steps that can be attached to DOM elements, with built-in navigation controls and lifecycle hooks.

In this frontend, Shepherd.js is used to:
- define route-aware tours (steps can trigger navigation before attaching)
- attach steps to stable UI anchors (via element selectors)
- synchronize step rendering with asynchronous UI updates (waiting for DOM targets)

## Runtime Design
`GuideProvider` builds a single tour instance with translated default button labels.

Key helpers:
- `waitForElement(selector, timeoutMs)`
- `goToPathAndWaitFor(path, selector)`
- `ensureSidebarVisible()`

These helpers are the core stability layer for asynchronous UI and routing.

## Step Orchestration Strategy
Each step may define `beforeShowPromise`.

Typical sequence:
1. navigate to target route
2. ensure required shell state (e.g. sidebar open)
3. wait for anchor element
4. render step

Without this, tour steps race against rendering and fail intermittently.

## Anchor Contract
Guide steps depend on stable DOM ids:

- `guide-nav-toggle`
- `guide-sidebar`
- `guide-projects`
- `guide-file-import`
- `guide-ticket-filter`
- `guide-kanban`
- `guide-backlog`
- `guide-ticket-details`

Anchor consistency:
- if an anchor id changes, the guide configuration is updated in the same change

## i18n Dependency
Guide labels and step content use i18n keys.

Implication:
- `GuideProvider` depends on `I18nProvider` being mounted first
- missing translation keys degrade UX immediately (button labels/step texts)

## Failure Modes and Current Mitigations
### Missing element
Symptom:
- step throws "Guide target not found"

Mitigation in current implementation:
- ensure route and conditional rendering create target element
- keep id anchors in stable, always-mounted wrappers where possible

LayoutProvider dependency:
- Some tour targets exist only when a specific layout branch is rendered. In the current UI, sidebar-bound targets (e.g. `guide-sidebar`) depend on `LayoutProvider.sidebarOpen`.
- The tour therefore depends on `LayoutProvider` being mounted and its state being controllable from the tour logic, otherwise selectors can be correct but still resolve to no DOM element.

### Route/render timing mismatch
Symptom:
- a step appears before the target route content is present

Mitigation in current implementation:
- route-dependent steps use a navigation + wait strategy (`goToPathAndWaitFor(...)`)


## Evolution Characteristics
When new guided steps are added, the implementation typically evolves as follows:
1. define stable anchor id in UI component
2. add translated strings in both locales
3. add step with explicit preconditions (`beforeShowPromise`)
4. validate complete tour in both languages
