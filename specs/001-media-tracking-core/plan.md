# Implementation Plan: Media Tracking Core

**Branch**: `001-media-tracking-core` | **Date**: 2026-01-31 | **Spec**: [spec.md](./spec.md)
**Input**: Feature specification from `/specs/001-media-tracking-core/spec.md`

## Summary

Build a cross-platform media tracking application supporting movies, TV series, books, and podcasts. Users can add media to personalized lists (Want to Watch, Watching, Watched, Dropped), track episode progress, and discover trending content. The app uses an offline-first architecture with cloud sync, passwordless authentication, and integrations with TMDB, Google Books, and ListenAPI.

## Technical Context

**Language/Version**: TypeScript 5.x (strict mode)
**Primary Dependencies**: Astro 5.x, Vue 3, DrizzleORM, Capacitor 6.x, Supabase Client
**Storage**: SQLite (local via Capacitor), Supabase PostgreSQL (cloud sync)
**Testing**: Vitest (unit/integration), Playwright (E2E)
**Target Platform**: Web (Cloudflare Pages), iOS 15+, Android 10+
**Project Type**: Web + Mobile (shared codebase via Capacitor)
**Performance Goals**: <2s cold start, <100ms list operations, <2s search response
**Constraints**: Offline-capable, WCAG 2.1 AA, <50MB app bundle
**Scale/Scope**: Initial: 1k users, 100k list entries; Target: 100k users, 10M list entries

## Constitution Check

*GATE: Must pass before Phase 0 research. Re-check after Phase 1 design.*

| Principle | Status | Notes |
|-----------|--------|-------|
| I. Component-First | ✅ PASS | All UI built as Vue components in shared library |
| II. API-First | ✅ PASS | Service layer abstracts TMDB/Books/ListenAPI |
| III. Offline-First | ✅ PASS | Local SQLite + sync to Supabase |
| IV. Test Coverage | ✅ PASS | Vitest + Playwright planned per requirements |
| V. Cross-Platform Parity | ✅ PASS | Single Vue codebase, Capacitor for native |
| VI. Data Privacy | ✅ PASS | Local-first, opt-in analytics, data export planned |

## Project Structure

### Documentation (this feature)

```text
specs/001-media-tracking-core/
├── plan.md              # This file
├── research.md          # Technology decisions and alternatives
├── data-model.md        # Entity definitions and relationships
├── quickstart.md        # Development setup and testing guide
├── contracts/           # API service interfaces
└── tasks.md             # Implementation tasks as epics
```

### Source Code (repository root)

```text
src/
├── components/          # Shared Vue components
│   ├── ui/              # Generic UI (buttons, inputs, cards)
│   ├── media/           # Media-specific (MediaCard, EpisodeList)
│   └── layout/          # Navigation, tabs, sections
├── pages/               # Astro pages
│   ├── index.astro      # Homepage (trending + currently watching)
│   ├── search.astro     # Global search
│   ├── movies/          # Movie-specific pages
│   ├── series/          # TV series pages
│   ├── books/           # Book pages
│   └── podcasts/        # Podcast pages
├── services/            # Business logic & API abstraction
│   ├── media/           # TMDB, Google Books, ListenAPI clients
│   ├── sync/            # Offline sync logic
│   └── auth/            # Supabase auth wrapper
├── stores/              # Pinia state management
│   ├── user.ts          # User preferences and auth state
│   ├── lists.ts         # Media lists and progress
│   └── cache.ts         # API response cache
├── db/                  # DrizzleORM schema and migrations
│   ├── schema.ts        # Table definitions
│   └── migrations/      # Migration files
├── lib/                 # Utilities
│   ├── capacitor/       # Capacitor plugin wrappers
│   └── utils/           # Helpers (dates, formatting)
└── types/               # TypeScript type definitions

tests/
├── unit/                # Component and service tests (Vitest)
├── integration/         # Database and API tests
└── e2e/                 # End-to-end flows (Playwright)

ios/                     # Capacitor iOS project
android/                 # Capacitor Android project
```

**Structure Decision**: Astro + Vue hybrid with Capacitor for native mobile. Shared component library in `src/components/`, Astro pages for routing, Pinia for state. Capacitor projects at root level per official conventions.

## Complexity Tracking

No constitution violations detected. Architecture follows all principles.
