---
name: 0-latest-terminals
description: What's new in terminal emulators in 2026 — Ghostty 1.0, WezTerm 2026, Alacritty 0.13, Kitty 0.43, and the trend toward GPU rendering, stable image protocols, and TOML config.
type: summary
---

# Core Content

core_features:
  - Quarterly snapshot of terminal-emulator releases and trends
  - Recent: Ghostty 1.0 (Aug 2026), WezTerm 2026.06, Kitty 0.43, Alacritty 0.13.2, Rio 0.2, Tabby 1.0
  - GPU rendering is the new default across all major terminals
  - Three image protocols coexist: iTerm2, Kitty graphics, Sixel
  - Config formats moving from JSON to TOML (Alacritty, Rio) or Lua (WezTerm)

# Key Information

highlights:
  - Ghostty 1.0 is the first stable release after ~2 years; ships native UI per-platform
  - Alacritty 0.13 broke YAML configs in favor of TOML
  - WezTerm 2026.x changed default Lua config layout; needs module-import updates
  - "Pair with tmux" is the dominant design philosophy
  - Kitty 0.42+ requires OpenGL 3.3 (was 3.0)

# Use Cases

use_cases:
  - Catching up on what's changed if you last looked at terminals ~12 months ago
  - Picking a terminal for a new machine based on current state of the art
  - Knowing which breaking changes affect an existing config

# Related Resources

official:
  repo: https://github.com/x-cmd/terminal
related:
  - name: 1-terminal-overview
    url: https://github.com/x-cmd/terminal/blob/main/docs/1-terminal-overview.en.md
  - name: 2-alacritty
    url: https://github.com/x-cmd/terminal/blob/main/docs/2-alacritty.en.md

# Summary

2026 is the year GPU rendering becomes the default for every notable terminal emulator, image protocols stabilize across iTerm2 / Kitty / Sixel, and configuration moves away from JSON toward TOML and Lua. Ghostty ships its 1.0; Alacritty's TOML migration lands; WezTerm's 2026.x refreshes its Lua config layout. Read 1-terminal-overview for the side-by-side comparison, and per-terminal deep dives for install / config recipes.