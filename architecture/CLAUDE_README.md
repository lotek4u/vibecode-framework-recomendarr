CLAUDE_README.md

This file is written for an LLM agent — specifically a Claude Code session — that is being handed this project for the first time. Read this file completely before reading anything else. It explains what this project is, what the two attached documents are for, how you should interpret them, and what your job is.


WHAT THIS PROJECT IS

recommendarr is a self-hosted web application that tracks what a user listens to and watches, builds a personalized taste profile from that history, and generates ranked recommendations for music, movies, and TV shows. It integrates with Radarr, Sonarr, and Lidarr so users can go directly from a recommendation to downloading it. It supports multiple user profiles in the same household, each with their own independent history and taste preferences.

The application described in the overview document is a real working implementation built with Python and Flask. You are not being asked to contribute to that existing codebase. You are being asked to build a new, similar application from scratch using whatever stack and architecture you believe is best suited to deliver all of the described features and pass all of the described test cases.


THE TWO DOCUMENTS


RECOMMENDARR_OVERVIEW.md

This is the feature specification. It describes every part of the application in detail: the visual design, the navigation structure, each page and what it contains, how scrobbling works, how the taste profile is built and tuned, how the recommendation engine scores candidates, how each of the three explorers work, what the configuration structure looks like, and what the deployment model is. Read this document as a product requirements document. It is the authoritative description of what the application should do and how it should feel to use.

When you interpret this document, treat every described feature as a requirement that must be implemented. The document is organized by feature area. Do not skip sections. Pay particular attention to the Taste Profile section since that is the most algorithmically complex part of the application. Pay particular attention to the Music Explorer section since it is the most interactively complex part of the application. The override bars, grounding tags, local caching with refresh, Lidarr integration, per-listener song breakdowns, and the chain navigation model are all mandatory features, not optional enhancements.

The visual design section describes a dark purple-accented UI. You do not have to replicate it exactly, but the application should feel like a modern media app rather than a configuration panel. Dark background, card-based layout, accent colors per content type (purple for music, blue for movies, green for TV), and responsive design are all expected.


TEST_CASES.md

This is the acceptance test suite. It contains test cases written as plain prose user stories, grouped by feature. Each test case describes a complete user journey: what pages to visit, what to click, what to enter, and what the expected outcome is. There are at least two test cases per major feature area.

Your implementation is considered correct when every test case in this document can be followed by a human tester or an LLM agent and the described expected outcomes occur without errors. These test cases are your acceptance criteria. Do not consider the project complete until all of them pass.

When you begin planning your implementation, map each test case to the features it exercises. Some test cases exercise multiple features simultaneously, which is intentional. Use the test cases to validate your implementation as you go, not just at the end.


HOW TO GET CLAUDE CODE RUNNING

Claude Code is Anthropic's official agentic coding CLI. It runs in your terminal and gives Claude direct access to your filesystem, shell, and editor so it can read code, write files, run commands, and work through complex tasks autonomously.

The official documentation is at https://docs.anthropic.com/en/docs/claude-code/overview

Install it with npm:
npm install -g @anthropic-ai/claude-code

Then run it from your project directory:
claude

You will need an Anthropic API key. Set it as an environment variable:
export ANTHROPIC_API_KEY=your_key_here

Or add it to your shell profile so it persists across sessions.


PLATFORM PREFERENCE ORDER

Claude Code runs on any Unix-like environment. Use whichever of the following applies to your situation, in this order of preference:

1. Native macOS — the best experience. Node.js and npm are available via Homebrew. Install with brew install node then npm install -g @anthropic-ai/claude-code. Run directly from your project directory in Terminal or iTerm2.

2. Native Linux — equally good. Install Node.js via your distro's package manager (apt, dnf, pacman, etc.) or via nvm for version control. Then install Claude Code via npm as above.

3. WSL2 on Windows — fully supported and works well. Install WSL2 with a Ubuntu or Debian distribution. Inside the WSL2 terminal, install Node.js via nvm or apt, then install Claude Code via npm. Run Claude Code from inside the WSL2 terminal, not from a Windows Command Prompt or PowerShell. Keep your project files inside the WSL2 filesystem (under /home/youruser/) rather than on the Windows filesystem (/mnt/c/) to avoid file permission and performance issues.

