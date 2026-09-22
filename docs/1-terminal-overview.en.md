---
x-title: Terminal Emulator Overview — Comparison Across GPU, Multiplexer, Config, Platform
x-desc: >-
  A side-by-side comparison of the most-used terminal emulators —
  Alacritty, Kitty, WezTerm, Ghostty, Rio, Tabby, iTerm2, Windows
  Terminal, GNOME Terminal, foot, Konsole — across renderer,
  multiplexer, config format, platform support, and best-for.
x-sidebar: Terminal overview
x-keywords: terminal emulator, comparison, alacritty, kitty, wezterm, ghostty, iterm2, windows terminal
x-json-ld:
  '@context': https://schema.org
  '@graph':
    - '@type': TechArticle
      headline: 'Terminal emulator overview'
      inLanguage: 'en'
      about: 'terminal emulator comparison'
---

# Terminal Emulator Overview

A horizontal comparison of the terminal emulators people
actually pick in 2026. Use this page when you know you want
*a terminal* but don't yet know which one.

## The comparison

| Terminal | Renderer | Multiplexer | Config | Platforms | Best for |
| --- | --- | --- | --- | --- | --- |
| **Alacritty** | OpenGL ES 2.0 | External (tmux / zellij) | TOML | Linux, macOS, BSD, Windows | Smallest, fastest, "just terminal"; pair with tmux |
| **Kitty** | OpenGL 3.3 | Built-in (kitty-session) + tmux | Plain text | Linux, macOS, BSD | Image protocols, kittens, GPU-extensible |
| **WezTerm** | wgpu (Vulkan / Metal / DX12 / OpenGL) | Built-in (Lua) | Lua | Linux, macOS, Windows, BSD | Multiplexer + scripting in one |
| **Ghostty** | Metal / OpenGL | External (tmux / ghostty-session) | TOML / MoonBit | Linux, macOS, Windows | Native UI per-platform, fast startup |
| **Rio** | wgpu | External (tmux) | TOML | Linux, macOS, Windows | Newer Rust alternative to Alacritty |
| **Tabby** | CPU (Electron) | Built-in (Electron tabs / panes) | YAML | Linux, macOS, Windows | Visual config, SSH manager baked in |
| **iTerm2** | CPU + Metal | Built-in (panes / split / tabs) | Prefs UI / JSON | macOS only | macOS users who want a feature-rich terminal |
| **Windows Terminal** | DirectX | Built-in (panes) | JSON | Windows only | Windows users, Microsoft-supported |
| **GNOME Terminal** | CPU (VTE) | External (tmux) | GSettings / dconf | Linux | Default Linux choice; GNOME users |
| **Konsole** | CPU (VTE) | Built-in (splits / tabs) | KConfig / ini | Linux, BSD | KDE users |
| **foot** | CPU (wlroots) | External (tmux) | ini | Linux (Wayland) | Minimal Wayland terminal |
| **st** | CPU (Xlib) | External (tmux) | C source patches | Linux, BSD | Suckless minimalism; patch your own |

## Reading the table

### Renderer

- **GPU** (Metal / OpenGL / wgpu) — text is rendered into a
  glyph atlas on the GPU. Smooth scrolling on heavy output
  (`cat huge.log`, `cargo build`, `tail -f`). The default
  choice in 2026.
- **CPU** (VTE / Xlib / Electron) — text is rendered by the
  CPU. Adequate for most daily work; chokes on heavy output.
  GNOME Terminal, Konsole, st, foot, iTerm2 (older), Tabby.

### Multiplexer

- **External** — terminal pairs with tmux, zellij, or your
  tiling window manager. Less duplicated code; users pick
  their favorite.
- **Built-in** — terminal ships its own session manager.
  Single-app workflow; less composable.

### Config

- **TOML** — minimal, comments, sections. Used by Alacritty,
  Rio, Ghostty.
- **Plain text** — Kitty's own format.
- **Lua** — programmable, used by WezTerm.
- **JSON** — verbose; used by Tabby, Windows Terminal.
- **Prefs UI** — point-and-click; iTerm2.

### Platforms

Linux, macOS, BSD, Windows. Some are platform-specific
(iTerm2 = macOS only; Windows Terminal = Windows only).

## Picking a terminal

A simple decision tree:

1. **Need the smallest, fastest?** → Alacritty. Pair with tmux.
2. **Need built-in multiplexer + scripting?** → WezTerm.
3. **Need image protocols / kittens?** → Kitty.
4. **Need native macOS UI on macOS, native Win32 on Windows?** → Ghostty.
5. **Need a GUI config editor?** → iTerm2 (macOS), Windows
   Terminal (Windows), Tabby (any).
6. **GNOME / KDE user, want the default?** → GNOME Terminal /
   Konsole.
7. **Want it minimal, on Wayland?** → foot.
8. **Want it minimal, want to patch your own?** → st.

## Per-platform notes

### macOS

The strongest lineup. iTerm2 is the long-time default; Alacritty
and Kitty have substantial macOS userbases; Ghostty 1.0 ships
a native AppKit UI. **Tabby** and **Rio** also work on macOS.

### Linux

Alacritty, Kitty, WezTerm, Ghostty, foot, and st are all
Linux-native. GNOME Terminal and Konsole ship by default with
their respective desktop environments. Most terminals support
both X11 and Wayland; st is X11-only.

### Windows

The choice has historically been sparse. The 2026 landscape:

- **Windows Terminal** — Microsoft's official terminal;
  DirectX renderer; built-in tabs and panes.
- **Alacritty** — full Windows support via ConPTY since 0.9.
- **WezTerm** — full Windows support; popular for cross-platform
  configs.
- **Ghostty** — native Win32 support as of 1.0.
- **Tabby** — Electron-based; works well on Windows.

Kitty on Windows requires WSL or Cygwin; not first-class.

### BSD

Alacritty, Kitty, WezTerm, Ghostty, st, Konsole all work on
*BSD variants. foot is Linux-only.

## What's *not* a terminal emulator

This page is about terminal emulators. For completeness:

- **tmux / zellij / screen** — terminal *multiplexers*, not
  emulators. They run *inside* a terminal emulator.
- **xterm / mintty / ConEmu** — terminal emulators, but
  largely superseded in 2026; not covered in depth here.
- **Browser shells** (e.g. VS Code's integrated terminal) —
  built into an editor; not a standalone terminal emulator.

## Related

- [`docs/0-latest-terminals.en.md`](./0-latest-terminals.en.md) — recent releases and trends.
- [`docs/2-alacritty.en.md`](./2-alacritty.en.md) — Alacritty deep dive.
- [`docs/3-kitty.en.md`](./3-kitty.en.md) — Kitty deep dive (planned).
- [`docs/4-wezterm.en.md`](./4-wezterm.en.md) — WezTerm deep dive (planned).
- [`docs/5-ghostty.en.md`](./5-ghostty.en.md) — Ghostty deep dive (planned).