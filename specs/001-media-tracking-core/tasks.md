# Tasks: Media Tracking Core

**Branch**: 001-media-tracking-core
**Date**: 2026-01-31
**Spec**: [spec.md](./spec.md) | **Plan**: [plan.md](./plan.md)

## Overview

- **Total Tasks**: 67
- **Epics**: 12
- **Estimated MVP**: Epics 1-4 (Setup + Core DB + Auth + Search)

## Phase 1: Project Setup

### Epic 1: Skeleton Project Setup

Initialize the Astro + Vue + Capacitor project structure with all dependencies.

**Acceptance**: Project builds for web, `npm run dev` works, Capacitor projects exist.

- [ ] T001 Initialize Astro project with TypeScript in root directory
- [ ] T002 Install and configure @astrojs/vue integration in `astro.config.mjs`
- [ ] T003 Install and configure Tailwind CSS with Astro in `tailwind.config.cjs`
- [ ] T004 Install Pinia and create store configuration in `src/stores/index.ts`
- [ ] T005 Create base project structure per plan.md directories
- [ ] T006 Configure TypeScript strict mode in `tsconfig.json`
- [ ] T007 [P] Initialize Capacitor with iOS and Android projects via `npx cap init`
- [ ] T008 [P] Configure Vitest for unit testing in `vitest.config.ts`
- [ ] T009 [P] Configure Playwright for E2E testing in `playwright.config.ts`
- [ ] T010 Create base layout component in `src/components/layout/BaseLayout.vue`
- [ ] T011 Create navigation tabs component in `src/components/layout/TabNavigation.vue`

---

## Phase 2: Foundational Infrastructure

### Epic 2: DrizzleORM Configuration

Set up database schema and migrations for local SQLite and cloud PostgreSQL.

**Acceptance**: Schema compiles, migrations run locally, types are generated.

- [ ] T012 Install drizzle-orm and drizzle-kit dependencies
- [ ] T013 Create DrizzleORM schema for User entity in `src/db/schema/user.ts`
- [ ] T014 Create DrizzleORM schema for MediaItem entity in `src/db/schema/mediaItem.ts`
- [ ] T015 Create DrizzleORM schema for ListEntry entity in `src/db/schema/listEntry.ts`
- [ ] T016 Create DrizzleORM schema for Episode entity in `src/db/schema/episode.ts`
- [ ] T017 Create DrizzleORM schema for EpisodeWatch entity in `src/db/schema/episodeWatch.ts`
- [ ] T018 Create DrizzleORM schema for Collection entities in `src/db/schema/collection.ts`
- [ ] T019 Create schema index exporting all entities in `src/db/schema/index.ts`
- [ ] T020 Configure drizzle-kit for SQLite in `drizzle.config.ts`
- [ ] T021 Generate initial migration with `npx drizzle-kit generate`
- [ ] T022 Create database connection utility for local SQLite in `src/db/client.ts`

### Epic 3: Supabase Configuration

Configure Supabase client, database connection, and real-time subscriptions.

**Acceptance**: Supabase client connects, can read/write to cloud database.

- [ ] T023 Create Supabase project and obtain API keys (manual step - document in README)
- [ ] T024 Install @supabase/supabase-js dependency
- [ ] T025 Create Supabase client singleton in `src/services/supabase/client.ts`
- [ ] T026 Create environment configuration for Supabase in `src/lib/env.ts`
- [ ] T027 Apply DrizzleORM schema to Supabase PostgreSQL via migrations
- [ ] T028 Create sync service interface in `src/services/sync/types.ts`
- [ ] T029 Implement bidirectional sync logic in `src/services/sync/syncService.ts`
- [ ] T030 Create sync queue manager for offline operations in `src/services/sync/queue.ts`

### Epic 4: Supabase Auth (Passwordless)

Implement passwordless email authentication with magic links.

**Acceptance**: User can request magic link, click it, and be authenticated.

