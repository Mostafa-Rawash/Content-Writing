# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

Not a code project — there is no build, lint, or test tooling. It is a personal content/career workspace for Mostafa Rawash: a verified career-facts source, a Reel production library, and notes for a 90-day personal operating system ("Mostafa OS", 2026-11-13 → 2027-02-12) that lives in ClickUp.

## Layout and how the pieces relate

- `CAREER_FACTS.md` — the **single source of truth** for all career claims (timeline, eDariba/Qayedny details, projects, skills, CV bullet bank, GitHub audit). Facts carry status tags: `[CONFIRMED]`, `[SELF-REPORTED]`, `[REPO-VERIFIED]`, `[THIRD-PARTY]`, `[VERIFY]`, `[MISSING]`, `[CONFLICT]`. §16 lists open items needing verification.
- `reels/` — 28 record-ready Reel scripts, grouped `W01…W13-<topic>/reel-NN-<slug>/`, each with a `CONTENT.md` and an (initially empty) `materials/` folder for screenshots/B-roll/audio. `reels/README.md` holds the publishing schedule (2026-11-20 → 2027-02-14). Each `CONTENT.md` has: title, publish date, duration, ClickUp task link, platforms, Hook, Spoken script, Closing, Caption, Material checklist. Scripts are in Egyptian Arabic with English product terms (Product, roadmap, Stakeholder, outcomes) kept in English.
- `MOSTAFA_OS.md` — the operating manual: mirrors the ClickUp Space structure (Space ID and List IDs), daily/weekly rhythm, 90-day outcomes, monthly phases, weekly review checklist, system rules.
- `MOSTAFA_OS_MISSING.md` — running checklist of undecided personal choices (wake time, target roles, primary course, study slots, budget). Update it when decisions are made or items are completed.

## Rules to follow when editing

- Use only claims present in `CAREER_FACTS.md` and respect their status tags; do not add unsupported metrics or confidential details to scripts, CVs, or posts. Do not treat `[VERIFY]`/`[MISSING]`/`[CONFLICT]` items as confirmed. Known unresolved example: the "028/2026" open-to-work date is invalid.
- Content production is canonical in the **Content-Writing** ClickUp Space and the `reels/` folder; do not create duplicate content-production tasks in the Mostafa OS Space. Each Reel's `CONTENT.md` links to its ClickUp task — keep the publish date consistent between `CONTENT.md`, `reels/README.md`, and ClickUp.
- When adding a Reel, follow the existing `CONTENT.md` structure and folder naming, and add it to the schedule table in `reels/README.md`.
- ClickUp is accessed through the `claude_ai_ClickUp` MCP tools. Before converting relative dates ("this week", "tomorrow") call `clickup_get_user` and use its `current_time`.
