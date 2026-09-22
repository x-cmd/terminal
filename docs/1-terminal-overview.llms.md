---
name: 1-terminal-overview
description: Horizontal comparison of terminal emulators across renderer, multiplexer, config format, platforms, and best-for. Covers Alacritty, Kitty, WezTerm, Ghostty, Rio, Tabby, iTerm2, Windows Terminal, GNOME Terminal, Konsole, foot, st.
type: summary
---

# Core Content

core_features:
  - Side-by-side comparison of 12 widely-used terminal emulators
  - Dimensions: renderer (GPU vs CPU), multiplexer (built-in vs external), config format, platforms, best-for
  - Decision tree for picking the right terminal
  - Per-platform notes (macOS, Linux, Windows, BSD)
  - Disambiguation: what is and isn't a terminal emulator

# Key Information

highlights:
  - GPU rendering is the default in 2026: Alacritty (OpenGL ES 2.0), Kitty (OpenGL 3.3), WezTerm (wgpu), Ghostty (Metal/OpenGL), Rio (wgpu)
  - TOML is the new config-format default; Lua (WezTerm) is programmable; JSON is legacy
  - "Pair with tmux" is the dominant design philosophy (Alacritty, Ghostty, Rio, foot, st)
  - iTerm2 is the macOS-only feature-rich choice; Windows Terminal is its Windows counterpart
  - Kitty requires OpenGL 3.3+; some virtualized GPUs may need updates

# Use Cases

use_cases:
  - Picking a terminal for a new machine
  - Comparing two terminals side-by-side
  - Understanding the renderer / multiplexer / config trade-offs
  - Knowing what's Linux-native vs Wayland-only vs cross-platform

# Related Resources

official:
  repo: https://github.com/x-cmd/terminal
related:
  - name: 0-latest-terminals
    url: https://github.com/x-cmd/terminal/blob/main/docs/0-latest-terminals.en.md
  - name: 2-alacritty
    url: https://github.com/x-cmd/terminal/blob/main/docs/2-alacritty.en.md

# Summary

A practical comparison of the terminal emulators people actually pick in 2026. The headline: GPU rendering is now the default across all major terminals, TOML is the new config standard, and the "do one thing, pair with tmux" philosophy (Alacritty, Ghostty, Rio) is mainstream. The decision tree picks Alacritty for raw speed, WezTerm for built-in multiplexer + Lua scripting, Kitty for image protocols, Ghostty for native per-platform UI. iTerm2 and Windows Terminal cover their respective platforms for users who want GUI config and built-in tabs/panes.