- [ ] T031 Configure Supabase Auth settings for magic links (Supabase dashboard)
- [ ] T032 Create auth service wrapper in `src/services/auth/authService.ts`
- [ ] T033 Create auth store with Pinia in `src/stores/auth.ts`
- [ ] T034 Create sign-in page with email input in `src/pages/auth/signin.astro`
- [ ] T035 Create magic link callback handler in `src/pages/auth/callback.astro`
- [ ] T036 Create auth state persistence for Capacitor in `src/lib/capacitor/authPersist.ts`
- [ ] T037 Configure deep linking for magic links on iOS in `ios/App/App/Info.plist`
- [ ] T038 Configure deep linking for magic links on Android in `android/app/src/main/AndroidManifest.xml`

### Epic 5: Capacitor Configuration

Configure Capacitor plugins for SQLite, filesystem, and platform features.

**Acceptance**: App runs on iOS simulator and Android emulator with SQLite.

- [ ] T039 Install @capacitor-community/sqlite plugin
- [ ] T040 Create SQLite wrapper for Capacitor in `src/lib/capacitor/sqlite.ts`
- [ ] T041 Implement database initialization on app start in `src/lib/capacitor/init.ts`
- [ ] T042 Configure iOS project settings in Xcode (bundle ID, capabilities)
- [ ] T043 Configure Android project settings in Android Studio (package name, permissions)
- [ ] T044 Create platform detection utility in `src/lib/utils/platform.ts`

### Epic 6: Cloudflare Configuration

Configure Cloudflare Pages deployment with Astro adapter.

**Acceptance**: Site deploys to Cloudflare Pages, edge functions work.

- [ ] T045 Install @astrojs/cloudflare adapter
- [ ] T046 Configure Astro for Cloudflare in `astro.config.mjs`
- [ ] T047 Create Cloudflare Pages project (manual - document in README)
- [ ] T048 Configure GitHub Actions for automatic deployment in `.github/workflows/deploy.yml`
- [ ] T049 Set up environment variables in Cloudflare dashboard

---

## Phase 3: External Integrations

### Epic 7: Media API Integrations

Implement service layer for TMDB, Google Books, and ListenAPI.

**Acceptance**: Can search and fetch details from all three APIs.

- [ ] T050 Create TMDB service client in `src/services/media/tmdb.ts`
- [ ] T051 Create Google Books service client in `src/services/media/googleBooks.ts`
- [ ] T052 Create ListenAPI service client in `src/services/media/listenApi.ts`
- [ ] T053 Create unified media search service in `src/services/media/search.ts`
- [ ] T054 Create API response caching layer in `src/services/media/cache.ts`
- [ ] T055 Create rate limiting utility for API calls in `src/lib/utils/rateLimit.ts`
- [ ] T056 Implement trending content fetcher in `src/services/media/trending.ts`
- [ ] T057 Implement where-to-watch data fetcher in `src/services/media/watchProviders.ts`
- [ ] T058 Implement ratings aggregator in `src/services/media/ratings.ts`

---

## Phase 4: User Interface

### Epic 8: Core UI Components

Build reusable Vue components for media display and interaction.

**Acceptance**: Components render correctly, are responsive, and accessible.

- [ ] T059 [P] Create MediaCard component in `src/components/media/MediaCard.vue`
- [ ] T060 [P] Create MediaGrid component in `src/components/media/MediaGrid.vue`
- [ ] T061 [P] Create EpisodeList component in `src/components/media/EpisodeList.vue`
- [ ] T062 [P] Create SearchBar component in `src/components/ui/SearchBar.vue`
- [ ] T063 [P] Create StatusSelector component in `src/components/media/StatusSelector.vue`
- [ ] T064 [P] Create RatingDisplay component in `src/components/media/RatingDisplay.vue`
- [ ] T065 [P] Create CastCrew component in `src/components/media/CastCrew.vue`
- [ ] T066 [P] Create WatchProviders component in `src/components/media/WatchProviders.vue`
- [ ] T067 [P] Create ProgressBar component in `src/components/ui/ProgressBar.vue`

### Epic 9: Homepage & Navigation

Build homepage with trending and currently watching sections.

**Acceptance**: Homepage displays content, tabs navigate between media types.

