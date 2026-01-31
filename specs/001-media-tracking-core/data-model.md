# Data Model: Media Tracking Core

**Date**: 2026-01-31
**Branch**: 001-media-tracking-core

## Entity Relationship Diagram

```
┌─────────────┐       ┌──────────────────┐       ┌─────────────────┐
│    User     │───────│   ListEntry      │───────│   MediaItem     │
└─────────────┘  1:N  └──────────────────┘  N:1  └─────────────────┘
                              │                          │
                              │                          │ 1:N
                              │                    ┌─────────────┐
                              │                    │   Episode   │
                              │                    └─────────────┘
                              │                          │
                              │                          │
                      ┌───────────────┐                  │
                      │ EpisodeWatch  │──────────────────┘
                      └───────────────┘
                              │
                              │
                      ┌───────────────┐
                      │  Collection   │──────── MediaItem (N:M)
                      └───────────────┘
```

## Entities

### User

Represents an authenticated user. Local-only users have no user record until they authenticate.

| Field | Type | Constraints | Description |
|-------|------|-------------|-------------|
| id | UUID | PK | Supabase auth user ID |
| email | string | unique | User's email address |
| created_at | timestamp | not null | Account creation time |
| last_sync_at | timestamp | nullable | Last successful cloud sync |
| preferences | JSON | nullable | UI preferences (theme, default tab) |

**State Transitions**: None (static after creation)

### MediaItem

Represents any trackable media (movie, series, book, podcast).

| Field | Type | Constraints | Description |
|-------|------|-------------|-------------|
| id | UUID | PK | Internal unique identifier |
| external_id | string | indexed | External API ID (TMDB ID, ISBN, etc.) |
| source | enum | not null | 'tmdb', 'google_books', 'listen_api' |
| type | enum | not null | 'movie', 'series', 'book', 'podcast' |
| title | string | not null | Display title |
| poster_url | string | nullable | Cover/poster image URL |
| release_date | date | nullable | Release or publication date |
| synopsis | text | nullable | Description/synopsis |
| metadata | JSON | nullable | Source-specific data (runtime, pages, etc.) |
| cached_at | timestamp | not null | When metadata was last fetched |

**Indexes**: 
- `(external_id, source)` for API lookups
- `type` for filtered lists

**State Transitions**: None (immutable once cached; re-fetch updates)

### ListEntry

Links a user to a media item with tracking status.

| Field | Type | Constraints | Description |
|-------|------|-------------|-------------|
| id | UUID | PK | Unique entry ID |
| user_id | UUID | FK → User, nullable | Null for local-only (anonymous) |
| media_item_id | UUID | FK → MediaItem | The tracked media |
| status | enum | not null | 'want', 'active', 'done', 'dropped' |
| added_at | timestamp | not null | When added to list |
| status_changed_at | timestamp | not null | When status last changed |
| device_id | string | not null | Device that made last change (for sync) |
| is_synced | boolean | not null, default false | True if synced to cloud |

**Indexes**:
- `user_id` for user lists
- `status` for filtered views
- `is_synced` for sync queue

**State Transitions**:
- want → active → done (normal flow)
- any → dropped (user gives up)
- any → want (re-adding after removal)

### Episode

Represents a single episode of a series or podcast.

| Field | Type | Constraints | Description |
|-------|------|-------------|-------------|
| id | UUID | PK | Unique episode ID |
| media_item_id | UUID | FK → MediaItem | Parent series/podcast |
| external_id | string | indexed | External API episode ID |
| season_number | int | nullable | Season (null for podcasts) |
| episode_number | int | not null | Episode number within season |
| title | string | nullable | Episode title |
| air_date | date | nullable | Original air/publish date |
| runtime_minutes | int | nullable | Episode duration |

**Indexes**:
- `media_item_id` for episode lists
- `(media_item_id, season_number, episode_number)` for ordering

### EpisodeWatch

Tracks user progress on individual episodes.

| Field | Type | Constraints | Description |
|-------|------|-------------|-------------|
| id | UUID | PK | Unique watch record ID |
| user_id | UUID | FK → User, nullable | Null for local-only |
| episode_id | UUID | FK → Episode | Watched episode |
| watched_at | timestamp | not null | When marked as watched |
| device_id | string | not null | Device that recorded watch |
| is_synced | boolean | not null, default false | True if synced to cloud |

**Indexes**:
- `(user_id, episode_id)` unique for idempotency
- `is_synced` for sync queue

### Collection

Groups related movies (franchises, series).

| Field | Type | Constraints | Description |
|-------|------|-------------|-------------|
| id | UUID | PK | Unique collection ID |
| external_id | string | indexed | TMDB collection ID |
| name | string | not null | Collection name |
| poster_url | string | nullable | Collection poster |

### CollectionItem

Junction table for collection membership.

| Field | Type | Constraints | Description |
|-------|------|-------------|-------------|
| collection_id | UUID | FK → Collection | Parent collection |
| media_item_id | UUID | FK → MediaItem | Member movie |
| order | int | not null | Position in collection |

**Primary Key**: `(collection_id, media_item_id)`

## Validation Rules

1. **ListEntry**: Only one entry per `(user_id, media_item_id)` pair
2. **EpisodeWatch**: Only one watch record per `(user_id, episode_id)` pair
3. **MediaItem.type**: Must match source (TMDB → movie/series, Google Books → book, ListenAPI → podcast)
4. **Episode**: `season_number` required for series, null for podcasts
5. **Sync**: All `is_synced=false` records must be processed on next online event

## Sync Strategy

1. **Local-first**: All writes go to local SQLite immediately
2. **Queue-based**: Unsynced records (`is_synced=false`) form the sync queue
3. **Conflict resolution**: Last-write-wins using `device_id` + timestamp
4. **Bidirectional**: Pull cloud changes → merge → push local changes
5. **Idempotent**: Sync operations can be safely retried
