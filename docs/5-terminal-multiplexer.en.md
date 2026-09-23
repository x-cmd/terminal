---
x-title: Terminal Multiplexer Strategies — tmux vs zellij vs Built-In
x-desc: >-
  A practical angle article on terminal multiplexers — tmux (the
  classic), zellij (the modern Rust alternative), and the built-in
  multiplexers in Kitty / WezTerm / Ghostty / iTerm2 / Windows
  Terminal. When to pick external vs built-in, persistence, session
  sharing, scripting.
x-sidebar: Terminal multiplexer
x-keywords: terminal multiplexer, tmux, zellij, wezterm-mux-server, kitty-session, ghostty-session
x-json-ld:
  '@context': https://schema.org
  '@graph':
    - '@type': TechArticle
      headline: 'Terminal multiplexer strategies'
      inLanguage: 'en'
      about: 'terminal multiplexer analysis'
---

# Terminal Multiplexer Strategies — tmux vs zellij vs Built-In

A terminal **multiplexer** lets one window manage many sessions
— panes, tabs, persistent connections that survive
disconnects. Three flavors exist in 2026:

1. **External multiplexers** (tmux, zellij, screen) — run
   *inside* a terminal emulator.
2. **Built-in multiplexers** (Kitty, WezTerm, iTerm2,
   Windows Terminal) — part of the terminal itself.
3. **No multiplexer** (Alacritty, Ghostty 1.0, foot, st,
   GNOME Terminal without extensions) — pair with tmux.

This page is an angle article — strategy comparison, not a
per-tool tutorial. Install / config live at
[x-cmd.com/install/<tool>](https://x-cmd.com/install/).

> **TL;DR.** Most dev workflows are fine with **a built-in
> multiplexer** (Kitty / WezTerm / iTerm2) plus **tmux** when
> you need persistence or session sharing. **zellij** is a
> modern alternative to tmux with better UX defaults.
> **Alacritty + tmux** is the classic Unix combo.

## The three flavors

### External multiplexers

External multiplexers run as their own process **inside** the
terminal emulator.

```
┌──────────────────────────────────┐
│ Alacritty (terminal emulator)   │
│  ┌────────────────────────────┐ │
│  │ tmux (multiplexer)         │ │
│  │  ┌────┐ ┌────┐ ┌────┐      │ │
│  │  │pane│ │pane│ │pane│      │ │
│  │  └────┘ └────┘ └────┘      │ │
│  └────────────────────────────┘ │
└──────────────────────────────────┘
```

**tmux** — the classic (since 2007). Configuration in
`~/.tmux.conf`. Plugin ecosystem (tpm, tmux-resurrect,
tmux-continuum). Persistent sessions across disconnects.
Scripting via `tmux send-keys`. Steep learning curve.

**zellij** — modern Rust alternative (since 2021). Better
UX defaults — discoverable keybindings, status bar by
default, layouts. Configuration in YAML. Plugin system
via WASM modules. Less mature ecosystem than tmux.

**screen** — older, less common today.

### Built-in multiplexers

Modern terminals ship their own multiplexers.

```
┌──────────────────────────────────┐
│ WezTerm (terminal + multiplexer)  │
│  ┌────┐ ┌────┐ ┌────┐             │
│  │pane│ │pane│ │pane│             │
│  └────┘ └────┘ └────┘             │
│  (no separate process)            │
└──────────────────────────────────┘
```

- **Kitty** — `kitty @ launch` + `Ctrl+Shift+Enter` to split.
  Configuration in `kitty.conf`.
- **WezTerm** — `wezterm-mux-server` (a Lua-scriptable
  multiplexer). Configuration in `wezterm.lua`.
- **iTerm2** — built-in panes, tabs, splits. Native macOS
  experience.
- **Windows Terminal** — built-in panes + tabs.
- **Konsole** — built-in splits + tabs.

### No multiplexer

Terminals that intentionally don't ship one:

- **Alacritty** — by design (pairs with tmux).
- **Ghostty 1.0** — no tabs yet (target: 1.2).
- **foot, st, GNOME Terminal** — pair with tmux.

## Comparison matrix

| Tool | Type | Persistence | Cross-machine | Scripting | Ecosystem | Best for |
| --- | --- | --- | --- | --- | --- | --- |
| **tmux** | External | ✅ Yes | ⚠️ With config | `tmux send-keys` etc. | Mature (tpm, plugins) | Persistent sessions, server work |
| **zellij** | External | ✅ Yes | ⚠️ With config | `zellij action` etc. | Growing | Modern tmux alternative |
| **screen** | External | ✅ Yes | ⚠️ With config | `screen -X` | Old | Legacy |
| **Kitty** | Built-in | ❌ (lost on close) | ❌ | `kitty @` | Kitty ecosystem | Lightweight + image protocols |
| **WezTerm** | Built-in | ✅ Yes (via Lua) | ✅ Via `wezterm connect ssh://` | Lua | Lua | Multiplexer + scripting in one |
| **iTerm2** | Built-in | ⚠️ Limited | ❌ | AppleScript | macOS-only | macOS users |
| **Windows Terminal** | Built-in | ⚠️ Limited | ❌ | Limited | Windows-only | Windows users |
| **Konsole** | Built-in | ❌ | ❌ | Limited | KDE | KDE users |
| **Ghostty (1.0)** | None | ❌ | ❌ | ❌ | Native UI | Native UX + tmux for splits |

## When to pick which

### Pick external (tmux / zellij) when:

- You want **persistent sessions** that survive disconnects
  (SSH, laptop sleep).
- You do **server work** — `tmux` is the lingua franca on
  Linux servers.
- You need **session sharing** — pair-programming, demos.
- You want a **mature plugin ecosystem** (tmux-resurrect,
  tmux-continuum, tpm).
- You pair with **Alacritty / foot / st** (which don't have
  built-in multiplexers).

### Pick built-in (Kitty / WezTerm / iTerm2 / Windows Terminal / Konsole) when:

- You mostly work **locally**.
- You want **multiplexer + terminal in one binary** (no extra
  process to manage).
- You want **Lua / AppleScript / native config** instead of
  `.tmux.conf`.
- You want **image protocols + multiplexer together** (Kitty).

### Pick hybrid (built-in + tmux) when:

- You use **Alacritty / foot / Ghostty** (no built-in
  multiplexer).
- You want both **persistent sessions** and **fast local
  panes**.
- The Alacritty / tmux combo is canonical.

### Pick zellij when:

- You're new to multiplexers (zellij has better UX defaults
  and discoverable keybindings).
