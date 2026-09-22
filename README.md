# x-cmd/terminal — terminal emulator topic library

Articles, comparisons, and deep dives on terminal emulators.
Published as a topic library at <https://x-cmd.com/terminal>.

> 🌐 **中文版：[README.cn.md](./README.cn.md)** — same content,
> Chinese front matter.

This repo holds the canonical English / Chinese articles that
back the **Terminal** section of the x-cmd website. The
articles are content-only, factual, and open for **modification
PRs** from anyone — see [`CONTRIBUTING.md`](./CONTRIBUTING.md)
for the article workflow and frontmatter spec.

## What's in this repo

```
x-cmd/terminal/
├── README.md                 # this file (English)
├── README.cn.md              # Chinese version
├── CONTRIBUTING.md           # article workflow + frontmatter spec + FAQ schema
├── SKILL.md                  # AI-agent recipe for using the topic library
├── LICENSE                   # Apache-2.0
└── docs/
    ├── 0-latest-terminals.{en,cn}.md        # what's new in terminal emulators
    ├── 0-latest-terminals.llms.md
    ├── 0-latest-terminals.faq.yml
    ├── 1-terminal-overview.{en,cn}.md       # overview + horizontal comparison
    ├── 1-terminal-overview.llms.md
    ├── 1-terminal-overview.faq.yml
    ├── 2-alacritty.{en,cn}.md               # Alacritty deep dive
    ├── 2-alacritty.llms.md
    └── 2-alacritty.faq.yml
```

The leading integer in the filename is the reading order.
Articles are kept in sync across all four files per slot:
`.en.md`, `.cn.md`, `.llms.md`, `.faq.yml`.

## Article slots

| Slot | Article | Purpose |
| --- | --- | --- |
| `0-` | Latest terminals | What's new in terminal emulators — recent releases, trends, breaking changes. Newsletter-style. |
| `1-` | Terminal overview | Big-picture map of the terminal-emulator space; one comparison table across the main alternatives. |
| `2-…` | Per-terminal deep dives | One article per notable terminal — what it is, install, config, when to use / not use. |

## Sister repos

- [`x-cmd/cve`](https://github.com/x-cmd/cve) — CVE / CWE intelligence (topic library pattern reference).
- [`x-cmd/gpg`](https://github.com/x-cmd/gpg) — team GPG keyring (topic library pattern reference).
- [`x-cmd/browser`](https://github.com/x-cmd/browser) — web browser topic library (parallel structure).
- [`x-cmd/install`](https://github.com/x-cmd/install) — install database (drives `x install <name>`).
- [`x-cmd/x-cmd`](https://github.com/x-cmd/x-cmd) — module source (`mod/`).

## License

Apache License 2.0 — see [`LICENSE`](./LICENSE).