4. Container — if you prefer an isolated environment, you can run Claude Code inside a Docker container with Node.js installed. Mount your project directory into the container. This is the most portable option but requires the most setup. Use it if you cannot install software on the host machine directly.


INSTRUCTIONS FOR THE LLM AGENT


Your primary task is to build a new application that satisfies the feature description in RECOMMENDARR_OVERVIEW.md and passes all test cases in TEST_CASES.md. Here is how you should approach the work.

Start by reading both documents completely before writing a single line of code. You need to understand the full scope before making architectural decisions. The application has enough interconnected features that a wrong early decision (such as a schema that does not support per-user isolation, or a frontend pattern that makes the chain navigation difficult) will require expensive refactoring later.


STEP ONE: BREAK THE OVERVIEW INTO TODO FILES

After reading both documents, your first concrete deliverable is not code. It is a collection of TODO files. Do not start coding until this collection is complete and reviewed.

Read RECOMMENDARR_OVERVIEW.md from top to bottom and decompose it into a numbered series of TODO files. Each file represents one self-contained phase of the build. Name them sequentially: TODO-001.md, TODO-002.md, TODO-003.md, and so on. Store them in a folder called todos/ at the root of the project.

Each TODO file must contain the following sections. The phase name and number. A one-paragraph description of what this phase builds and why it comes at this point in the sequence. A list of the specific features, routes, database tables, UI components, and behaviors that must be completed before this TODO is considered done. A list of the test cases from TEST_CASES.md that this phase must satisfy, identified by their TC- identifiers. A list of any external APIs, services, or libraries this phase depends on. A notes section for anything a future Claude session needs to know before picking up this TODO.

The TODO files must be written in the correct order of operations. Each phase must be completable without depending on anything that comes in a later phase. A later phase may build on or extend an earlier phase but must not require it to be rearchitected. Think of the TODO files as a contract: if you complete TODO-004 exactly as written, you should be able to hand the project to a fresh Claude session with no other context and it should be able to do TODO-005 without confusion.

A reasonable decomposition looks roughly like this, though you should adjust based on your chosen stack and what makes sense given the dependencies you identify. Phase 1 covers project scaffolding: repository structure, chosen framework, database connection, config file loading, Docker setup, and a working dev server that returns a hello world response. Phase 2 covers authentication: the user account table, login and logout routes, session management, CSRF protection, and the first-run setup page. Phase 3 covers user profiles: creating, renaming, activating, deactivating, and deleting profiles, plus the database tables that hold per-user data in isolation. Phase 4 covers scrobble import: the CSV import flow for Last.fm exports, the scrobble history table, the purge and reimport actions, and the enrichment call to the Last.fm API for genre tags. Phase 5 covers infrastructure connectors: Radarr, Sonarr, Lidarr, TMDb, and MusicBrainz API clients, connection testing, and the sync that pulls library data into the known items table. Phase 6 covers the taste profile builder: tag collection from all enabled sources, noise tag filtering, weight computation, normalization, and per-user per-category storage. Phase 7 covers the recommendation engine: candidate scoring against the taste profile, user override bars, grounding tags, dismissed item exclusion, and release window filters. Phase 8 covers the Recommendations page and the Taste Profile page including the five-bar override widget and the grounding tag form. Phase 9 covers the Music Explorer: search, the chain navigation model, artist node cards with tags and streaming links, member and member-of relationships, albums and tracklists, the songs layer with per-listener bars, Lidarr add-artist and add-album actions, local DB caching, and the refresh button. Phase 10 covers the Movie Explorer using the same chain pattern: movie cards, person cards, company cards, Radarr add action, and Plex watchlist. Phase 11 covers the TV Explorer: show cards, person cards, network cards, Sonarr add action, and Plex watchlist. Phase 12 covers the Dashboard, Status page, per-profile stat cards, and the background sync scheduler. Phase 13 is final integration: run every test case, fix any failures, and verify the full application works end to end.

This is a guide, not a rigid rule. If you find that two phases are deeply interdependent and are safer combined, combine them. If a phase feels too large to complete in one focused session, split it. The goal is that each TODO file describes a chunk of work a Claude session can complete confidently, test against specific test cases, and hand off cleanly.


