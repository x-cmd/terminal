---
x-title: Terminal Image Protocols — Sixel, iTerm2, and Kitty Graphics
x-desc: >-
  A side-by-side comparison of the three terminal image protocols —
  Sixel (DEC VT340), iTerm2 image protocol, Kitty graphics protocol —
  their history, encoding, capability matrix across terminals, and
  practical tooling that uses them (ls with image previews, image
  viewers, charts).
x-sidebar: Terminal image protocols
x-keywords: terminal image protocol, sixel, iterm2 image, kitty graphics, ls --icon, viu
x-json-ld:
  '@context': https://schema.org
  '@graph':
    - '@type': TechArticle
      headline: 'Terminal image protocols'
      inLanguage: 'en'
      about: 'terminal image protocol comparison'
---

# Terminal Image Protocols — Sixel, iTerm2, and Kitty Graphics

Three protocols let programs **render images directly in
the terminal**: **Sixel**, **iTerm2 image**, and **Kitty
graphics**. Each has a different history, encoding, and
support matrix. Most modern terminals implement all three.

This page is an angle article — image protocol comparison,
not a per-tool tutorial. Install / config live at
[x-cmd.com/install/<terminal>](https://x-cmd.com/install/).

> **TL;DR.** Sixel (1980s) is data-only and pixel-level.
> iTerm2 image (2009) is data-only and frame-based.
> Kitty graphics (2018) is data-only OR path-based (path
> mode is the dangerous one — refuse outside-CWD). Most
> modern terminals implement all three. Use **ls --icon /
> eza --icons / yazi** for file manager previews; use
> **viu / chafa / imgcat** for image viewing; use
> **Klipper / lsix** for legacy Sixel.

## The three protocols

### Sixel (DEC VT340, 1980s)

Sixel is the **oldest**. It was part of DEC's VT340 terminal
(1983). Encoding: 6 bits per character (A-P for value
0-15; special chars for newlines, repeat, end-of-frame).

```sixel
\ePq
#0;2;0;0;0#1;2;100;100;0#2;2;0;100;0
~~CDDDDDDDD`????????
\e\\
```

Each character encodes 6 pixels vertically. A full image
is sent as a stream of Sixel characters.

Pros:

- Data-only — no path mode, no security risk.
- Works in any terminal that supports Sixel (most do
  today).

Cons:

- Limited color (originally 16 colors; modern terminals
  support higher via palette extensions).
- Pixel-level (no image compression).
- Slow for large images (the stream is large).

### iTerm2 image protocol (2009)

Designed by iTerm2 to solve Sixel's slowness. Encoding:
**base64-encoded PNG / JPEG**, sent as a single OSC
sequence with a chunked transfer mode.

```
\e]1337;File=name=<base64>;size=<bytes>;width=<px>;height=<px>;preserveAspectRatio=1:<base64-chunk>\e\\
```

Modern terminals (Kitty, WezTerm, Ghostty, Konsole)
implement the iTerm2 image protocol.

Pros:

- Supports PNG / JPEG natively (compression).
- Frame-based — fast for animations.
- Widely supported.

Cons:

- Data-only — no path mode in the standard.
- Some terminals (Kitty) prefer their own graphics
  protocol; iTerm2 image is the fallback.

### Kitty graphics protocol (2018)

Designed by Kovid Goyal (Kitty author). Encoding: a
**multi-command protocol** with explicit transmission
modes.

Modes:

- **Direct data** — base64-encoded image data in the
  protocol itself (similar to iTerm2).
- **File path** — the protocol points to a file path;
  the terminal reads the file.
- **Shared memory** — high-throughput via shared
  memory between process and terminal.

```
\e_Gi=1;f=100;t=PNG;s=<size>;v=<height>;a=T,C,U<base64-chunk>\e\\
\e_Ga=m,f=100,t=PNG,s=<size>;...<next-chunk>\e\\
\e_Ga=quit\e\\
```

Pros:

- Most feature-rich of the three.
- Supports animations, placement control, virtual
  placement, transmission modes.
- Shared memory mode for high-throughput.

Cons:

- **Path mode** is a security risk — a malicious path
  could read `/etc/passwd`. Mitigated by safe-mode
  default in Kitty.

## Capability matrix (2026)

| Terminal | Sixel | iTerm2 image | Kitty graphics | Notes |
| --- | --- | --- | --- | --- |
| **Alacritty** | ✓ (since 0.13) | ⚠️ (third-party) | ❌ | Limited; Alacritty's philosophy is minimal. |
| **Kitty** | ✓ | ✓ | ✓ (native) | All three. |
| **WezTerm** | ✓ | ✓ | ⚠️ (partial) | Two main protocols. |
| **Ghostty** | ✓ (since 1.0) | ⚠️ (third-party) | ❌ | Sixel only in 1.0; iTerm2 / Kitty planned. |
| **iTerm2** | ✓ | ✓ (native) | ❌ | iTerm2 image is iTerm2's native. |
| **Windows Terminal** | ❌ | ❌ | ❌ | No image protocols. |
| **GNOME Terminal** | ✓ | ⚠️ (third-party) | ❌ | Sixel only. |
| **foot** | ✓ (since 0.4) | ❌ | ❌ | Sixel only. |
| **st** | ❌ | ❌ | ❌ | Suckless minimalism. |
| **Konsole** | ✓ | ❌ | ❌ | Sixel only. |

✓ = supported; ⚠️ = third-party or partial; ❌ = not supported.

## Practical tooling

### File managers with image previews

```
ls --icons                  # modern ls replacement
eza --icons                 # eza is a popular ls replacement
yazi                        # terminal file manager with image previews
broot                       # tree viewer (no images)
```

yazi uses iTerm2 / Sixel / Kitty graphics for image
previews. Highly recommended for image-rich workflows.

### Image viewers

```
viu                         # small Rust image viewer (iTerm2 / Kitty / Sixel)
chafa                       # more flexible image-to-terminal converter
imgcat                      # macOS / iTerm2 classic
lsix                        # Sixel-only image viewer (older)
```

`viu` and `chafa` are the modern choices. They support
multiple protocols and handle PNG / JPEG / GIF.

### Charts and graphs

```
chart-image                 # graphviz → terminal image
go-echarts                  # Go charts → terminal
spark                       # sparkline-style charts
```

For data visualization in the terminal.

## Choosing a protocol

For most use cases, **enable all three** in your terminal
config. Each program picks what it supports. Kitty supports
all three and is the safest default.

For legacy code or scripts that need Sixel-only, **Alacritty,
WezTerm, Ghostty, foot, GNOME Terminal, Konsole** all
support Sixel.

For animations and high-throughput, **Kitty graphics** is
the best (with shared-memory mode for performance).

For just images (no animation), **iTerm2 image** is fine and
widely supported.

## Security: which protocol is safest?

- **Sixel** — pure data. Safe.
- **iTerm2 image** — pure data. Safe.
- **Kitty graphics** — supports **path mode** in addition
  to data mode. Path mode is a security risk — terminals
  should refuse paths outside the CWD by default.

Mitigation:

- **Kitty's `safe_image_protocol` is on by default** —
  refuses paths outside CWD.
- **Disable image protocols** in sensitive workflows.
- **Audit AI tools** that print image paths.

## What's next?

- **5-terminal-multiplexer** — tmux vs zellij vs built-in
  multiplexer strategies.

## Related

- [Kitty graphics protocol spec](https://sw.kovidgoyal.net/kitty/protocol.html)
- [iTerm2 image protocol](https://iterm2.com/documentation-images.html)
- [Sixel reference](https://en.wikipedia.org/wiki/Sixel)
- [yazi file manager](https://github.com/sxyazi/yazi)
- [viu image viewer](https://github.com/atanunq/viu)

> For per-tool install / config: `x-cmd.com/install/
> <terminal>` or `x-cmd.com/doc-2026/install/<terminal>`. This
> page is angle / analysis only.