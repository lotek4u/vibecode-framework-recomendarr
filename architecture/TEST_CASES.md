# recommendarr Test Cases

These are user-story-style test cases written so that an LLM or human tester can follow each one step by step. Each test describes a complete user journey, the actions to take, and the expected outcome.

---

## Music Explorer

### TC-ME-01: search a band, open their card, verify tags and streaming links appear

Open the Music Explorer page. Click the search box and type the name of a well-known band such as Radiohead. Wait for the search dropdown to appear. Confirm the band appears in the dropdown with its type listed such as Group and its country. Click the result. Confirm a node card appears below the search box showing the band name, genre tag pills such as alternative rock or art rock, streaming service link buttons for any services that are configured, a YouTube preview button, and a MusicBrainz link. A Load Albums button and a Load Tracks button should appear as lazy sections. The card should have a close button in its top right corner.

### TC-ME-02: search a band, close the card, confirm the chain is empty

Open the Music Explorer page. Search for a band and click the result to open their node card. Confirm the card is visible in the chain. Click the close button in the top right corner of the card. Confirm the card is removed and the chain area returns to an empty placeholder state. Confirm no ghost card or empty space remains.

### TC-ME-03: search a band, click a member, load their discography, add the full artist to Lidarr

Open the Music Explorer page. Search for a band with known members such as Pink Floyd. Open their card. Scroll to the Members section and click one of the listed members such as David Gilmour. A second node card should appear for that member. Click Load Albums on the member card. Wait for the discography list to appear. Confirm multiple albums are listed. Click the Add to Lidarr button on the member artist card, not on a specific album. Confirm success is indicated, meaning the full artist discography has been added with all albums monitored. If the artist is already in Lidarr, confirm an appropriate error state is shown.

### TC-ME-04: load an artist's discography and add a single album to Lidarr

Open the Music Explorer page. Search for a band and open their card. Click Load Albums. Wait for the discography list to appear. Find a specific album in the list. Click the Lidarr button in the header of that album row. Confirm success is indicated. Confirm the artist-level Add to Lidarr button was not affected, since only this specific album should be added with the artist in an unmonitored state.

### TC-ME-05: expand an album row and verify the tracklist appears

Open the Music Explorer page. Search for a band such as Massive Attack. Open their card. Click Load Albums. Wait for the list of albums to appear. Click on a specific album row to expand it. Confirm individual track names and positions appear inside the expanded row. Confirm other album rows remain collapsed and unaffected.

### TC-ME-06: open the same artist twice from different paths and confirm it highlights instead of duplicating

Open the Music Explorer page. Search for and open a solo artist such as Thom Yorke. In the Member Of section click Radiohead to open a second card. In the Radiohead Members section click Thom Yorke again. Confirm that instead of a third duplicate card appearing, the existing Thom Yorke card is highlighted to indicate it is already in the chain. Confirm the page scrolls to the existing card. Note: this test relies on Thom Yorke and Radiohead having the correct member relationships in MusicBrainz data; choose artists with well-known and stable entries if this path fails.

### TC-ME-07: load tracks for an artist, click a track, see per-listener play counts

Open the Music Explorer page. Search for an artist that exists in at least one imported Last.fm scrobble CSV. Open their card. Click Load Tracks. Wait for track pills to appear. Confirm each pill shows the track name and a play count number. Click one of the track pills. Confirm a Song card appears as the next node in the chain. Confirm the song card shows each user who has scrobbled the track with their play count, displayed proportionally so users with higher counts appear more prominent. Confirm streaming service links and a YouTube preview button are present on the song card.

---

## Movie Explorer

### TC-MOV-01: search a film, browse a cast member's filmography, add a different film to Radarr

Open the Movie Explorer page. Search for a film and click the result. Confirm the movie card shows a poster thumbnail, title, year, rating, genre tags, and a cast section. Click a cast member. Confirm a Person card appears below with a profile photo or placeholder, the actor name, and their filmography listed as clickable pills sorted by popularity. Click a different film from the filmography. Confirm that film opens as a new card. Click the Add to Radarr button on that film card. Confirm it opens Radarr's add movie search page in a new tab pre-filled with the film title.

