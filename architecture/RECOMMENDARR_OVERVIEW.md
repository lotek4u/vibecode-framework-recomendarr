# recommendarr — Full Feature Overview

> **Purpose of this document:** A complete blueprint describing every feature of recommendarr as it stands today, written so that another developer could understand the vision and attempt to build something similar from scratch.

---

## What is recommendarr?

recommendarr is a self-hosted web application that learns what you watch and listen to, builds a taste profile from that history, and uses it to generate ranked recommendations for music, movies, and TV shows. It is designed for households with multiple users, each with their own media history and preferences.

The core philosophy is **local-first data ownership**: all scrobble history is stored in a local SQLite database, all browsing activity in the explorers is cached locally, and all taste profile computation happens on your own server. You control what gets fed in, how it's weighted, and what gets recommended.

The app integrates with the popular *arr suite (Radarr, Sonarr, Lidarr) so you can go from "I got a recommendation" to "it's downloading" in a single click.

---

## Visual Design & Layout

recommendarr has a dark, purple-accented UI. The color palette is deep navy (`#0d0d1a`) for the page background, slightly lighter navy cards (`#151528`), with purple as the primary accent (`#7c3aed` / `#a855f7`). Green is used for success states and TV content, blue for movie content, and muted gray for secondary text.

The app uses Bootstrap 5 for layout with heavily customized components. Bootstrap Icons are used throughout for iconography. All card corners are rounded, and cards lift slightly on hover. The overall feel is modern and clean — like a media app, not a configuration panel.

**Navigation** is a top navbar containing:
- **Dashboard** — home page with profile stats
- **User Profiles** — manage users and import scrobble history
- **Music Explorer** — interactive band/artist graph browser
- **Movie Explorer** — interactive movie graph browser
- **TV Explorer** — interactive TV show graph browser
- Username + Logout button (when authentication is enabled)
- The "recommendarr" brand name on the right

The Dashboard has a **left sidebar** containing sub-navigation for: Dashboard home, Recommendations, Taste Profile, and Status. This sidebar keeps you in the "dashboard context" while switching between those four related views.

---

## Navigation & Pages

### Dashboard (Home)
The home page shows a stat card for each active user profile. Each card displays:
- **Scrobbled Artists** — number of unique music artists in their history
- **Watched Movies** — number of movies tracked
- **Watched Shows** — number of TV shows tracked
- **Recommendations** — total count broken down into sub-chips (X movies, X shows, X artists, X albums)

A "Configure" link on each card takes you to that user's detail page. If a sync is running, a spinning "Syncing…" badge appears, and the page auto-refreshes every 3 seconds until it finishes.

### Recommendations
The recommendations page shows a responsive grid of recommendation cards (6 across on wide screens, scaling down to 2 on mobile). Each card contains:

- A **poster image** (movie/show artwork from TMDb, or artist image fetched from Lidarr if configured)
- A **type badge** (artist / album / movie / show) in the appropriate accent color
- The **title**
- Sub-info: artist name for albums, year and star rating for movies/shows
- A **match reason** — a brief human-readable explanation of why this was recommended (e.g. "similar to artists you know", "matches your indie/rock taste")
- A **score bar** — a thin gradient bar showing the match score as a percentage
- Genre tags for movies and shows
- **Action buttons** depending on content type (see below)
- A **Dismiss** button to permanently remove a recommendation

**Action buttons per type:**
- *Music (artist/album):* Streaming service links (Spotify, Apple Music, YouTube Music, Tidal, Amazon Music — configurable), a YouTube preview button, and a Lidarr add button
- *Movies:* TMDb link, Metacritic, IMDb, JustWatch, and a Radarr add button
- *TV Shows:* TMDb link, Metacritic, TVDB, JustWatch, and a Sonarr add button

**YouTube Preview:** When a YouTube API key is configured, the music preview button opens a modal with an embedded YouTube player that auto-plays the best matching video. If no API key is configured, the button instead links out to a YouTube search.

