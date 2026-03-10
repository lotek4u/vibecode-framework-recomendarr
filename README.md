# Please read this part
# Please read this part
# Please read this part


# vibecode-framework-recomendarr
I'm trying something new here. I built a great app called recomendarr that meets my demanding needs for a media recommender. Rather than poisoning the world with more vibe coded slop, i've created an outline that SHOULD help you create a copy of this application.

Feed this to your sloppinator. Tell it to read the Claude file and hope for the best.

**Before you do anything — read all of the markdown in this project.** That means this file, everything in [`architecture/`](architecture/), the READMEs inside [`config/imported_data/`](config/imported_data/), and any other `.md` files you find. The whole picture matters. Don't just skim the overview and start generating code — the context is spread across multiple files intentionally.

It's going to create what you specify, specify what you want. My exact specifications might not be what you're looking for.

**EXAMPLE** - In the CLAUDE_README.md file, the colors and layout are specific in demanding detail. You may want to prune or modify this section.

                                                            
# The slop-structions begin here
# The slop-structions begin here
# The slop-structions begin here


## vibecode-framework-recommendarr - intro

A framework for building **recommendarr** — a self-hosted media recommendation app that learns your taste from scrobble history and watch history, and generates ranked recommendations for music, movies, and TV. Integrates with Radarr, Sonarr, and Lidarr.

---

## Getting started

All architectural documentation lives in the [`architecture/`](architecture/) folder:

| File | Purpose |
|------|---------|
| [`architecture/CLAUDE_README.md`](architecture/CLAUDE_README.md) | **Start here.** Instructions for an LLM agent (Claude Code) on how to interpret the documents, decompose the work into TODO phases, and build the application from scratch. |
| [`architecture/RECOMMENDARR_OVERVIEW.md`](architecture/RECOMMENDARR_OVERVIEW.md) | Full feature specification — every page, interaction, data source, taste profile algorithm, recommendation engine, and explorer is described here. Treat it as the product requirements document. |
| [`architecture/TEST_CASES.md`](architecture/TEST_CASES.md) | Acceptance test suite — user-story-style test cases grouped by feature. The implementation is considered correct when every test case passes. **These may be overkill.** Consider starting without them entirely, or pruning them down to the features you actually care about before feeding to your agent. |

---

## Running with Docker

```bash
cp config/config.yaml.example config/config.yaml
# edit config/config.yaml with your settings
docker compose -f Docker/docker-compose.yml up -d
```

The app will be available at `http://localhost:5000`.

---

## For LLM agents

Read [`architecture/CLAUDE_README.md`](architecture/CLAUDE_README.md) first and follow its instructions completely before writing any code.