### TC-MOV-02: click a production company and browse other films from that studio

Open the Movie Explorer page. Search for a film and open its card. Find the production companies section and click one of the company pills. Confirm a Company card appears showing the studio name and a list of other movies from that company as clickable pills. Click one of those film pills. Confirm that film opens as a third card in the chain, extending the exploration chain.

### TC-MOV-03: add a film to the Plex watchlist from the movie card

Open the Movie Explorer page. Search for a film and open its card. Click the Add to Watchlist button. Confirm success is indicated when the Plex API call completes, or an error state is shown and automatically clears if it fails. This test requires a Plex token to be configured for at least one user profile.

### TC-MOV-04: open the same movie twice from different paths and confirm it highlights instead of duplicating

Open the Movie Explorer page. Open a film card. Click a cast member to get their filmography. Click the same original film from the filmography list. Confirm the original film card is highlighted instead of creating a duplicate card in the chain.

---

## TV Explorer

### TC-TV-01: search a show, click a cast member, find another show they appeared in, add it to Sonarr

Open the TV Explorer page. Search for a TV show and open its card. Confirm the card shows a poster, title, year, rating, genre tags, network pills, and cast pills. Click a cast member. Confirm a Person card appears with that actor's television credits sorted by popularity. Click a different show from their credits. Confirm that show opens as a new card. Click the Add to Sonarr button. Confirm it opens Sonarr's add show search in a new tab pre-filled with the show title.

### TC-TV-02: click a network pill and browse other shows on that network

Open the TV Explorer page. Search for a show on a well-known network such as Succession on HBO. Open the show card. Click the HBO network pill. Confirm a Network card appears listing other shows from that network. Click one of those shows. Confirm it opens as a new show card. Click Add to Sonarr on that card. Confirm it opens Sonarr's add show page in a new tab.

### TC-TV-03: add a show to the Plex watchlist

Open the TV Explorer page. Search for a show and open its card. Click the Add to Watchlist button. Confirm success is indicated when the Plex API call completes, or an error state is shown and automatically clears if it fails. This test requires a Plex token configured for at least one user profile.

### TC-TV-04: close a middle card in a three-card chain and confirm child cards collapse

Open the TV Explorer page. Build a three-card chain: open a show card, click a cast member to open a Person card, then click a show from their credits to open a third card. Click the close button on the middle Person card. Confirm the middle card and the third card both disappear. Confirm only the original show card remains in the chain.

---

## User Profiles and CSV Import

### TC-USR-01: create a new user profile, assign a Last.fm CSV, import it, verify the scrobble count is recorded

Open the User Profiles page. Click the Add button and enter a name such as Partner. Click Create. Confirm the new profile appears in the list with zero scrobbles and a note about no taste profile yet. In the CSV Import Files panel on the right, find an available CSV file. Select the new user from the dropdown for that CSV. Click Import. Confirm an Importing status appears while the import is in progress. Confirm on completion the feedback shows the number of scrobbles imported, such as 47,382 scrobbles imported. Confirm the status badge updates to imported. Confirm the profile row now displays the scrobble count.

### TC-USR-02: import a CSV under the wrong user, purge it, reassign to the correct user, reimport

Open the User Profiles page. Find a CSV that has been imported under the wrong user. Click the Purge button for that CSV. Confirm a confirmation dialog appears before any action. Confirm the dialog explains that scrobbles will be removed but the file kept. Confirm after purging that the status badge returns to pending and the user's scrobble count decreases accordingly. Change the user dropdown for that CSV to the correct user. Click Import. Confirm the import succeeds and the correct user now has the scrobble count.

### TC-USR-03: enrich an imported CSV via Last.fm API, run a sync, verify genre tags appear in the taste profile

