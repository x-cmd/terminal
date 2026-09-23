---
name: 3-terminal-performance
description: Performance-focused angle on terminal emulators — four axes (rendering speed, memory footprint, energy use, throughput under heavy output), GPU vs CPU trade-offs, per-terminal benchmarks (Alacritty / Kitty / WezTerm / Ghostty / iTerm2 / Windows Terminal / GNOME Terminal / foot / st), vtebench.
type: summary
---

# Core Content

core_features:
  - Four performance axes: rendering speed, memory footprint, energy use, throughput under heavy output
  - GPU vs CPU trade-off matrix
  - Per-terminal idle-RSS numbers
  - vtebench standardized benchmarks
  - GPU terminals: Alacritty, Kitty, WezTerm, Ghostty, iTerm2 (Metal), Windows Terminal (DirectX)
  - CPU terminals: GNOME Terminal, st, foot, Konsole
  - Hybrid: WezTerm wgpu with CPU fallback; Ghostty 1.0+

# Key Information

highlights:
  - GPU terminals win heavy-output throughput (cat huge.log, tail -f)
  - CPU terminals win idle memory + idle energy
  - Alacritty is smallest at idle (~5-15 MB)
  - foot is the leanest CPU Wayland terminal (~10-25 MB idle)
  - iTerm2 is heaviest (Cocoa-based, ~80-150 MB idle)
  - GPU terminals use more energy on idle (compositor always on)
  - Apple silicon + iTerm2 / Ghostty competitive on idle energy
  - vtebench provides standardized rendering / scrolling / parsing benchmarks

# Use Cases

use_cases:
  - Picking a terminal based on workload (heavy output vs battery vs minimal)
  - Benchmarking terminals on your hardware with vtebench
  - Diagnosing scrollback stutter on heavy output
  - Reducing idle memory for many open terminals
  - Choosing a server / minimal terminal

# Related Resources

related:
  - name: vtebench
    url: https://github.com/alacritty/vtebench
  - name: Alacritty FAQ
    url: https://alacritty.org/faq.html
  - name: Ghostty 1.0 release notes
    url: https://ghostty.org/

# Summary

Terminal performance has four axes: rendering speed, memory footprint, energy use, throughput under heavy output. GPU terminals (Alacritty, Kitty, WezTerm, Ghostty, iTerm2-Metal, Windows Terminal-DirectX) win heavy-output throughput because text is rendered as a GPU glyph atlas — 60 FPS sustained on cat huge.log / tail -f / cargo build. CPU terminals (GNOME Terminal, foot, st, Konsole) win idle memory (foot ~10 MB, st ~5 MB) and idle energy (no GPU compositor). Energy trade-off flips under heavy output: GPU terminals parallelize work on GPU; CPU terminals saturate the CPU. Per-terminal idle RSS: Alacritty 5-15 MB; Kitty 50-80 MB; WezTerm 30-60 MB; Ghostty 25-50 MB; iTerm2 80-150 MB; GNOME Terminal 30-60 MB; foot 10-25 MB; st 5-15 MB. Pick GPU for dev workloads; pick CPU for battery-constrained or minimal installs; pick hybrid (WezTerm wgpu, Ghostty 1.0+) for both worlds. vtebench provides standardized benchmarks to verify on your hardware.