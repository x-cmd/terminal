---
name: 4-wezterm
description: GPU-accelerated terminal with built-in multiplexer, Lua scripting, and iTerm2 / Sixel image protocol support. Rust-built, MIT licensed, Linux / macOS / Windows / BSD.
type: summary
---

# Core Content

core_features:
  - GPU rendering via wgpu (Vulkan / Metal / DX12 / OpenGL)
  - Built-in multiplexer via wezterm-mux-server (panes, tabs, workspaces)
  - Lua configuration + scripting — wezterm.lua is a full Lua program
  - iTerm2 + Sixel image protocols (no Kitty graphics)
  - Live config reload via file watcher
  - Cross-platform in a single binary (Linux, macOS, Windows, BSD)

# Key Information

highlights:
  - "Multiplexer + scripting in one" — bet on consolidating tmux + zsh config + image protocol into one binary
  - Lua config can subscribe to events (window-focus-changed, open-uri, format-window-title)
  - SSH domain: wezterm connect ssh://user@host — remote session through local WezTerm
  - MIT licensed — permissive, commercial use OK
  - Larger binary than Alacritty / Kitty (more features)

# Use Cases

use_cases:
  - All-in-one workflow (no tmux required for most cases)
  - Cross-platform consistency — same wezterm.lua on Linux / macOS / Windows
  - Power users who want programmable config (Lua over TOML)
  - DevOps / SSH workflows via wezterm connect ssh://
  - Image-rich workflows via imgcat + yazi

# Related Resources

official:
  website: https://wezfurlong.org/wezterm/
  repo: https://github.com/wez/wezterm
related:
  - name: Alacritty
    url: https://alacritty.org/
  - name: Kitty
    url: https://sw.kovidgoyal.net/kitty/
  - name: Ghostty
    url: https://ghostty.org/
  - name: tmux
    url: https://github.com/tmux/tmux
  - name: yazi
    url: https://github.com/sxyazi/yazi

# Summary

WezTerm is the canonical "multiplexer + scripting in one" terminal — Rust-built, MIT licensed, cross-platform via wgpu. It ships a built-in multiplexer (wezterm-mux-server with panes, tabs, workspaces), Lua configuration (wezterm.lua is a full Lua program with event handlers and the full WezTerm API), and iTerm2 / Sixel image protocols. The bet: consolidate tmux + zsh config + image handling into one binary. Live config reload. SSH domain opens remote sessions through local WezTerm. Verdict: recommended for users who want multiplexer + scripting in one terminal; skip if you want the smallest terminal or TOML config.