- [ ] T068 [US1] Create homepage layout in `src/pages/index.astro`
- [ ] T069 [US1] Create TrendingSection component in `src/components/sections/TrendingSection.vue`
- [ ] T070 [US1] Create CurrentlyWatching section in `src/components/sections/CurrentlyWatching.vue`
- [ ] T071 [US6] Create media type tab pages in `src/pages/movies/index.astro`, `src/pages/series/index.astro`, etc.
- [ ] T072 [US6] Create status sub-tabs component in `src/components/layout/StatusTabs.vue`
- [ ] T073 Create lists store in `src/stores/lists.ts`
- [ ] T074 Create cache store in `src/stores/cache.ts`

### Epic 10: Global Search

Implement cross-media search with results from all sources.

**Acceptance**: User can search, see grouped results, add items to lists.

- [ ] T075 [US1] Create search page in `src/pages/search.astro`
- [ ] T076 [US1] Create SearchResults component in `src/components/search/SearchResults.vue`
- [ ] T077 [US1] Create SearchResultGroup component in `src/components/search/SearchResultGroup.vue`
- [ ] T078 [US1] Implement search debouncing and caching in `src/lib/utils/search.ts`

### Epic 11: Media Details

Build detail pages for movies, series, books, and podcasts.

**Acceptance**: Detail pages show full metadata, ratings, cast, where-to-watch.

- [ ] T079 [US5] Create movie detail page in `src/pages/movies/[id].astro`
- [ ] T080 [US5] Create series detail page in `src/pages/series/[id].astro`
- [ ] T081 [US5] Create book detail page in `src/pages/books/[id].astro`
- [ ] T082 [US5] Create podcast detail page in `src/pages/podcasts/[id].astro`
- [ ] T083 [US7] Create CollectionView component in `src/components/media/CollectionView.vue`
- [ ] T084 [US2] Implement episode tracking UI in `src/components/media/EpisodeTracker.vue`

---

## Phase 5: Polish & Cross-Cutting

### Epic 12: Testing & Quality

Write tests and ensure quality standards.

**Acceptance**: Tests pass, coverage meets targets, accessibility validated.

- [ ] T085 Write unit tests for media services in `tests/unit/services/`
- [ ] T086 Write unit tests for sync service in `tests/unit/sync/`
- [ ] T087 Write integration tests for database operations in `tests/integration/db/`
- [ ] T088 Write E2E test for search → add to list flow in `tests/e2e/search.spec.ts`
- [ ] T089 Write E2E test for authentication flow in `tests/e2e/auth.spec.ts`
- [ ] T090 Run accessibility audit with axe-core
- [ ] T091 Create README with setup instructions in `README.md`
- [ ] T092 Create contributing guide in `CONTRIBUTING.md`

---

## Dependency Graph

```
Epic 1 (Setup)
    │
    ├──► Epic 2 (Drizzle) ──► Epic 3 (Supabase) ──► Epic 4 (Auth)
    │                                    │
    │                                    ▼
    ├──► Epic 5 (Capacitor) ◄────────────┘
    │
    └──► Epic 6 (Cloudflare)

Epic 7 (APIs) ─────► Epic 8 (Components) ─────► Epic 9 (Homepage)
                           │                        │
                           ▼                        ▼
                     Epic 10 (Search)         Epic 11 (Details)
                           │                        │
                           └────────────┬───────────┘
                                        ▼
                                  Epic 12 (Testing)
```

## Parallel Execution Opportunities

| Tasks | Can Run Parallel | Reason |
|-------|------------------|--------|
| T007, T008, T009 | Yes | Independent tool configuration |
| T059-T067 | Yes | Independent component files |
| T071 pages | Yes | Independent page files |
| T079-T082 | Yes | Independent detail pages |

## MVP Scope

**Minimum Viable Product**: Epics 1-4, 7-10

This delivers:
- Working web app with search
- Add to list functionality
- Magic link authentication
- Trending content
- Basic media details

**Post-MVP**: Epics 5, 6, 11, 12 (Mobile deployment, collections, full testing)
