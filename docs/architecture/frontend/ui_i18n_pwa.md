# Frontend UI, i18n and PWA

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

## Localization Lifecycle
`I18nProvider` initializes i18next once and exposes:
- translation function `t(...)`
- active language accessor
- language switch function

A readiness gate (`Show when={ready()}`) prevents rendering untranslated placeholders before initialization completes.

Operational rule:
- every visible string must exist in both locale files (`en.json`, `de.json`)

## Guided Tour Behavior
`GuideProvider` (Shepherd.js) is route-aware and async-safe.

Mechanics:
- route change is triggered per step when required
- tour waits until target DOM anchor exists (`waitForElement`)
- sidebar can be programmatically opened before sidebar-bound steps

Benefit:
- guide remains stable across route transitions and deferred rendering.

Risk:
- refactoring anchor IDs without updating guide config breaks steps.

## PWA Implementation
Configured via `vite-plugin-pwa` in `vite.config.ts`.

Current baseline:
- generated service worker
- manifest metadata
- static icons in `public/icons`
- dev PWA mode enabled

## PWA Gaps and Next Logical Steps
Current gaps:
- no offline fallback UX
- no app-update prompt for new service worker
- no explicit runtime caching strategy for API calls

Recommended next steps:
1. define runtime caching rules per resource type
2. add update notification flow (new version available)
3. add offline page for navigation fallback