**Release Window Filters:** A collapsible panel at the top lets you set year range filters independently for music, movies, and TV. Movie and TV filters apply immediately to the displayed results; the music filter applies on the next sync. This lets you surface, for example, "only movies from 2015 onwards" or "only albums released after 2020."

**Type tabs:** Filter buttons at the top of the page let you view All, Artists, Albums, Movies, or Shows independently.

**User selector:** A dropdown at the top lets you switch between user profiles and view recommendations for different people.

### Taste Profile
The Taste Profile page is where the system's learned preferences are displayed and can be manually tuned. It shows three side-by-side category columns:

- **Music Taste** (purple accent) — genre/style tags learned from scrobble history
- **Movie Taste** (blue accent) — genre/keyword tags learned from watched movies
- **TV Taste** (green accent) — genre/keyword tags learned from watched shows

Each tag appears as a row with:
- The tag name
- A **5-bar override widget** — five clickable bars that let you manually boost or suppress the influence of that tag
- The computed weight as a percentage
- A horizontal fill bar showing the weight visually

**Override bar levels:**
1. Exclude — actively suppresses this tag from influencing recommendations
2. Downweight — reduces its influence below what was computed
3. Neutral — default (no override)
4. Boost — amplifies this tag's influence
5. Strong boost — maximum amplification

Clicking a bar saves the override instantly via AJAX (no page reload). Clicking the currently-active bar resets it back to neutral. A small "Saved" toast confirmation appears at the bottom-right.

**Grounding Tags:** At the bottom of each category card is an "Add grounding tag" form. This is one of the most powerful features: you can manually pin a genre or style tag into your profile even if the system hasn't learned it yet from your history. For example, you could ground your music profile with "shoegaze" or "lo-fi" to steer recommendations toward content you know you like but might not have much history for yet. You set an initial weight using the same 5-bar picker. Grounding tags are pinned permanently — they won't be removed by re-syncs — and are displayed separately with a pin icon. Once a sync has processed them, they appear alongside the computed tags and can be removed at any time.

### Status
The Status page has two sections:

1. **Infrastructure** — shows the connection status of all globally-configured services (Radarr, Sonarr, Lidarr, TMDb, MusicBrainz, etc.). Each service shows a colored dot (green = ok, red = error, gray = disabled/not configured) and the enabled/disabled/error state.

2. **Per-profile Sources** — for each active user profile, shows all enabled data sources and their last sync time and item count, or an error message if the last sync failed.

### User Profiles (main list)
The User Profiles page is a management hub with two columns:

**Left column — User list:**
- Create new user profiles with a name (e.g. "Default", "Partner", "Kid")
- Each profile row shows: avatar initial, name, scrobble count, creation date, and tag counts per category (e.g. "42 music tags · 18 movie tags")
- Clicking a profile row navigates to the user detail page
- Each row has inline Rename, Activate/Deactivate, and Delete actions
- Profiles defined in `config.yaml` but not yet in the database show an "Import User" button
- Profiles in the database with no `config.yaml` entry show a warning

**Right column — CSV Import Files:**
- Scans a configured directory (`config/imported_data/last.fm/`) for Last.fm export CSV files
- Each file shows its filename, row count, import date, assigned user, and status badge (pending / imported / error)
- Each file can be assigned to a user via a dropdown, then imported with one click
- **Re-import** replaces any existing scrobbles from that file
- **Purge** removes all scrobbles imported from that file without deleting the file itself — useful for reassigning to a different user
- **Enrich via Last.fm** (when Last.fm API is configured) — fetches genre tags from the Last.fm API for every artist in the imported CSV. This is essential: without enrichment, artists contribute to your profile by play count only; with enrichment, they contribute genre/style tags that power the recommendation engine
- A scrobble totals bar chart shows each user's total scrobble count relative to the others

A "How it works" info card explains the workflow to new users.

### User Detail
Each user has their own detail page, reached by clicking their name on the User Profiles page or from the Dashboard.

