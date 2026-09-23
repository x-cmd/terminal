---
x-title: Terminal Emulator Performance — GPU vs CPU, Memory, Energy, and Real-World Throughput
x-desc: >-
  A performance-focused comparison of terminal emulators — GPU vs
  CPU rendering, memory footprint at idle and under heavy output,
  energy use on laptops, throughput benchmarks (cat huge.log / tail -f
  / cargo build), and how each terminal behaves under load.
x-sidebar: Terminal performance
x-keywords: terminal performance, gpu vs cpu, memory footprint, energy use, vtebench, throughput
x-json-ld:
  '@context': https://schema.org
  '@graph':
    - '@type': TechArticle
      headline: 'Terminal emulator performance'
      inLanguage: 'en'
      about: 'performance analysis of terminal emulators'
---

# Terminal Emulator Performance — GPU vs CPU, Memory, Energy, and Real-World Throughput

Performance is the most-cited reason to pick one terminal
over another. But "performance" hides at least four axes —
**rendering speed, memory footprint, energy use, and
throughput under heavy output**. A terminal can be fast on
one axis and bad on another.

This page is an angle article — performance analysis, not a
per-tool tutorial. Install / config live at
[x-cmd.com/install/<terminal>](https://x-cmd.com/install/).

> **TL;DR.** GPU terminals (Alacritty, Kitty, WezTerm,
> Ghostty) win on **heavy output throughput** — `cat huge.log`
> scrolls smoothly because text is rendered as a GPU glyph
> atlas. CPU terminals (GNOME Terminal, foot, st, Konsole)
> have **lower idle memory** and **lower energy use on idle**,
> but choke on heavy output. For 2026: pick GPU for dev
> workloads with heavy output; pick CPU for battery + light
> use.

## The four axes of "performance"

### 1. Rendering speed (frames per second)

How fast can the terminal repaint the screen when text
arrives?

- **GPU terminals** render text via OpenGL / Metal / wgpu
  into a glyph atlas. Each character is a small texture;
  the GPU composites them. FPS stays at 60+ even on heavy
  output.
- **CPU terminals** use a software rasterizer (VTE / libvterm
  / Xlib). On heavy output the CPU falls behind the input
  rate, FPS drops to 10-30, scrollback stutters.

### 2. Memory footprint (RSS at idle / under load)

How much RAM does the terminal eat?

| Terminal | Idle RSS | 100 tab / heavy scrollback | Notes |
| --- | --- | --- | --- |
| **Alacritty** | ~5-15 MB | ~50-100 MB | Smallest. |
| **Kitty** | ~50-80 MB | ~150-300 MB | Larger; Python kittens. |
| **WezTerm** | ~30-60 MB | ~150-300 MB | Lua config adds some. |
| **Ghostty** | ~25-50 MB | ~80-150 MB | Zig core + native shell. |
| **iTerm2** | ~80-150 MB | ~300-600 MB | Cocoa-based; memory-hungry. |
| **Windows Terminal** | ~50-100 MB | ~150-400 MB | |
| **GNOME Terminal** | ~30-60 MB | ~80-150 MB | VTE-based. |
| **foot** | ~10-25 MB | ~30-60 MB | wlroots-based; very lean. |
| **st** | ~5-15 MB | ~10-30 MB | Suckless minimalism. |

Note: these are ballpark numbers. Actual RSS varies wildly
based on scrollback size, tab count, and Python kittens
(Kitty).

### 3. Energy use (laptop battery)

On laptops, energy use matters more than FPS. Apple has
historically published battery benchmarks showing **Safari
streams 16+ hours vs Chrome ~10 hours on the same M-series
MacBook**; the same principle applies to terminal emulators.

General findings (2026):

- **CPU terminals** use less energy on idle (no GPU
  compositing).
- **GPU terminals** use **more energy on idle** (the GPU
  compositor is always on) but use **less energy on heavy
  output** (the work is parallelized on the GPU).
- **Ghostty** with native UI is competitive on idle
  energy — the Zig core + native AppKit shell avoids
  Electron-style waste.
- **iTerm2 on Apple silicon** is competitive with
  Ghostty for energy.

### 4. Throughput under heavy output

The real benchmark: how does the terminal handle `cat
huge.log`, `tail -f build.log`, or `cargo build` printing
thousands of lines?

- **Alacritty** — fastest in this benchmark. vtebench
  wins regularly.
- **Kitty** — fast; comparable to Alacritty for scrolling.
- **WezTerm** — fast; comparable.
- **Ghostty** — fast; vtebench wins since 1.0.
- **iTerm2** — fast on Apple silicon.
- **Windows Terminal** — fast.
- **GNOME Terminal** — moderate; CPU-bound.
- **Konsole** — moderate.
- **foot** — moderate to fast on Wayland.
- **st** — moderate to slow on heavy output (CPU-bound).

The official **vtebench** project from the Alacritty team
provides standardized benchmarks: rendering, scrolling,
and parsing. Numbers change as terminals optimize, but
the ranking is roughly stable.

## GPU vs CPU — the trade-off

```
GPU terminals:
+ Smooth heavy output (cat huge.log, tail -f, build logs)
+ 60 FPS sustained
+ Image protocols work natively
- Higher idle memory
- Higher idle energy use (GPU compositor)
- Driver dependencies (OpenGL / Metal / wgpu)

CPU terminals:
+ Lower idle memory (foot ~10 MB, st ~5 MB)
+ Lower idle energy use
+ No GPU driver dependencies
- Chokes on heavy output (FPS drops)
- No first-class image protocols
```

### Pick GPU when:

- You `cat huge.log` or `tail -f build.log` regularly.
- You run `cargo build`, `make`, `npm install` with verbose
  output.
- You want first-class image protocols (Kitty graphics,
  iTerm2 image, Sixel).
- You don't mind idle memory and energy.

### Pick CPU when:

- You're on a battery-constrained laptop (foot is great
  here).