Open the User Profiles page. Find an imported CSV row that shows an Enrich via Last.fm button. This button only appears when the Last.fm API is configured. Select the correct user in the dropdown. Click Enrich via Last.fm. Confirm a Fetching tags status appears while the enrichment is in progress. Wait for completion and confirm feedback shows how many artists were tagged successfully. Trigger a sync from the Dashboard. After the sync completes, navigate to Taste Profile for that user. Confirm genre tags such as indie rock, alternative, or electronic appear in the Music Taste column with percentage weights. This test requires a Last.fm API key configured in the infrastructure settings.

### TC-USR-04: deactivate a user profile and confirm they are excluded from recommendations and syncs

Open the User Profiles page. Find an active user profile. Click the deactivate toggle. Confirm the profile row shows an inactive state. Trigger a sync from the Dashboard. Navigate to Recommendations and open the user selector. Confirm the deactivated user does not appear as an option. Navigate to the Dashboard and confirm the inactive profile's stat card is no longer shown. Reactivate the profile by clicking the toggle again and confirm the user returns to active status.

### TC-USR-05: rename a user profile and confirm all references update

Open the User Profiles page. Find a user profile. Click the rename icon. Confirm an inline text input appears within the same row with the current name pre-filled. Clear the field, type a new name, and click Save. Confirm the new name is shown in the profile row after saving. Confirm all references such as the CSV assignment dropdown and the recommendation user selector also reflect the updated name.

---

## User Detail and Data Sources

### TC-DET-01: configure a Plex token for a user and verify the connection succeeds

Open the User Profiles page and click a user's name to open their detail page. In the Data Sources panel find the Plex source. Click the toggle switch to enable it. Click Edit to expand the configuration form. Enter the Plex server URL and the user's personal Plex token. Click Test. Confirm a Connected message is shown on success or an error message is shown on failure. Click Save. Confirm the source shows the saved values on reload and still shows the enabled status.

### TC-DET-02: disable a data source for one user without affecting another user

Open the User Detail page for the first user. Toggle a data source such as Watcharr to disabled. Navigate to the User Detail page for a second user. Confirm that the same data source is in whatever state it was previously set to for that second user and has not been changed. Each user's source toggles are fully independent.

### TC-DET-03: user detail page shows recent scrobbles and top recommendations without additional navigation

Open the User Profiles page and click a user to open their detail page. Confirm the left column shows a Recent Scrobbles section with the last several scrobbled tracks including track title, artist, album, and timestamp. Confirm the left column also shows CSV Imports with a list of files and their status badges. Confirm the right column shows a Taste Profile summary with tag chips color-coded by category with percentage weights. Confirm the right column shows Top Recommendations with the top music, movie, and TV picks and a link to see all recommendations for that user.

---

## Taste Profile

### TC-TP-01: boost a genre tag using the override bars and confirm the change saves without a page reload

Open the Taste Profile page for a user who has a populated music taste profile. Find any tag in the Music Taste column. Click the fifth bar on its override bar widget. Confirm all five bars become active immediately without a page reload. Confirm a Saved confirmation appears and fades away. Reload the page. Confirm the same tag still shows all five bars active, confirming the override was persisted to the database.

### TC-TP-02: exclude a tag using the override bars, then reset it to neutral by clicking the active bar again

Open the Taste Profile page. Find a tag in the Music Taste column. Click the first bar to set it to Exclude. Confirm only the first bar is active. Confirm a Saved confirmation appears. Now click the first bar again since it is the currently active bar. Confirm all bars clear and the tag returns to a neutral unset state. Confirm a Saved confirmation appears again. Reload the page and confirm no bars are active for that tag.

### TC-TP-03: add a grounding tag, confirm it appears as pending, then appears in the computed profile after a sync

Open the Taste Profile page. Scroll to the bottom of the Music Taste card and find the Add grounding tag form. Type a genre you want to explore but have little scrobble history for, such as post-punk. Click the fourth bar in the bar picker to set a Boost level. Click the Pin button. Confirm the tag appears immediately in a Pending grounding tags section, indicating it has not yet been incorporated into the computed profile. Trigger a sync from the Dashboard. Return to Taste Profile. Confirm the tag has moved from the pending section into the main computed tag list. Confirm it also appears in the Pinned grounding tags section with a remove button.

