# CLAUDE_README.md

This file is written for a Claude Code agent being handed this project for the first time. Read this file completely before reading anything else.

---

## What This Project Is

**recommendarr** is a self-hosted web application that tracks what a user listens to and watches, builds a personalized taste profile from that history, and generates ranked recommendations for music, movies, and TV shows. It integrates with Radarr, Sonarr, and Lidarr so users can go directly from a recommendation to downloading it. It supports multiple user profiles in the same household, each with their own independent history and taste preferences.

The application described in the overview document is a real working implementation built with Python and Flask. You are not being asked to contribute to that existing codebase. You are being asked to build a new, similar application from scratch using whatever stack and architecture you believe is best suited to deliver all of the described features and pass all of the described test cases.

---

## Do This First — In This Order

Before writing a single line of code, complete these steps in sequence:

1. **Read `RECOMMENDARR_OVERVIEW.md`** completely. This is the feature specification. Every described feature is a requirement.
2. **Read `TEST_CASES.md`** completely. These are your acceptance criteria. Map each test case to the features it exercises.
3. **Read `config/config.yaml.example`** completely. This defines the full configuration structure — every service, every per-user source option, and every setting the app must support.
4. **Choose a technology stack** and document the decision. See the Architecture Decision section below.
5. **Create `todos/`** at the project root and write all TODO files before writing any code. See the TODO Files section below.
6. **Create `CLAUDE.md`** at the project root. See the Session Continuity section below.
7. **Begin coding from `TODO-001.md`**.

Do not skip or reorder these steps. The application has enough interconnected features that wrong early decisions will require expensive refactoring later.

---

## Reference Documents

### RECOMMENDARR_OVERVIEW.md

The feature specification. Describes every page, interaction, data source, taste profile algorithm, recommendation engine strategy, and explorer in detail. Treat it as the authoritative product requirements document.

Pay particular attention to:
- **Taste Profile** — the most algorithmically complex part. The override bars, grounding tags, noise tag filtering, and per-user isolation are all mandatory.
- **Music Explorer** — the most interactively complex part. The chain navigation model, local DB caching, Lidarr integration, per-listener song breakdowns, and the Refresh button are all mandatory.

The visual design section describes a dark purple-accented UI. You do not have to replicate it exactly, but the app should feel like a modern media app: dark background, card-based layout, accent colors per content type (purple for music, blue for movies, green for TV), responsive design.

### TEST_CASES.md

The acceptance test suite. Plain-prose user stories grouped by feature. Each test case describes a complete user journey: pages to visit, what to click, what to enter, and the expected outcome.

Your implementation is considered correct when every test case can be followed step by step and the expected outcomes occur. When the test cases require something the overview does not fully explain, **the test case is the ground truth**.

### config/config.yaml.example

The full configuration file template. Read this to understand exactly what options must be supported: every infrastructure service, every per-user source (Last.fm, ListenBrainz, Plex, Watcharr, YouTube, TMDb personal, TVDB personal, local media), and every general setting. The app must load and act on all of these.

---

## Architecture Decision

Before writing `TODO-001`, choose a technology stack. The reference implementation used Python with Flask, SQLite, Gunicorn, and APScheduler. You are not required to use the same stack. Good choices include Python with Flask or FastAPI, Node.js with Express or Fastify, or a full-stack framework like SvelteKit or Next.js. SQLite is appropriate for a self-hosted single-instance app of this scale.

Whatever you choose:
- Document the choice and its rationale at the top of `TODO-001.md`.
- Do not revisit it. Stack changes mid-build are not acceptable.
- Ensure the stack supports: background job scheduling, SQLite, server-side session management, CSRF protection, and Docker deployment.

---

## TODO Files

After reading all three reference documents, your first concrete deliverable is a set of TODO files — not code.

### Format

Store all TODO files in `todos/` at the project root. Name them `TODO-001.md`, `TODO-002.md`, etc.

Each TODO file must begin with a status block in exactly this format:

```
Status: Not Started
Started: —
Completed: —
Notes: —
```

When work begins, update to:

```
Status: In Progress
Started: [brief description of where you are]
Completed: —
Notes: [anything a fresh session needs to know to continue]
```

When done:

```
Status: Complete
Started: —
Completed: [brief summary of what was built]
Notes: [anything the next TODO needs to know]
```

After the status block, each TODO file must contain:
- Phase name and number
- A one-paragraph description of what this phase builds and why it comes at this point in the sequence
- A checklist of specific features, routes, database tables, UI components, and behaviors that must be done before this TODO is complete
- The `TC-` test case identifiers from `TEST_CASES.md` this phase must satisfy
- External APIs, services, or libraries this phase depends on
- A **Deferred Decisions** section (may be empty) for decisions that belong to a later phase

### Ordering Rules

Each phase must be completable without depending on anything from a later phase. A later phase may extend an earlier one but must never require it to be rearchitected. If you complete TODO-004 exactly as written, a fresh session with no other context must be able to start TODO-005 without confusion.

### Suggested Phase Breakdown

This is a guide, not a rule. Combine phases if they are deeply interdependent. Split a phase if it is too large for one focused session.

