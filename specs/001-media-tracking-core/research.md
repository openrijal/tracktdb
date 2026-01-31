# Research: Media Tracking Core

**Date**: 2026-01-31
**Branch**: 001-media-tracking-core

## Technology Decisions

### Framework: Astro + Vue

**Decision**: Use Astro 5.x with Vue 3 integration for the web frontend.

**Rationale**:
- Astro provides excellent static generation and partial hydration ("islands")
- Vue integration is first-class with `@astrojs/vue`
- Content-focused approach suits media catalog pages
- Shared components work across web and Capacitor

**Alternatives Considered**:
- Next.js + React: Heavier bundle, no Capacitor-native Vue ecosystem
- Nuxt 3: Full Vue SSR but less flexible for static-first content
- SvelteKit: Smaller bundle but smaller ecosystem for media-related components

### Database: DrizzleORM + SQLite/PostgreSQL

**Decision**: DrizzleORM with SQLite for local storage, Supabase PostgreSQL for cloud.

**Rationale**:
- DrizzleORM is type-safe and works with both SQLite and PostgreSQL
- SQLite via `@capacitor-community/sqlite` for offline mobile storage
- Supabase provides managed PostgreSQL with built-in auth
- Same schema definition works across both databases

**Alternatives Considered**:
- Prisma: Heavier runtime, SQLite support less mature
- TypeORM: More verbose, decorator-based syntax
- Raw SQL: No type safety, more boilerplate

### Authentication: Supabase Magic Links

**Decision**: Passwordless email authentication via Supabase magic links.

**Rationale**:
- Zero password management for users
- Supabase handles email delivery and link generation
- Works on web and mobile (deep linking)
- Free tier sufficient for initial launch

**Alternatives Considered**:
- Email + Password: More friction, password reset flows needed
- OAuth only (Google/Apple): Requires store review, limits user base
- Custom auth: Significant engineering overhead

### Media APIs

**Decision**: Use TMDB for movies/TV, Google Books API for books, ListenAPI for podcasts.

**Rationale**:
- TMDB: Industry standard, extensive metadata, free tier (40 req/s)
- Google Books: Comprehensive catalog, free API
- ListenAPI: Modern podcast API with good coverage

**Alternatives Considered**:
- OMDb: Less metadata than TMDB, no where-to-watch data
- Open Library: Less cover images than Google Books
- Podcast Index: Less structured episode metadata

### Mobile: Capacitor

**Decision**: Capacitor 6.x for iOS and Android builds.

**Rationale**:
- Shares web codebase (Vue components work directly)
- Native plugins for SQLite, filesystem, deep linking
- Official Ionic support and active community
- PWA fallback built-in

**Alternatives Considered**:
- React Native: Would require rewrite from Vue
- Flutter: Completely different tech stack
- PWA only: Limited offline support on iOS, no app store presence

### Deployment: Cloudflare Pages

**Decision**: Cloudflare Pages for web hosting with edge functions.

**Rationale**:
- Fast global CDN
- Free tier includes custom domains and unlimited bandwidth
- Astro adapter available (`@astrojs/cloudflare`)
- Edge functions for API proxying/caching

**Alternatives Considered**:
- Vercel: Similar features but Vue/Astro support less prominent
- Netlify: Good option but Cloudflare edge network is faster
- Self-hosted: More operational overhead

## Open Questions (Resolved)

| Question | Resolution |
|----------|------------|
| How to handle large episode lists (500+ episodes)? | Virtual scrolling with `vue-virtual-scroller` |
| Where-to-watch data source? | TMDB provides via JustWatch integration (free) |
| Ratings aggregation? | TMDB provides IMDB IDs, fetch ratings client-side or cache |
| Image CDN? | Use TMDB/Google Books image URLs directly (no self-hosting) |

## Dependencies to Install

```json
{
  "dependencies": {
    "astro": "^5.0.0",
    "@astrojs/vue": "^5.0.0",
    "@astrojs/cloudflare": "^12.0.0",
    "vue": "^3.5.0",
    "pinia": "^2.2.0",
    "@capacitor/core": "^6.0.0",
    "@capacitor/ios": "^6.0.0",
    "@capacitor/android": "^6.0.0",
    "@capacitor-community/sqlite": "^6.0.0",
    "drizzle-orm": "^0.35.0",
    "@supabase/supabase-js": "^2.45.0",
    "vue-virtual-scroller": "^2.0.0"
  },
  "devDependencies": {
    "drizzle-kit": "^0.25.0",
    "vitest": "^2.0.0",
    "@playwright/test": "^1.48.0",
    "typescript": "^5.6.0"
  }
}
```