### TC-TP-04: remove a grounding tag and confirm it no longer pins after the next sync

Open the Taste Profile page. Find a pinned grounding tag in the Pinned grounding tags section. Click the remove button next to it. Confirm the tag disappears from the pinned section. Trigger a sync. Return to Taste Profile. Confirm the tag is no longer marked as pinned. Confirm it still appears in the main tag list only if the user's actual scrobble history supports it; otherwise it should be gone entirely.

### TC-TP-05: verify that override bars for one user do not affect a second user's profile

Open the Taste Profile page for User A. Find a tag such as metal and boost it to five bars. Switch to User B using the user selector. Find the same metal tag if it exists. Confirm its override bars are in a neutral unset state and have not inherited User A's boost. Each user's overrides are stored independently.

---

## Recommendations

### TC-REC-01: filter recommendations by type tab and verify only that content type is shown

Open the Recommendations page. Click the Movies filter tab. Confirm only movie cards appear in the grid and no artist, album, or show cards are visible. Confirm the Movies tab has an active visual style and the other tabs appear inactive. Click the TV tab. Confirm only show cards appear. Click All to return to the full mixed list.

### TC-REC-02: dismiss a recommendation and confirm it does not reappear after a sync

Open the Recommendations page. Find any recommendation card. Click its Dismiss button. Confirm the card is no longer present after the dismissal. Trigger a full sync from the Dashboard. Return to Recommendations. Confirm the dismissed item has not reappeared. Dismissals are permanent for the user that performed them.

### TC-REC-03: set a release window year filter for movies and verify only films within the range are shown

Open the Recommendations page. Click the Release Window button. Confirm a filter panel expands with independent year range fields for music, movies, and TV. Enter a minimum year such as 2010 in the Movies from year field. Click Save and Apply. Confirm an active filter indicator is shown on the Release Window button. Confirm any movie recommendations with a release year before 2010 are no longer visible. Confirm music and TV show recommendations are not affected by the movie year filter.

### TC-REC-04: clear all release window filters and confirm all recommendations return

Open the Recommendations page when active year filters are applied. Click the Release Window button to expand the panel. Click the Clear All button. Confirm the active filter indicator disappears from the button. Confirm all previously filtered-out recommendations reappear in the grid.

### TC-REC-05: open a YouTube preview for a music recommendation and confirm it plays inline

Open the Recommendations page. Find a music artist or album recommendation. Click the YouTube preview button. Confirm a modal dialog opens showing the content name. Confirm an embedded YouTube player appears with the best matching video and that the video plays. Confirm closing the modal stops the video. This test requires a YouTube Data API key configured in settings.

### TC-REC-06: switch between user profiles in the recommendation user selector and see different results

Open the Recommendations page and note which user is selected and what recommendations appear. Use the user selector to switch to a different user with a different taste profile. Confirm the heading updates to show that user's name. Confirm the recommendations grid changes to reflect the second user's independent taste profile and viewing and listening history.

---

## Dashboard

### TC-DASH-01: trigger a sync and confirm the in-progress indicator appears and clears on completion

Open the Dashboard page. Click the Sync Now button. Confirm a Syncing indicator appears on the page. Confirm the page updates periodically while the sync is running. Confirm the indicator disappears when the sync completes. Confirm profile stat cards reflect any newly generated recommendations.

### TC-DASH-02: stat cards appear for active profiles and not for inactive ones

Ensure at least two user profiles exist, one active and one inactive. Open the Dashboard. Confirm one stat card appears per active profile showing scrobbled artists, watched movies, watched shows, and total recommendation count broken down by type. Confirm the inactive profile does not have a stat card. Confirm the default profile has a visually distinct appearance compared to the others.

---

