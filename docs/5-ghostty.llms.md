---
name: 5-ghostty
description: GPU-accelerated terminal with native UI per platform — AppKit on macOS, GTK4 / libadwaita on Linux, Win32 on Windows. Zig core + Swift wrapper on macOS. MIT licensed.
type: summary
---

# Core Content

core_features:
  - Native UI per platform: AppKit (macOS), GTK4/libadwaita (Linux), Win32 (Windows)
  - GPU rendering: Metal on macOS, OpenGL on Linux + Windows
  - TOML config with live reload
  - Sixel image protocol since 1.0 (iTerm2 + Kitty graphics planned)
  - Fast startup via Zig core + small native shell
  - Auto-update mechanism (Homebrew on macOS, system packages elsewhere)

# Key Information

highlights:
  - "One core, three native UIs" — bet against Electron-style cross-platform toolkits
  - No built-in tabs in 1.0 (planned for 1.2)
  - Sixel-only image protocol today
  - Linux requires GTK4 / libadwaita (GTK3 fallback removed in 1.0)
  - MIT licensed
  - Created by Mitchell Hashimoto (HashiCorp co-founder)

# Use Cases

use_cases:
  - Native UX on each platform (menu bar, GTK4 headerbar, taskbar jump list)
  - Fast startup (< 100 ms on most machines)
  - Lightweight + GPU-accelerated without Electron
  - Cross-platform consistency — same TOML config everywhere

# Related Resources

official:
  website: https://ghostty.org/
  repo: https://github.com/ghostty-org/ghostty
related:
  - name: Alacritty
    url: https://alacritty.org/
  - name: Kitty
    url: https://sw.kovidgoyal.net/kitty/
  - name: WezTerm
    url: https://wezfurlong.org/wezterm/
  - name: tmux
    url: https://github.com/tmux/tmux
  - name: yazi
    url: https://github.com/sxyazi/yazi

# Summary

Ghostty is the canonical "native per-platform UI terminal" — written by Mitchell Hashimoto, Zig core with Swift wrapper on macOS, MIT licensed. Ships native AppKit on macOS, GTK4/libadwaita on Linux, Win32 on Windows — first-class integration everywhere (menu bar, GTK4 headerbar, taskbar jump list). GPU-accelerated (Metal on macOS, OpenGL elsewhere). TOML config with live reload. Sixel image protocol since 1.0 (iTerm2 + Kitty graphics planned). No built-in tabs in 1.0 (target 1.2). Verdict: recommended for users who value native UX and fast startup; skip if you want built-in tabs now or a plugin system.