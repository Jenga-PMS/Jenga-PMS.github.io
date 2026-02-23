# Web App Walkthrough (Usage)

## Overview
This document describes how the Jenga web application is operated from a user perspective. It follows the main UI areas and the typical workflow: authenticate, select a project, work in the sprint workspace, and update tickets.

It intentionally describes runtime behavior as it is observed in the current implementation (for example, which parts update immediately, which actions write to the server, and which UI areas are driven by selection state).

## Global Layout and Navigation
The application uses a shell layout consisting of:

- an app bar with global actions (navigation toggle, language, guided tour, authentication)
- a sidebar for navigation (can be collapsed)
- a page outlet where the active route is rendered
- a footer

Routes visible in the current UI:

- `/` (Home): project selection and import
- `/Sprint`: sprint workspace for tickets
- `/Profile`: current user profile
- `/About`, `/Privacy`: static pages

## Authentication (Login/Logout)
Authentication is required for domain pages (projects, tickets) to load.

Observed behavior:

- after successful login, the UI loads user and project domain state automatically
- on logout, domain state is cleared and views converge back to an unauthenticated baseline

## Home: Project Selection and Import
The home page is the entry point for selecting which project is active.

Typical interactions:

- selecting a project activates it as the current working context
- importing data (if available in the UI) updates the project/ticket domain state for the selected project

Observed behavior:

- switching the selected project changes which tickets are loaded in `/Sprint`
- the selected ticket (details sidebar) is reset when the project context changes

## Sprint Workspace (Tickets)
The sprint page is the main operational screen. It is composed of:

- `TicketFilters`: filter rows for narrowing the visible ticket set
- `Kanban`: status-oriented ticket board with drag-and-drop
- `Backlog`: list-style ticket view (non-board)
- `TicketInfo`: details panel for the currently selected ticket

### Ticket Selection
Selecting a ticket updates the global selection state. The `TicketInfo` panel renders the current selection and enables editing.

Observed behavior:

- selection is shared across sprint widgets: selecting in one place updates the details panel
- if no ticket is selected, the details panel stays in an empty/placeholder state

### Filtering
Filters are local UI state and affect what is shown in the sprint workspace.

Observed behavior:

- filters do not modify server state
- the filter UI keeps at least one empty row available

### Kanban Drag-and-Drop
Tickets can be moved across columns using drag-and-drop.

Observed behavior:

- the drag-and-drop operation routes through the same update path as form edits (single provider orchestrator)
- the UI updates optimistically and then reconciles with server state

## Ticket Details (TicketInfo)
The ticket details panel is the primary place for editing a ticket.

Typical fields and interactions include:

- core fields (title, description, status, size, priority, assignee, reporter)
- labels
- acceptance criteria (represented as a list with completion checkboxes)
- relations: `related`, `blocking`, `blocked` (entered as id lists)

Observed behavior:

- updates are applied optimistically and then reconciled with the server
- on failed updates, the UI rolls back to the pre-update state and surfaces an error to the UI layer

## Ticket Relations (Related/Blocking/Blocked)
Relations are stored as ticket id lists.

Observed behavior:

- relation edits are synchronized via dedicated relation endpoints
- invalid/self ids are filtered out before relation diffs are computed
- changes are treated as set updates: only add/remove operations for changed edges are executed

## Guided Tour
The guided tour is a route-aware walkthrough of the UI.

Observed behavior:

- steps navigate to the correct page before attaching
- steps depend on stable DOM anchors and on layout preconditions (for example, a visible sidebar)

The architecture and failure modes are documented in [Guided Tour](guided_tour.md).

## Language Switching (i18n)
The UI supports switching between `en` and `de`.

Observed behavior:

- switching language updates visible translated text reactively
- tour labels and step text are localized because the guided tour is i18n-backed

Details are documented in [Internationalization (i18n)](i18n.md).

## AI Chat (In-App Assistant)
The UI includes a chat feature.

Observed behavior:

- requests are contextual: they include the current user, the selected project, and the selected ticket
- chat session state is reset on logout

## PWA (Installability)
The frontend ships with a PWA baseline configuration (manifest, service worker, icons).

Observed behavior:

- the app can be installed as a PWA in supported browsers
- offline behavior and update prompts are not part of the current baseline

Details are documented in [UI and PWA](ui_i18n_pwa.md).
