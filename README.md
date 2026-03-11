# Please read this part
#
#
# Note from LoTek
I'm trying something new here. I built a great app called recomendarr that meets my demanding needs for a media recommender. Rather than poisoning the world with more vibe coded slop, i've created an outline that SHOULD help you create a copy of this application.

**Before you do anything — read all of the markdown in this project.** That means this file, everything in [`architecture/`](architecture/), the READMEs inside [`config/imported_data/`](config/imported_data/), and any other `.md` files you find. The whole picture matters. Don't just skim the overview and start generating code — the context is spread across multiple files intentionally.

It's going to create what you specify, specify what you want. My exact specifications might not be what you're looking for.

**EXAMPLE 1** - In the CLAUDE_README.md file, the colors and layout are specific in demanding detail. You may want to prune or modify this section.
**EXAMPLE 2** - The test cases, onsider starting without them entirely, or pruning them down to the features you actually care about before feeding to your agent.

## How to Get Claude Code Running

Claude Code is Anthropic's official agentic coding CLI. It runs in your terminal and gives Claude direct access to your filesystem, shell, and editor so it can read code, write files, run commands, and work through complex tasks autonomously.

The official documentation is at: https://docs.anthropic.com/en/docs/claude-code/overview

### Environment recomendations
- If you have a native Linux of MacOS device, use that
- If you are stuck on Windows (like me), use WSL2
- If you can't use WSL 2 on windows, or for any other weird scenario, run Claude Code in a container.

---

### Claude Code Notes
- There is no GUI, if you're looking for that, you have the wrong app.
- It's native to Linux, it's easiest to use in Linux, but once it's running, OS doesn't matter.

---


#
#
#
# The slop-structions begin here
#
## Feed this to your sloppinator. Tell it to read the Claude file and hope for the best.
#
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

## For LLM agents

Read [`architecture/CLAUDE_README.md`](architecture/CLAUDE_README.md) first and follow its instructions completely before writing any code.
