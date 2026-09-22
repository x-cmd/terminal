---
x-title: Latest Terminal Emulators — What's New in 2026
x-desc: >-
  Recent releases, trends, and breaking changes in terminal emulators —
  Ghostty 1.0, WezTerm 2026, Alacritty 0.13, Kitty 0.43, and the rise
  of GPU-accelerated rendering.
x-sidebar: Latest terminals
x-keywords: terminal emulator, ghostty, kitty, wezterm, alacritty, rio, 2026
x-json-ld:
  '@context': https://schema.org
  '@graph':
    - '@type': TechArticle
      headline: 'Latest terminal emulators — 2026'
      inLanguage: 'en'
      about: 'terminal emulator news and releases'
---

# Latest Terminal Emulators — What's New in 2026

A snapshot of what shipped, what changed, and where the
terminal-emulator space is heading in 2026. Updated quarterly.

## Recent releases

| Date | Terminal | Version | Headline change |
| --- | --- | --- | --- |
| 2026-08 | **Ghostty** | 1.0 | First stable release after ~2 years of pre-release. Native GTK/libadwaita UI on Linux; native AppKit on macOS; native Win32 on Windows. |
| 2026-06 | **WezTerm** | 2026.06.00 | Wayland 1.24 protocol support; iTerm2 image protocol import; improved Lua config ergonomics. |
| 2026-05 | **Kitty** | 0.43 | New graphics cursor (GPU-rendered); iTerm2 / KiTTY / sixel image protocols stabilized. |
| 2026-03 | **Alacritty** | 0.13.2 | Bug-fix line on the 0.13 series; 0.14 in active development. |
| 2026-02 | **Rio** | 0.2 | wgpu-based renderer stabilizes; multi-window support. |
| 2025-12 | **Tabby** | 1.0 | Electron-based; long-time beta; first stable. |

## Trends

### GPU rendering is the default

Every notable new terminal now ships with a GPU-accelerated
renderer: Ghostty uses Metal on macOS, WezTerm uses wgpu (Vulkan
/ Metal / DX12 / OpenGL), Kitty uses OpenGL, Alacritty uses
OpenGL ES 2.0, Rio uses wgpu. CPU rendering is now a legacy
choice — GNOME Terminal and xterm are the last major holdouts.

### Image protocols are stabilizing

Three image protocols now coexist: **iTerm2** (escape-sequence
based), **Kitty graphics protocol** (escape-sequence based,
Kitty-native), and **Sixel** (DEC VT340-style, the oldest). Most
terminals now implement at least one; many implement two or
three. The site has a separate deep dive on the trade-offs.

### Configuration is moving away from JSON

TOML is the new default: Alacritty (since 0.12), Rio. WezTerm
chose Lua for programmability. JSON (Tabby, Windows Terminal)
is acceptable but verbose. The field is moving toward formats
that allow comments and inline documentation.

### "Terminal multiplexer is someone else's job"

Alacritty's design philosophy — *do one thing, pair with tmux
or zellij* — has gone mainstream. Ghostty, WezTerm, and Kitty
all ship some built-in multiplexer features, but each one
recommends tmux / their session manager as the heavy lifter.

## Breaking changes worth knowing

- **Alacritty 0.13** moved config from YAML to TOML. Existing
  `alacritty.yml` configs need to be renamed and re-formatted.
- **WezTerm 2026.x** changed the default Lua config layout;
  old `.wezterm.lua` files need module-import updates.
- **Ghostty 1.0** removed the GTK3 fallback; GTK4 / libadwaita
  is now required on Linux.
- **Kitty 0.42+** requires OpenGL 3.3 (was 3.0). Some older
  virtualized GPUs need driver updates.

## What to watch

- **Alacritty 0.14** — a major release candidate in late 2026
  with a refactored renderer and improved scrollback.
- **Ghostty multi-window / tabs** — the most-requested feature
  in Ghostty's issue tracker. Not in 1.0; targeting 1.2.
- **Sixel everywhere** — proposals for a unified image protocol
  that subsumes iTerm2 + Kitty + Sixel. Early stage; nothing
  shipped yet.

## Related

- [`docs/1-terminal-overview.en.md`](./1-terminal-overview.en.md) — overview and comparison table.
- [`docs/2-alacritty.en.md`](./2-alacritty.en.md) — Alacritty deep dive.