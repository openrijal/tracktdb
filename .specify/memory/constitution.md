# TracktDB Constitution

<!-- Sync Impact Report
Version change: 0.0.0 → 1.0.0 (Initial constitution)
Added principles: Component-First, API-First, Offline-First, Test-Coverage, Cross-Platform Parity, Data Privacy
Added sections: Technology Stack, Development Workflow
Templates requiring updates: ✅ None (initial setup)
-->

## Core Principles

### I. Component-First Architecture
Every UI feature MUST be built as a reusable Vue component before integration into pages. Components MUST be independently testable with Storybook or similar. Shared components live in a dedicated component library with clear documentation.

**Rationale**: Astro + Vue architecture demands clear component boundaries. Reusable components accelerate development and ensure visual consistency across web and mobile via Capacitor.

### II. API-First Design
All external integrations (TMDB, Google Books, ListenAPI) MUST be abstracted behind a unified service layer. Direct API calls from components are forbidden. Each service MUST define TypeScript interfaces for request/response contracts.

**Rationale**: Media APIs have rate limits, varying schemas, and may change. Abstraction enables caching, fallbacks, and future provider swaps without UI changes.

### III. Offline-First with Sync
User data (watchlists, progress, ratings) MUST be stored locally first using DrizzleORM. Sync to Supabase happens when online. Conflicts resolve using last-write-wins with user notification for significant changes.

**Rationale**: Mobile users expect apps to work offline. Media tracking is personal—data loss is unacceptable.

### IV. Test Coverage Requirements
- Unit tests MUST cover all service layer functions.
- Integration tests MUST verify Supabase auth flows and data sync.
- E2E tests MUST cover critical user journeys: search, add to list, mark as watched.
- Minimum 70% code coverage for non-UI code.

**Rationale**: Media tracking involves complex state (episodes, progress, collections). Tests prevent regressions.

### V. Cross-Platform Parity
Features MUST work identically on web and mobile (iOS/Android via Capacitor). Platform-specific code MUST be isolated in Capacitor plugins. UI MUST be responsive-first, tested at mobile breakpoints.

**Rationale**: Users expect seamless experience across devices. TracktDB is a personal tool used on couch and commute.

### VI. Data Privacy & User Control
User data MUST never be shared with third parties. Users MUST be able to export their data (JSON). Users MUST be able to delete their account and all associated data. Analytics, if added, MUST be opt-in.

**Rationale**: Media consumption is personal. Trust is foundational.

## Technology Stack

**Non-negotiable stack decisions:**
- **Framework**: Astro 5.x with Vue 3 integration
- **Database**: DrizzleORM with SQLite (local) + Supabase (cloud sync)
- **Auth**: Supabase Auth with passwordless email (magic links)
- **Mobile**: Capacitor 6.x for iOS/Android
- **Deployment**: Cloudflare Pages (web), App Store / Play Store (mobile)
- **APIs**: TMDB (movies/TV), Google Books API, ListenAPI (podcasts)

**Styling**: Tailwind CSS with component-scoped styles.
**State**: Vue Composition API with Pinia for global state.

## Development Workflow

1. **Feature Specification**: Every feature starts with a spec in `/specs/<feature>/spec.md`.
2. **Task Breakdown**: Specs become epics in GitHub Issues with sub-tasks.
3. **Branch Strategy**: `main` (production), `develop` (integration), `feature/<name>` (work).
4. **PR Requirements**: 
   - Linked to GitHub issue
   - Passing CI (tests, lint, type-check)
   - At least one approval (when team grows)
5. **Release Cadence**: Weekly deploys to web; mobile follows app store review cycles.

## Governance

This constitution supersedes all ad-hoc decisions. Amendments require:
1. Written proposal with rationale
2. Update to this document with version bump
3. Notification in project changelog

All code reviews MUST verify compliance with these principles. Deviations require documented justification in the PR.

**Version**: 1.0.0 | **Ratified**: 2026-01-31 | **Last Amended**: 2026-01-31
