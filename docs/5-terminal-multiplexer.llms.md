---
name: 5-terminal-multiplexer
description: Strategy angle on terminal multiplexers — external (tmux, zellij, screen) vs built-in (Kitty, WezTerm, iTerm2, Windows Terminal, Konsole) vs none (Alacritty, Ghostty, foot, st). Persistence, session sharing, scripting, comparison matrix.
type: summary
---

# Core Content

core_features:
  - Three multiplexer flavors: external, built-in, none
  - tmux — classic, mature, persistence, server work
  - zellij — modern Rust alternative with better UX defaults
  - Built-in multiplexers in Kitty / WezTerm / iTerm2 / Windows Terminal / Konsole
  - Persistence (survives disconnects) vs ephemeral (lost on close)
  - Cross-machine session sharing (SSH tmux attach)
  - WezTerm Lua scripting for multiplexer; kitty @ CLI
  - Practical recipes: server tmux, Alacritty + tmux combo, WezTerm built-in, zellij, pair programming

# Key Information

highlights:
  - Alacritty pairs with tmux (canonical Unix combo)
  - WezTerm is the only major terminal with built-in multiplexer + Lua scripting
  - tmux is the lingua franca on Linux servers
  - zellij has better UX defaults; growing but smaller ecosystem than tmux
  - Built-in multiplexers don't persist across disconnects
  - WezTerm's wezterm connect ssh:// for cross-machine persistence
  - tmux reattach-to-user-namespace needed on macOS for clipboard

# Use Cases

use_cases:
  - Choosing tmux vs zellij for persistent sessions
  - Picking a built-in multiplexer for local-only work
  - Pair-programming with shared tmux sessions
  - Setting up Alacritty / foot / Ghostty + tmux
  - Recovering sessions after disconnect

# Related Resources

related:
  - name: tmux
    url: https://github.com/tmux/tmux
  - name: zellij
    url: https://github.com/zellij-org/zellij
  - name: WezTerm multiplexer docs
    url: https://wezfurlong.org/wezterm/config/lua/mux.html
  - name: Kitty sessions
    url: https://sw.kovidgoyal.net/kitty/overview.html#sessions

# Summary

Three terminal multiplexer flavors in 2026: external (tmux, zellij, screen — runs inside the terminal emulator), built-in (Kitty, WezTerm, iTerm2, Windows Terminal, Konsole — part of the terminal itself), none (Alacritty, Ghostty 1.0, foot, st — pair with tmux). External multiplexers (tmux + zellij) give persistence across disconnects, server-friendly scripting (tmux send-keys), and a mature plugin ecosystem. Built-in multiplexers (WezTerm especially) give multiplexer + terminal in one binary, with Lua scripting for the multiplexer itself. Pick external for server work, persistent sessions, and pairing. Pick built-in for local-only workflows and image-protocol + multiplexer combos (Kitty). Pick hybrid (built-in + tmux) for Alacritty / foot / Ghostty users who want both. zellij is a modern tmux alternative with better UX defaults and WASM plugins; less mature ecosystem. Practical recipes: server tmux (`tmux new -s work`), Alacritty + tmux combo, WezTerm Lua multiplexer config, zellij default layout, pair programming via `tmux attach -t pair`.