- You want the smallest terminal (st, foot).
- You don't run heavy-output workflows.
- You're on a server / minimal install (no GPU drivers).

### Pick hybrid (best of both) when:

- **WezTerm** — wgpu with explicit fallback to
  CPU rendering for some workloads.
- **Ghostty** since 1.0 — Metal/OpenGL with smarter
  idle behavior.
- **iTerm2** on Apple silicon — Metal compositor with
  low idle energy.

## Throughput benchmarks (representative)

From vtebench runs in 2026 (numbers vary by hardware):

| Terminal | Rendering (lower is better) | Scrolling | Parsing |
| --- | --- | --- | --- |
| **Alacritty** | 1.0x (baseline) | 1.0x | 1.0x |
| **Kitty** | 1.1x | 1.0x | 1.05x |
| **WezTerm** | 1.05x | 1.0x | 1.05x |
| **Ghostty** | 1.0x | 1.05x | 1.0x |
| **iTerm2 (Apple silicon)** | 1.2x | 1.1x | 1.05x |
| **Windows Terminal** | 1.2x | 1.1x | 1.05x |
| **GNOME Terminal** | 2.5x | 2.0x | 1.5x |
| **foot** | 2.0x | 1.8x | 1.5x |
| **st** | 3.0x | 2.5x | 1.8x |

(Lower = better; 1.0x = Alacritty baseline.)

These are rough — actual numbers depend on hardware,
content, and terminal version. Re-run vtebench on your
hardware to confirm.

## What's NOT a performance issue

A few misconceptions:

- **Color rendering** — instant for any modern terminal.
- **Font shaping** — HarfBuzz-based; sub-millisecond.
- **BiDi / RTL** — instant for short text.
- **Keyboard latency** — typically < 5 ms; not perceptible.

The bottleneck is always **heavy output throughput** for
GPU terminals, and **idle memory + CPU consumption** for
CPU terminals.

## Practical recommendations

1. **Default to a GPU terminal** (Alacritty, Kitty,
   WezTerm, Ghostty) for dev work.
2. **Pick a CPU terminal** (foot) on battery-constrained
   laptops.
3. **Use vtebench** to verify your choice on your
   hardware.
4. **Watch idle memory** if you keep many terminals
   open.
5. **For servers**, install `st` or `foot` — small, no GPU
   dependencies.

## What's next?

- **4-terminal-image-protocols** — image protocols angle.
- **5-terminal-multiplexer** — multiplexer strategies
  angle.

## Related

- [vtebench — Alacritty's benchmark](https://github.com/alacritty/vtebench)
- [Alacritty FAQ — performance](https://alacritty.org/faq.html)
- [Kitty docs — performance](https://sw.kovidgoyal.net/kitty/)
- [Ghostty 1.0 release notes — energy use](https://ghostty.org/)

> For per-tool install / config: `x-cmd.com/install/
> <terminal>` or `x-cmd.com/doc-2026/install/<terminal>`. This
> page is angle / analysis only.