| Phase | Description |
|-------|-------------|
| **Phase 1** | Project scaffolding: repository structure, chosen framework, database connection, config file loading, Docker setup, and a working dev server. |
| **Phase 2** | Authentication: user account table, login/logout routes, session management, CSRF protection, first-run setup page. |
| **Phase 3** | User profiles: create, rename, activate, deactivate, delete. Database tables for per-user data isolation. |
| **Phase 4** | Scrobble import: Last.fm CSV import flow, scrobble history table, purge and reimport, Last.fm API enrichment for genre tags. |
| **Phase 5** | Infrastructure connectors: Radarr, Sonarr, Lidarr, TMDb, MusicBrainz, and TVDB API clients. Connection testing. Library sync into known_items table. |
| **Phase 6** | Per-user data source connectors: Last.fm live sync, ListenBrainz, Plex (music + movies + TV), Watcharr, YouTube playlists + Takeout import, TMDb personal, TVDB personal, local media scanner. |
| **Phase 7** | Taste profile builder: tag collection from all enabled sources, noise tag filtering, weight computation, normalization, per-user per-category storage. |
| **Phase 8** | Recommendation engine: all music strategies (tag-based artist, similar-artist, tag-based album, collection gap finder, band-member web), all movie strategies (genre discover, similar-title, cast, crew, company, hidden gems), TV strategies (genre discover, similar-title). Scoring, override bars, grounding tags, dismissed item exclusion, release window filters. |
| **Phase 9** | Recommendations page and Taste Profile page: score bar, type tabs, user selector, release window filter panel, five-bar override widget, grounding tag form. |
| **Phase 10** | Music Explorer: search, chain navigation model, artist node cards, members/member-of, albums + tracklists, songs layer with per-listener bars, Lidarr integration, local DB caching, Refresh button. |
| **Phase 11** | Movie Explorer: same chain pattern — movie cards, person cards (cast + crew), company cards, Radarr add, Plex watchlist. |
| **Phase 12** | TV Explorer: show cards, person cards, network cards, Sonarr add, Plex watchlist. |
| **Phase 13** | Dashboard, Status page, per-profile stat cards, background sync scheduler, YouTube Playlists page, Utilities page (cache clear, per-source purge). |
| **Phase 14** | Final integration: run every test case, fix failures, verify end-to-end. |

---

## Session Continuity

Claude Code sessions have context limits. When a session ends, context is lost. Two mechanisms keep the build continuous across sessions.

### CLAUDE.md (primary)

Create a `CLAUDE.md` file at the project root before writing any code. Claude Code reads this file automatically at the start of every session. Keep it updated throughout the build. It should always contain:

- The chosen technology stack and why
- The current phase (which TODO is In Progress)
- Key architectural decisions made so far (schema conventions, API patterns, naming, etc.)
- Any gotchas or non-obvious things a fresh session must know

This is the most reliable continuity mechanism. Keep it concise and current.

### TODO files (secondary)

At the start of every session, read `CLAUDE.md` first, then check the `todos/` folder for the first TODO whose status is `In Progress` or `Not Started`. That is where you start.

At the end of every session, update the current TODO's status block and update `CLAUDE.md` to reflect the current phase and any new decisions made.

Mark a TODO `Complete` only when every item on its checklist is done and every listed test case passes.

---

## The Deferral Rule

When working through a TODO and you encounter a decision that is out of scope for the current phase, do not make it. Add it to the **Deferred Decisions** section of the current TODO, describe the decision and which future TODO it belongs to, and move on.

> **Example:** On Phase 3 (user profiles), you realize you need to decide how per-user Plex tokens are stored and validated — but that belongs to Phase 5 (connectors). Write: *"Plex token storage format deferred to TODO-005. For now, store as a plain string in the user sources config table."* Then continue.

Do not go back and reopen a completed TODO to satisfy a deferred decision from a later phase unless the earlier code is provably broken. If it works, leave it.

---

## Things That Must Not Be Wrong

A few areas where early mistakes are expensive:

**Per-user isolation** — Every taste profile, override bar, grounding tag, dismissed recommendation, and scrobble record must be scoped to a specific user profile. Infrastructure data (Radarr/Sonarr/Lidarr library snapshots) is shared across users, but nothing else is. Get the schema right in Phase 3 and enforce it everywhere.

**Chain navigation in the explorers** — The pattern where clicking within a card opens the next card below it, and closing a card collapses it and everything after it, is the core interaction of all three explorers. Build it correctly in the Music Explorer (Phase 10) and the Movie and TV Explorers are adaptations of the same pattern.

**Local caching** — Every API response from MusicBrainz, TMDb, and Last.fm that is fetched during explorer browsing must be written to the local database. Subsequent loads must come from the DB. A Refresh button must force a live re-fetch. Do not skip this — it is what makes the explorers usable.

**Schema must be additive** — Never drop or alter existing tables. Only add new ones or new columns. This ensures the app can be updated without data loss.

---

## Goal

A working, self-hostable application that a non-technical user can run with Docker, configure through a YAML file and a web UI, and use without any external accounts beyond the optional services they choose to connect. Keep that user in mind throughout every phase.