- You want a **modern scripting model** (WASM plugins).
- You're not yet locked into tmux muscle memory.

## Practical recipes

### tmux on a server

```sh
# SSH into server, start tmux
ssh user@server
tmux new -s work

# Disconnect → reattach later
ssh user@server
tmux attach -t work
```

The session lives on the server. Disconnects don't lose it.

### Alacritty + tmux (the canonical combo)

```sh
alacritty  # terminal
tmux       # multiplexer inside
```

Alacritty is minimal; tmux does everything else.

### WezTerm built-in multiplexer

```lua
-- wezterm.lua
mux_enable_default_keybindings = true

local wezterm = require 'wezterm'
local act = wezterm.action

return {
  keys = {
    { key = '|', mods = 'CTRL|SHIFT', action = act.SplitHorizontal { domain = 'CurrentPaneDomain' } },
    { key = '_', mods = 'CTRL|SHIFT', action = act.SplitVertical { domain = 'CurrentPaneDomain' } },
    { key = 'h', mods = 'CTRL|SHIFT', action = act.ActivatePaneDirection 'Left' },
  },
}
```

Pure WezTerm; no external multiplexer needed.

### zellij (modern)

```sh
zellij --layout default  # start with default layout
```

Status bar shows keybindings. Discoverable without `.tmux.conf`.

### Session sharing (pair programming)

```sh
# Server side: create shared session
tmux new -s pair

# Each participant: attach
ssh user@server -t 'tmux attach -t pair'
```

Multiple users see the same panes in real time.

## Common pitfalls

1. **tmux without `reattach-to-user-namespace`** — breaks
   clipboard on macOS. Fix with `brew install reattach-to-
   user-namespace` and `set -g default-command "reattach-to-
   user-namespace -l zsh"`.
2. **tmux scrollback** — `Ctrl-b [` enters copy mode; `q` to
   exit. Zellij uses `Ctrl-o` for similar.
3. **Session lost on disconnect** — built-in multiplexers
   (Kitty, iTerm2, Windows Terminal) **don't** persist across
   disconnects; tmux / zellij do.
4. **Cross-platform** — tmux is Linux / macOS; zellij is
   Linux / macOS / Windows; Kitty / WezTerm / Ghostty are
   all cross-platform.
5. **Color schemes** — tmux with 256-color terminal: `set
   -g default-terminal "screen-256color"`. WezTerm / Kitty:
   handled automatically.

## What's NOT a multiplexer

- **OS window manager splits** — i3 / Sway / Aerospace /
  Rectangle — these manage windows, not terminals.
- **Editor splits** — Vim / Emacs / VS Code — splits in
  buffers / panes, not terminal sessions.
- **Browser shells** — VS Code's terminal — embedded in
  the editor, not a standalone multiplexer.

## What's next?

- Topic-library articles in `0-latest-terminals` /
  `1-terminal-overview` for cross-references.
- Per-tool install at `x-cmd.com/install/<tool>`.

## Related

- [tmux home](https://github.com/tmux/tmux)
- [zellij home](https://github.com/zellij-org/zellij)
- [WezTerm multiplexer docs](https://wezfurlong.org/wezterm/config/lua/mux.html)
- [Kitty multiplexer](https://sw.kovidgoyal.net/kitty/overview.html#sessions)
- [Alacritty + tmux combo](https://alacritty.org/)

> For per-tool install / config: `x-cmd.com/install/<tool>` or
> `x-cmd.com/doc-2026/install/<tool>`. This page is angle /
> analysis only.