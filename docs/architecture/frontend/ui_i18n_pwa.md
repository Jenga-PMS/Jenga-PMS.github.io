# Frontend UI and PWA

## UI Composition Model
The frontend uses a shell-plus-features model.

Shell (`App.tsx`):
- app bar and global controls
- sidebar navigation
- page content outlet
- footer

Feature pages compose domain widgets and delegate state mutations to providers.

## Rendering Responsibilities
- components render local UI and emit intent (events)
- pages aggregate components and handle user feedback
- providers perform async mutations and server sync

This keeps rendering logic and orchestration logic separated.

## UI Structural Notes
- `Home` composes `Projects` and `Filedrop`
- `Sprint` composes `TicketFilters`, `Kanban`, `Backlog`, and the `TicketInfo` sidebar
- route components do not own server orchestration logic in the current implementation; they trigger provider actions and show feedback

## PWA Implementation
Configured via `vite-plugin-pwa` in `vite.config.ts`.

Current baseline:
- generated service worker
- manifest metadata (some fields are placeholder values in the current configuration)
- static icons in `public/icons`
- dev PWA mode enabled

## PWA Gaps and Potential Extensions
Current gaps:
- no offline fallback UX
- no app-update prompt for new service worker
- no explicit runtime caching strategy for API calls

Potential extensions (not implemented in current baseline):
1. runtime caching rules per resource type
2. update notification flow when a new service worker is available
3. offline page/route for navigation fallback

## Related Pages
- [Internationalization (i18n)](i18n.md)
- [Guided Tour](guided_tour.md)
