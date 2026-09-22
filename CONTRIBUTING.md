# Contributing — `x-cmd/terminal`

This page covers how to add or modify an article in the
terminal-emulator topic library.

**Looking to read?** See [`README.md`](./README.md) for the
overview, or the [`SKILL.md`](./SKILL.md) for the AI-agent
recipe.

## Article slots

| Slot | Article | Add a new one? |
| --- | --- | --- |
| `0-latest-terminals` | Latest news. | Refresh in place; do not add a second `0-` slot. |
| `1-terminal-overview` | Overview + comparison. | Refresh in place. |
| `2-…`, `3-…`, … | One per notable terminal. | **Yes** — add a new slot. Filename `n-<terminal>.{en,cn,llms,faq}.md`. |

When you add a new per-terminal deep dive, link it from the
overview's comparison table and from the latest-news article
(if it's relevant to recent activity).

## Per-slot file convention

Every article slot is **four files, kept in sync**:

| File | Purpose | Required? |
| --- | --- | --- |
| `n-<slug>.en.md` | Canonical English article. The one the GitHub social preview and search engines see. | ✅ |
| `n-<slug>.cn.md` | Chinese translation. Same structure, same anchors, same diagrams. | ✅ |
| `n-<slug>.llms.md` | LLM-friendly summary — YAML frontmatter + flat prose. One screen of structured text. | ✅ |
| `n-<slug>.faq.yml` | Structured Q&A used for the FAQ section and JSON-LD on the site. | ✅ |

If you change `.en.md`, change `.cn.md` in the same commit. If
you add a new FAQ entry, add it to both languages.

## English frontmatter

```yaml
---
x-title: Alacritty — A Fast, Cross-Platform OpenGL Terminal Emulator
x-desc: >-
  Alacritty is a Rust-built, OpenGL-accelerated terminal emulator for
  Linux / macOS / BSD / Windows — minimal, fast, pairs with tmux.
x-sidebar: Alacritty
x-keywords: alacritty, terminal, opengl, rust, gpu, tmux
x-json-ld:
  '@context': https://schema.org
  '@graph':
    - '@type': TechArticle
      headline: 'Alacritty'
      inLanguage: 'en'
      about: 'Alacritty terminal emulator'
---
```

`x-title` and `x-desc` are required; `x-sidebar`, `x-keywords`,
and `x-json-ld` are optional but recommended for SEO.

## `.llms.md` format

```markdown
---
name: alacritty
description: Fast, GPU-accelerated terminal emulator written in Rust
  with minimal dependencies. Pairs with tmux.
type: summary
---

# Core Content

core_features:
  - GPU-accelerated rendering via OpenGL ES 2.0
  - Minimal footprint; pairs with tmux for multiplexing
  - TOML configuration with live reload

# Key Information

highlights:
  - Smallest, fastest GPU terminal in common use
  - No built-in tabs or splits by design

# Use Cases

use_cases:
  - Heavy-output dev (cat huge.log, cargo build, tail -f)
  - Pairing with tmux for session management

# Related Resources

official:
  website: https://alacritty.org/
  repo: https://github.com/alacritty/alacritty
related:
  - name: Kitty
    url: https://sw.kovidgoyal.net/kitty/
  - name: WezTerm
    url: https://wezfurlong.org/wezterm/

# Summary

Short paragraph that an LLM can quote verbatim.
```

## `.faq.yml` format

```yaml
id: x-t-2-alacritty

data:
  - name:
      en: overview
      cn: 概览
    qa:
      - id: what-is-alacritty
        question:
          en: What is Alacritty?
          cn: 什么是 Alacritty？
        answer:
          en: A Rust-built, OpenGL ES 2.0-accelerated terminal emulator for Linux, macOS, BSD, and Windows.
          cn: 用 Rust 编写的、OpenGL ES 2.0 加速的终端模拟器，支持 Linux、macOS、BSD 和 Windows。
        confidence: 9
        reference:
          - docs/2-alacritty.en.md
          - docs/2-alacritty.cn.md

  - name:
      en: install
      cn: 安装
    qa:
      - id: how-do-i-install
        question:
          en: How do I install Alacritty?
          cn: 如何安装 Alacritty？
        answer:
          en: x env use alacritty, or brew install --cask alacritty, or apt install alacritty.
          cn: x env use alacritty，或 brew install --cask alacritty，或 apt install alacritty。
        confidence: 9
        reference:
          - docs/2-alacritty.en.md
```

Each FAQ entry has:

- `id` — kebab-case, unique within the file
- `question` — bilingual `en` + `cn`
- `answer` — bilingual `en` + `cn`, ≤ 4 sentences each
- `confidence` (1–9) — how confident the team is in the answer
- `reference` — list of article files that back the answer

## Editing rules

- **One slot per commit.** Don't mix the overview and a deep
  dive in the same commit.
- **Both languages in the same commit.** Don't open separate
  PRs for the `.en.md` and `.cn.md` of the same slot.
- **Quote from official sources only.** Don't paraphrase from
  Wikipedia / Reddit / random blogs without verifying against
  the upstream repo, the official website, or release notes.
- **Don't quote from `x-cmd-install/mneme`.** That repo is
  private and exists to keep things out of public view.
- **Don't discuss intent.** Articles are content-only — no
  "we're planning to add", no monetization framing, no
  internal-org context.

## CI

The site's build pipeline reads `docs/` and validates that:

1. Every `.en.md` has a matching `.cn.md` with the same
   filename stem.
2. Every slot has a `.llms.md` and a `.faq.yml`.
3. The `.faq.yml` is valid YAML and every `id` is unique.
4. The `.llms.md` frontmatter parses.

If CI fails, the article does not publish to
`x-cmd.com/terminal` until the failure is fixed.

## What this repo is NOT

- **Not** a fork of any terminal emulator's source code.
- **Not** an install database — that's [`x-cmd/install`](https://github.com/x-cmd/install).
- **Not** an opinion piece on "which terminal is best" — the
  articles are factual and let the reader compare.
- **Not** a substitute for the upstream docs — link to them
  for canonical install / config recipes.