**Left column:**
- **Data Sources panel** — an accordion of all configurable per-user data sources. Each source has:
  - A colored icon indicating the service (red for Last.fm, yellow for Plex, etc.)
  - Name and last-sync status
  - An on/off toggle switch (saves instantly via AJAX)
  - An "Edit" button that expands a form to enter credentials/tokens
  - Sensitive fields (tokens, passwords) are masked with dots and require clicking "Change" to reveal an input
  - A **Test** button (on supported sources) that immediately tests the connection and shows a green checkmark or red error inline
- **Recent Scrobbles** — the last ~10 scrobbled tracks with artist, album, and timestamp
- **CSV Imports** — a summary of all CSV files imported for this user, with a link to manage them

**Right column:**
- **Taste Profile summary** — top tags from each category displayed as colored mini-chips with weight percentages. A "View full →" link goes to the Taste Profile page filtered to this user
- **Top Recommendations** — the top 5 music, movie, and TV recommendations for this user. A "See all N →" link goes to the full Recommendations page

---

## Scrobbling & Data Sources

"Scrobbling" in recommendarr means recording what you've listened to, watched, or played, and storing that history locally so it can inform your taste profile and power the Music Explorer's Songs layer.

The app supports multiple sources per user, and each source can be independently enabled or disabled.

### Music Sources

**Last.fm CSV Export:**
The primary way to import music history. Export your scrobble history from `lastfm.ghan.nl/export` and drop the CSV file into `config/imported_data/last.fm/`. The app scans this directory automatically. You assign each file to a user and click Import. The app reads every row (timestamp, artist, album, track) and stores it in a local scrobble history table. Multiple CSVs can be imported for the same user — useful for importing history from multiple Last.fm accounts or re-importing after corrections.

**Last.fm API (live sync):**
If you configure a Last.fm API key and username in a user's sources, the app can pull recent scrobbles directly from the Last.fm API on each sync. This supplements CSV imports with up-to-date listening data.

**ListenBrainz:**
An open-source alternative to Last.fm. Configure a user's ListenBrainz username and the app syncs their recent listens.

**Plex (music):**
Using a user-specific Plex X-Plex-Token (a per-user API token from your Plex server), the app can pull the user's Plex play history for music. Each user provides their own token, so play history stays private per-person.

**Local media scanner:**
Can scan a local music library path and register what's present, feeding it into the profile.

### Video Sources

**Plex (movies & TV):**
The same Plex X-Plex-Token used for music also covers movies and TV — the app pulls Plex watch history for movies and shows separately. Because tokens are per-user, each person's watch history is tracked independently.

**Watcharr:**
Watcharr is a self-hosted watch-tracking app. If you use it, the app can pull your watched/rated movie and show lists from its API.

**YouTube History:**
If you export your YouTube watch history (Google Takeout), the app can parse it and use it as a signal for video content preferences.

### Infrastructure Sources (global/shared)

These are not per-user — they describe your media library and are shared across all profiles.

**Radarr** — syncs your entire movie library. Every movie in your Radarr library is registered as "known" so it won't be recommended back to you.

**Sonarr** — syncs your TV show library. Same concept as Radarr.

**Lidarr** — syncs your music library (artists and albums). Artists and albums already in Lidarr are marked as owned and won't be recommended as new additions.

---

## Taste Profile System

The taste profile is the heart of recommendarr. It answers the question: "given everything this user has listened to and watched, what genres, styles, and themes do they care about — and how much?"

### How it's built

When a sync runs, the builder collects all known items for a user from their enabled sources. For each item (artist, movie, show), it looks up associated tags:
- For music: Last.fm genre/style tags fetched via API (or enrichment), MusicBrainz tags
- For movies: TMDb genre names, keyword tags
- For TV: TMDb genre names

Tags that describe user behavior rather than content style ("seen live", "favorites", "awesome", "love", etc.) are filtered out automatically. Only meaningful content descriptors contribute.

### Weighting & normalization

Tags are weighted based on how many items carry them and how frequently those items appear in the user's history. The more you listen to artists tagged "shoegaze", the higher "shoegaze" scores in your music profile. The weights are then normalized so the top tag = 100% and all others are proportional. This normalized weight is what's stored and displayed.