## Status Page

### TC-STS-01: status page shows correct indicators for configured and misconfigured services

Navigate to the Status page. Confirm each infrastructure service such as Radarr, Sonarr, Lidarr, and TMDb has a row with a status indicator and label. Confirm services that are enabled and reachable show an Enabled status. Confirm services that are disabled show a Disabled label. Temporarily misconfigure a service by entering a wrong API key and re-check the status page. Confirm it shows an error or not configured state for that service.

### TC-STS-02: status page shows per-profile source sync history with timestamps and item counts

Navigate to the Status page. Scroll past the Infrastructure section to the per-profile section. Confirm each active user profile appears as a card showing its name. Confirm enabled sources show their last sync timestamp and item count. Confirm sources that have never synced show an Enabled, never synced label. Confirm sources with errors show the error message. Confirm profiles with no source configuration show an appropriate warning.

---

## Authentication

### TC-AUTH-01: first-run setup creates an account and logs in automatically

On a fresh installation with no users in the database and authentication enabled, navigate to the app root URL. Confirm you are redirected to the setup page instead of the dashboard. Enter a username and password and click Create Account. Confirm you are automatically logged in and redirected to the Dashboard. Confirm the navbar shows your username and a Logout option. Confirm navigating to the setup page again redirects back to the Dashboard since setup is complete.

### TC-AUTH-02: logging out ends the session and protected pages redirect to login

While logged in, confirm you can access the Dashboard and Recommendations pages. Click the Logout option in the navbar. Confirm you are redirected to the login page. Try to navigate directly to the Recommendations page. Confirm you are redirected back to the login page. Confirm that any API call made to a protected endpoint while logged out returns a 401 status rather than silently redirecting.

### TC-AUTH-03: an incorrect password shows a generic error without revealing whether the username was wrong

Navigate to the login page. Enter a valid username with an incorrect password and click Login. Confirm a generic error message appears such as Invalid username or password that does not distinguish between a wrong username and a wrong password. Confirm the password field is cleared and the username field retains its value.

---

## Local Data Caching

### TC-CACHE-01: browse an artist, close the browser, reopen and confirm data loads instantly from cache

Open the Music Explorer. Search for an artist, open their card, and click Load Albums. Wait for the full discography to appear. Close the browser tab entirely. Reopen the app in a new tab and navigate to the Music Explorer. Search for the same artist and open their card. Confirm the card loads instantly without a visible loading state. Click Load Albums. Confirm the album list also appears instantly from the local database cache.

### TC-CACHE-02: confirm the cache age indicator appears on a previously browsed card and shows the correct age

Open the Music Explorer. Search for an artist you have browsed before so their data is already cached. Open their card. Confirm a cache age indicator is visible in the card header showing how long ago the data was fetched, such as "cached 2 days ago". Confirm the indicator is present on Movie Explorer and TV Explorer cards as well when browsing previously viewed content. Confirm the age reflects the actual time elapsed since the data was first fetched or last refreshed.

### TC-CACHE-03: click the cache age indicator to force a live re-fetch and confirm the card updates

Open the Music Explorer. Open a card for an artist whose data is cached. Note the current tag pills or member list. Click the cache age indicator in the card header. Confirm a loading state appears on the card while the fetch is in progress. Confirm the card content refreshes when the fetch completes. Confirm the cache age indicator resets to show a fresh fetch time such as "cached just now". Confirm subsequent loads of the same artist are again instant from the newly updated cache.

### TC-CACHE-04: confirm cards with data older than 30 days display the age indicator in a warning state

This test requires manipulating the cache timestamp directly in the database or waiting for 30 days to elapse. Set the cached_at timestamp for a browsed artist record to a date more than 30 days in the past. Open the Music Explorer and open that artist's card. Confirm the cache age indicator is displayed in a visually distinct warning style compared to a freshly cached card, such as an amber or red color. Confirm the same warning behavior applies to Movie Explorer and TV Explorer cards with equivalently stale timestamps.