THE DEFERRAL RULE

When you are working through a TODO file and you encounter a decision that is not yet in scope for that phase, do not make it. Write a note in the current TODO file under a section called Deferred Decisions, describe the decision and why it cannot be made yet, and specify which future TODO number it belongs to. Then move on.

For example, if you are on Phase 3 (user profiles) and you realize you need to decide how per-user Plex tokens will be stored and validated, but that belongs to Phase 5 (connectors), write a note: "Token storage format for Plex deferred to TODO-005. For now, store as a plain string in the user sources config table. TODO-005 will decide whether to encrypt at rest." Then continue with Phase 3 without resolving the Plex question.

This rule exists because trying to make all decisions upfront leads to over-engineered early phases and analysis paralysis. Make decisions when you have enough context to make them well, which is when you are actually implementing the feature that depends on them. If a deferred decision turns out to require a minor change to an earlier phase (adding a column, renaming a field), that is acceptable and expected. What is not acceptable is letting a deferred decision block forward progress.

Do not go back and reopen a completed TODO to add deferred decisions from a later phase unless the earlier phase's code is provably broken. If it works, leave it. Refactoring a working phase to satisfy an abstract future concern is waste.


CONTINUING THE WORK ACROSS SESSIONS

Claude Code sessions have context limits. When a session ends, context is lost. The TODO files are your continuity mechanism. At the start of every session, read the todos/ folder to find the first TODO that is not marked complete. That is where you start. At the end of every session, update the current TODO file to reflect what was completed, what was partially done, and what remains. Mark a TODO as complete only when all its listed features are implemented and all its listed test cases pass.

Each TODO file should have a status line at the top that is either Not Started, In Progress, or Complete. When you mark a TODO as In Progress, add a brief note about where you are in it. This means a fresh session can pick up mid-TODO without having to re-read all the code.


THE ARCHITECTURE DECISION

Before writing TODO-001, you need to make one foundational decision: the technology stack. The original application used Python with Flask, SQLite, Gunicorn, and APScheduler. You are not required to use the same stack. Choose whatever you believe is best for delivering this application reliably. Common good choices are Python with Flask or FastAPI, Node.js with Express or Fastify, or a framework like SvelteKit or Next.js if you want a tighter frontend-backend integration. SQLite is a reasonable database for a self-hosted single-instance app of this scale. Whatever you choose, document the choice and its rationale at the top of TODO-001 and do not revisit it.


WORKING THROUGH THE TODO FILES

When you begin a TODO, read it completely. Then read the test cases it is responsible for. Then implement. Do not implement features that belong to a later TODO, even if they seem easy or closely related. Scope creep between TODOs is how projects become unmaintainable mid-build.

When you finish a TODO, manually trace through each of its test cases and confirm the described outcomes occur. If a test case fails, fix it before marking the TODO complete. If fixing it requires changing something from an earlier TODO, make the minimal change needed and note it.

The Music Explorer TODO deserves the most careful treatment. The chain navigation model, where clicking within a card opens the next card below it and closing a card collapses it and all cards that came after it, is the core interaction pattern of all three explorers. Get it right in the Music Explorer phase and the Movie and TV Explorer phases will be much simpler adaptations of the same pattern. The local caching model is equally important: every piece of data fetched from an external API must be written to the local database, and a Refresh button must force a live re-fetch and update the cache.

The Taste Profile TODO also deserves careful treatment. The builder collects items from all a user's enabled sources, looks up tags for each item, filters out behavioral noise tags like seen live, favorites, love, awesome, and great, weights tags by how frequently tagged items appear in the user's history, normalizes the weights so the top tag equals 100%, and stores the result per user per category. The recommendation engine scores candidates by tag overlap, applies user override bars (five levels from exclude to strong boost), includes grounding tags as permanent pinned additions, and ranks by score. Per-user isolation is not optional: one user's profile, overrides, grounding tags, and dismissed recommendations must never touch another user's data.

When you encounter ambiguity in the overview document, default to the simpler interpretation and note the ambiguity in the current TODO file. When the test cases require something the overview does not fully explain, the test case is the ground truth.

The goal is a working, self-hostable application that a non-technical user could run with Docker and configure through a simple YAML file and a web UI. Keep that user in mind throughout every phase.
