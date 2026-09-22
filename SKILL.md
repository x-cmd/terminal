---
name: terminal
description: Terminal-emulator topic library — overview, comparison, and per-terminal deep dives (Alacritty, Kitty, WezTerm, Ghostty, …). Use when the user asks about "best terminal", "GPU terminal", "Alacritty vs Kitty", "terminal emulator comparison", or wants to pick / configure / install one.
metadata: type=topic-library, source=team-curated, schema=4-tuple-md, refresh=manual, license=apache-2.0, scope=terminal-emulators
---

# x-cmd/terminal — using the terminal topic library

## 1. Read on the website

The articles are published at <https://x-cmd.com/terminal>.
Latest news is at `/terminal/latest`, overview at
`/terminal/overview`, and each per-terminal deep dive at
`/terminal/<slug>` (e.g. `/terminal/alacritty`).

## 2. Use the raw files

Plain markdown + YAML, served over HTTPS from GitHub. Fetch
directly from `https://raw.githubusercontent.com/x-cmd/terminal/main/...`
— do not route through any CDN or proxy.

```sh
# All four files for one slot
for ext in en.md cn.md llms.md faq.yml; do
  curl -fsSL "https://raw.githubusercontent.com/x-cmd/terminal/main/docs/0-latest-terminals.$ext"
done
```

## Article schema (per slot)

Each slot is four files, kept in sync:

| File | Shape | Purpose |
| --- | --- | --- |
| `n-<slug>.en.md` | Markdown with YAML frontmatter (`x-title`, `x-desc`, optional `x-sidebar`, `x-keywords`, `x-json-ld`). | Canonical English article. |
| `n-<slug>.cn.md` | Same as above, in Chinese. | Chinese translation. |
| `n-<slug>.llms.md` | YAML frontmatter (`name`, `description`, `type`) + flat structured sections (`core_features`, `highlights`, `use_cases`, `related_resources`, `summary`). | LLM-friendly summary. |
| `n-<slug>.faq.yml` | Bilingual `question` + `answer`, with `confidence` (1–9) and `reference` per entry. | FAQ section + JSON-LD. |

## Slot conventions

| Slot | Article | Style |
| --- | --- | --- |
| `0-` | Latest news | Newsletter — recent releases, trends, breaking changes. Updated in place. |
| `1-` | Overview + comparison | One comparison table across the main alternatives; renderer / multiplexer / config / platform / best-for. |
| `2-…` | Per-terminal deep dive | What it is, install, config, when to use / not use, source-code tour. |

## Common agent queries

```sh
# "Which terminal is fastest?" — read 1-terminal-overview, look at the renderer column.
# "How do I install Alacritty?" — read 2-alacritty.en.md, follow the install section.
# "Does Kitty support Sixel?" — read 1-terminal-overview's comparison table.
# "What's new in 2026?" — read 0-latest-terminals.en.md.
```

When a question requires a comparison, **always cross-check**
the comparison table in `1-terminal-overview.{en,cn}.md` — do
not invent distinctions from memory. The table is the source
of truth for the side-by-side.

## Sources

- <https://github.com/x-cmd/terminal> — this repo (raw markdown + YAML).
- <https://x-cmd.com/terminal> — published articles.
- [`x-cmd/cve/SKILL.md`](https://github.com/x-cmd/cve/blob/main/SKILL.md) — parallel topic-library pattern.
- [`x-cmd/gpg/SKILL.md`](https://github.com/x-cmd/gpg/blob/main/SKILL.md) — parallel topic-library pattern.
- [`x-cmd/install/AGENTS.md`](https://github.com/x-cmd/install/blob/main/AGENTS.md) — install-database agent rules.