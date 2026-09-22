---
name: 3-kitty
description: GPU-accelerated terminal with first-class image protocols (Sixel, Kitty graphics, iTerm2), extensible kitten plugin system, and built-in multiplexer. C + Python, GPL-3.0. Linux, macOS, BSD.
type: summary
---

# Core Content

core_features:
  - GPU rendering via OpenGL 3.3
  - First-class support for 3 image protocols: Sixel, Kitty graphics, iTerm2
  - Extensible kitten framework (Python) — icat, diff, hyperlinked-grep, ssh, themes, transfer
  - Built-in multiplexer via kitty @ launch + Ctrl+Shift+Enter
  - Plain-text kitty.conf with live reload
  - GPU extensibility — any program can render to the terminal via Kitty graphics protocol

# Key Information

highlights:
  - "Most programmable GPU terminal" — bet on extensibility, not raw size
  - Kittens run in separate processes and talk to kitty over a Unix socket
  - OpenGL 3.3 required since 0.40 (was 3.0)
  - Not officially supported on Windows
  - GPL-3.0 — stricter than Apache-2.0 / MIT

# Use Cases

use_cases:
  - Image-rich workflows (inline image display, animations)
  - Developer tools (side-by-side diffs, clickable grep)
  - Remote sessions with GPU protocols over SSH (kitten ssh)
  - Heavy configuration — kitty.conf is highly configurable
  - Multi-window + multi-pane workflows

# Related Resources

official:
  website: https://sw.kovidgoyal.net/kitty/
  repo: https://github.com/kovidgoyal/kitty
related:
  - name: Alacritty
    url: https://alacritty.org/
  - name: WezTerm
    url: https://wezfurlong.org/wezterm/
  - name: Ghostty
    url: https://ghostty.org/
  - name: viu
    url: https://github.com/atanunq/viu

# Summary

Kitty is the canonical "GPU terminal with image protocols" — written by Kovid Goyal (Calibre author), C + Python, GPL-3.0, GPU-accelerated via OpenGL 3.3. It ships first-class support for Sixel / Kitty graphics / iTerm2 image protocols, an extensible kitten plugin framework (icat, diff, hyperlinked-grep, ssh, themes, transfer), and a built-in multiplexer. The bet: not the smallest or fastest terminal, but the most programmable. Plain-text kitty.conf with live reload. Verdict: recommended for image-rich workflows and plugin-extensible setups; skip if you want the smallest terminal or native Windows support.