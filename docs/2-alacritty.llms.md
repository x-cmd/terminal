---
name: 2-alacritty
description: Fast, GPU-accelerated terminal emulator written in Rust with minimal dependencies. Renders text via OpenGL ES 2.0; pairs with tmux for multiplexing. Apache-2.0; Linux / macOS / BSD / Windows.
type: summary
---

# Core Content

core_features:
  - GPU-accelerated rendering via OpenGL ES 2.0 glyph atlas
  - Minimal footprint; pairs with tmux / zellij for multiplexing
  - TOML configuration with live reload
  - Vi mode, regex hints, search, multi-window via alacritty msg
  - Single binary, no runtime dependencies
  - Cross-platform: Linux, macOS, BSD, Windows (ConPTY required)

# Key Information

highlights:
  - Smallest, fastest GPU terminal in common use as of 2026
  - Apache-2.0; no telemetry, no account, no SaaS
  - TOML config replaces YAML since 0.12
  - Two crates: alacritty_terminal (VTE parser / grid) + alacritty (OpenGL window / event loop)
  - Requires OpenGL ES 2.0 or higher; Windows requires ConPTY (10 v1809+)

# Use Cases

use_cases:
  - Heavy-output dev (cat huge.log, cargo build, tail -f)
  - Pairing with tmux for session management
  - Low-resource servers (Raspberry Pi, VMs)
  - Keyboard-driven workflows (Vi mode, Hints)
  - Cross-platform single-config setup across Linux / macOS / Windows

# Related Resources

official:
  website: https://alacritty.org/
  repo: https://github.com/alacritty/alacritty
related:
  - name: Kitty
    url: https://sw.kovidgoyal.net/kitty/
  - name: WezTerm
    url: https://wezfurlong.org/wezterm/
  - name: Ghostty
    url: https://ghostty.org/
  - name: tmux
    url: https://github.com/tmux/tmux

# Summary

Alacritty is the canonical "smallest, fastest" GPU terminal emulator — Rust-built, OpenGL ES 2.0-accelerated, Apache-2.0, cross-platform. It deliberately does one thing (render text fast) and pairs with tmux / zellij for multiplexing. TOML config with live reload. Two-crate architecture: alacritty_terminal (parser/grid) and alacritty (window/event loop). Verdict: recommended for terminal power users who want maximum speed; skip if you want a one-app-does-it-all terminal like iTerm2.