The top 30 music tags and top 20 movie/TV tags are kept active for recommendations.

### User override bars

On the Taste Profile page, every tag can be manually overridden using a 5-bar widget. The five levels are:
1. **Exclude** — this tag actively hurts scores for items that match it
2. **Downweight** — partial suppression
3. **Neutral** — no override, use computed weight
4. **Boost** — amplifies this tag's influence beyond what was computed
5. **Strong boost** — maximum amplification

Overrides persist across re-syncs. They let you correct the algorithm when it over- or under-represents something. For example, if you listened to a lot of Christmas music in December and don't want "christmas" to dominate your profile, set it to 1 (Exclude).

### Grounding tags

Grounding is the mechanism for seeding your profile with genres you want to explore but don't yet have much history for. You type any tag (e.g. "ambient", "post-punk", "neo-noir") and assign it an initial weight (1–5 bars). After the next sync, that tag is pinned into your profile with the specified weight and stays there permanently, actively steering recommendations toward that territory.

This is especially useful when starting fresh — you can immediately tell the system "I like these things" without needing to build up months of scrobble history first.

### Per-profile isolation

Each user has their own completely independent taste profile. Syncing one user's data never affects another user's profile. Infrastructure sources (Radarr/Sonarr/Lidarr) populate a shared "known items" list (so the system knows what's already owned), but the taste profile computation is run separately for every active user.

---

## Recommendation Engine

After building the taste profile, the engine scores candidates from external discovery services and ranks them.

### Music recommendations

Using the Last.fm API, the engine fetches "similar artists" for the artists in your library. It then scores each candidate by how well its tags align with your music taste profile. Artists already in your Lidarr library are excluded. A release window filter (optional) can restrict to albums released after a certain year, which is useful for surfacing "albums you don't own yet from artists you already like."

### Movie recommendations

Using TMDb's discover and similar-movie APIs, the engine fetches candidates based on genres and keywords matching your movie taste profile. Movies already in your Radarr library are excluded. Year range filters (min and max) apply.

### TV recommendations

Similar to movies: TMDb discover and similar-show lookups filtered against your TV taste profile, with Sonarr library exclusion and optional year range filtering.

### Scoring

Each candidate receives a score from 0 to 1 based on tag overlap with the taste profile, weighted by both the profile weights and any user overrides. The score drives both the sort order on the Recommendations page and the fill percentage of the score bar shown on each card.

A human-readable **match reason** is generated for each recommendation explaining the primary tags that drove the match.

Recommendations are regenerated on every sync. Dismissed recommendations are permanently excluded for that user.

---

## Music Explorer

The Music Explorer is the most developed of the three explorers. It's an interactive graph browser for discovering bands and artists by exploring their relationships — band members, side projects, collaborators — with all data stored locally in a SQLite cache.

### Search

A search box at the top queries MusicBrainz for artists by name. Results appear in a dropdown, each showing the artist name, type (group/person/etc.), country, and — if Lidarr is configured — whether they're already in your library (shown with a green "in library" indicator).

### Node cards

Clicking a search result opens that artist as a **node card** in a vertically-stacked chain below the search box. Each card has:

- A header with the artist name, type, and a close (×) button
- An icon area — if Lidarr is configured, this is replaced with the artist's actual photo fetched from Lidarr's metadata server
- Genre/style tag pills (from MusicBrainz tags)
- An "Add to Lidarr" button — if the artist is already in your library, this becomes a green "In Lidarr" indicator instead
- Streaming service links (Spotify, Apple Music, YouTube Music, Tidal, Amazon Music — whichever you've enabled in config)
- A YouTube preview button
- A MusicBrainz link

Below the header section, each artist card has lazy-loaded sections:

**Members / Member Of:**
- If the artist is a band: a "Members" section listing current and former members as clickable pills. Active members are styled normally; inactive/past members are slightly dimmed. Clicking a member pill opens their artist card as the next node in the chain, allowing you to drill into their solo work or other bands
- If the artist is a person: a "Member Of" section showing bands they've been part of

**Albums:**
A "Load albums" button triggers a fetch of the artist's discography from MusicBrainz (cached locally). Albums are displayed in a collapsible list sorted by year. Each album row shows the title, year, and type (Album/EP/Single). Clicking an album row expands it to show the **tracklist** (also fetched and cached from MusicBrainz). Each album header includes:
- A "Lidarr" add button to add just that album to Lidarr (the artist is added silently with no monitoring so only the specific album is tracked)
- A green "Owned" badge and disabled button if that album is already in your Lidarr library
- Streaming links for the album

**Songs:**
A "Load tracks" button fetches that artist's top tracks from your locally-stored scrobble history (from the Last.fm CSVs you've imported). Tracks are displayed as clickable "song pills" showing the track name and total play count across all your users.

Clicking a song pill opens a **Song card** as the next node in the chain. Song cards show:
- Per-listener play-count bars: a horizontal bar for each user who has scrobbled the track, showing their individual play count relative to the highest
- Streaming service links (artist + track query)
- A YouTube preview button (inline embedded video if YouTube API key is configured)
- A link to the MusicBrainz recording page

### Local caching & refresh

All data fetched while browsing — artist details, relationships, albums, tracks — is stored in the local database. When you revisit the same artist, data loads instantly from the DB rather than hitting external APIs. A **Refresh** button on each card forces a fresh fetch from the live API and updates the local cache, so you can always get the latest data when you want it.

### Chain navigation

The explorer maintains an ordered chain of node cards. When you click a member or song pill, the new card appears below the current one and the view scrolls to it. If you click the same artist that's already in the chain, that card is highlighted (flashes its border) instead of duplicating. Clicking the × button on any card collapses it and all cards that came after it in the chain (those that were opened from it).

### Lidarr integration

If Lidarr is configured:
- Artist cards show whether the artist is in your library
- Album rows show which albums are owned vs. not owned (green "Owned" badge)
- "Add to Lidarr" buttons appear on both the artist card (adds the artist with all albums monitored) and individual album rows (adds just that album)
- The "Add to Lidarr" button transitions through states: normal → spinning → green checkmark (success) or red warning (error, resets after 4 seconds)
- Artist images are fetched from Lidarr's metadata layer and injected into the node icon

---

## Movie Explorer

The Movie Explorer follows the same node-card chain pattern as the Music Explorer, but for films.

**Search** queries TMDb for movies. Results include the movie title, year, and rating.

**Movie cards** show:
- Title, year, rating, overview/description
- Genre tags
- Cast members as clickable pills — clicking a cast member opens a **Person card** for that actor
- Production companies as clickable pills — clicking one opens a **Company card** showing other movies from that studio
- Links to TMDb, Metacritic, IMDb, and JustWatch
- A "Add to Radarr" button — links to Radarr's add-movie search for that film
- A "Add to Plex Watchlist" button — adds the movie to the authenticated user's Plex watchlist via the Plex cloud API

**Person cards** (actors/directors) show:
- Name and known-for department
- Their filmography as clickable movie pills, each with vote count displayed

**Company cards** show:
- The production company name
- A list of notable movies from that company as clickable pills

All browsed data is stored locally for instant re-access.

---

## TV Explorer

The TV Explorer mirrors the Movie Explorer in structure and UX, adapted for television.

**Search** queries TMDb for TV shows.

**Show cards** show:
- Title, year, rating, overview
- Genre tags
- Cast as clickable pills → Person cards (with TV credits)
- Networks as clickable pills → Network cards (showing other shows on that network)
- Links to TMDb, Metacritic, TVDB, and JustWatch
- A "Add to Sonarr" button
- A "Add to Plex Watchlist" button

**Person cards** in the TV context show the person's television credits instead of film credits.

**Network cards** use TMDb's discover API to show other shows from the same network.

All browsed data is stored locally and can be refreshed on demand.

---

## Configuration

recommendarr is configured through a `config.yaml` file that lives in a `config/` directory alongside the database.

The config has two main sections:

### Infrastructure (global)

These are services shared across all users:
- **Radarr** — URL and API key for your Radarr instance
- **Sonarr** — URL and API key for your Sonarr instance
- **Lidarr** — URL and API key for your Lidarr instance
- **TMDb** — API key for The Movie Database (required for movie/TV recommendations and explorers)
- **MusicBrainz** — user agent string (MusicBrainz requires a descriptive user agent; no key needed)
- **Last.fm** — global API key (used for artist tag enrichment and music recommendations)
- **Streaming services** — which services to show link buttons for (Spotify, Apple Music, YouTube Music, Tidal, Amazon Music), toggled on/off per service

### Profiles (per-user)

Each user can have their own set of data sources. A profile entry in config.yaml includes:
- **Last.fm** — username (and optional API key override), toggle enabled/disabled
- **ListenBrainz** — username, toggle
- **Plex** — server URL + X-Plex-Token (this is the per-user Plex token, not a shared server token), toggle
- **Watcharr** — server URL + API key, toggle
- **YouTube** — path to the Google Takeout history JSON file, toggle
- **Local media** — paths to local media directories, toggle

Profiles defined in `config.yaml` can be imported into the database from the User Profiles page. The database and config.yaml are kept in sync — the UI warns you if they diverge.

### Authentication

The app supports optional username/password authentication. On first run with auth enabled, a `/setup` page allows creating the first admin account. Subsequent logins are via a `/login` page. All POST routes are CSRF-protected (form posts use a hidden token; AJAX calls use a request header). Session secrets are stored in `/config/secret.key` and auto-generated on first run.

Authentication can be completely disabled for single-user home setups where you trust your local network.

---

## Deployment

recommendarr is designed to run in Docker. The container uses Gunicorn as the WSGI server and APScheduler for background sync jobs that run automatically on a schedule (so your recommendations stay fresh without manual intervention).

The database and config file live in a mounted volume (`/config`) so they persist across container restarts and updates.

For local development, the app can be run directly with Python and detects a relative `config/` directory automatically. An `.env` file can be used for local environment variable overrides.

---

## Data Flow Summary

```
User history sources (CSV, Last.fm API, Plex, ListenBrainz, Watcharr, YouTube)
         ↓
   Local SQLite DB (scrobble_history, known_items)
         ↓
   Tag enrichment (Last.fm API genre tags per artist/movie/show)
         ↓
   Taste profile builder (weighted tag vector per category per user)
         ↓
   User overrides + grounding tags applied
         ↓
   Recommendation engine (score candidates from Last.fm similar artists, TMDb discover)
         ↓
   Ranked recommendations stored in DB
         ↓
   Displayed on Recommendations page with action buttons (Lidarr / Radarr / Sonarr)
```

```
User searches in Music/Movie/TV Explorer
         ↓
   Check local DB cache first (instant)
         ↓ (on miss)
   Fetch from MusicBrainz / TMDb / Last.fm API
         ↓
   Store in local DB (permanent cache, refreshable on demand)
         ↓
   Rendered as node card in explorer chain
```

---

## Key Design Decisions

- **Everything is cached locally.** API calls are only made on first access; subsequent loads are instant from the DB. You can always force a refresh.
- **Per-user isolation.** Taste profiles, recommendations, and scrobble histories are fully isolated per user. Infrastructure (owned media libraries) is shared.
- **Grounding prevents cold-start problems.** New users don't have to wait weeks of listening before recommendations are meaningful — they can manually seed their profile.
- **Override bars give tuning power without complexity.** You don't set numeric weights; you just move bars up or down. The UI is self-explanatory.
- **Explorers are for discovery, not just lookup.** The chain/graph model means you naturally discover new artists by following connections rather than searching for things you already know.
- **The *arr integrations close the loop.** Finding something interesting in the explorer or on the recommendations page leads directly to adding it to your library without switching apps.
