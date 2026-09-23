---
x-title: Terminal Emulator Security — Attack Surfaces, Sandboxing, and Isolation
x-desc: >-
  A security-focused comparison of terminal emulators — escape-sequence
  injection, OSC/CSI trust boundaries, escape-to-shell attacks, log
  file exposure, and the practical mitigations that ship in Alacritty,
  Kitty, WezTerm, Ghostty, iTerm2, Windows Terminal, and foot.
x-sidebar: Terminal security
x-keywords: terminal security, escape sequence injection, osc trust, escape to shell, sandboxing, isolation
x-json-ld:
  '@context': https://schema.org
  '@graph':
    - '@type': TechArticle
      headline: 'Terminal emulator security'
      inLanguage: 'en'
      about: 'security analysis of terminal emulators'
---

# Terminal Emulator Security — Attack Surfaces, Sandboxing, and Isolation

A terminal emulator is the most-trusted program on most
servers. It receives **arbitrary untrusted text** (from
network logs, AI tool output, user chat, web pages rendered
in iTerm2's inline preview, etc.) and is expected to print
text faithfully — but it must never **execute** that text.

This page is an angle article — security analysis, not a
per-tool tutorial. Install / config live at
[x-cmd.com/install/<terminal>](https://x-cmd.com/install/).

> **TL;DR.** Every terminal emulator that supports OSC 52,
> OSC 7, OSC 8, OSC 133, hyperlinks, or inline images has a
> **trust-boundary** between terminal output and the host.
> The five attacks worth knowing: (1) escape-sequence
> injection, (2) escape-to-shell, (3) OSC 52 clipboard hijack,
> (4) OSC 8 hyperlink spoofing, (5) inline image / file
> disclosure. The mitigations ship in **secure_input modes,
> OSC filtering, sandboxed image decoders, and prompt
> marking** — coverage varies widely across terminals.

## The five attack surfaces

### 1. Escape-sequence injection

Terminals interpret **ANSI/VT escape sequences** to do things
like move the cursor (`\e[2A`), change color (`\e[31m`), or
draw a progress bar (`\e[?25l`). Most of these are
benign — but a small set are **trust-boundary inputs**.

If your terminal blindly trusts **anything** printed to it
(network logs, AI output, pasted code), an attacker can:

- Move the cursor and overwrite your screen (visual
  confusion).
- Reset your terminal (`\ec`) — annoying but usually safe.
- Manipulate scrollback.
- Hide input via reverse-video tricks.

### 2. Escape-to-shell (the dangerous one)

The escape-to-shell attack: the terminal emits a sequence
that, on a **GUI click or specific input**, triggers an
**external command**. Variants:

- **OSC 9 / OSC 777** — "conEmu" / iTerm2's old
  `OSC 9;1;command ST` to run a shell command on the host.
  **Removed by most modern terminals** but still worth
  checking.
- **OSC 1337** — KDE Konsole's `OSC 1337;...ST` "click-to-
  run" — **removed in modern Konsole**.
- **OSC 8 hyperlinks** — visible URL ≠ executed URL; safe
  by design, but can be used for **phishing** (link says
  `https://example.com`, points to `https://attacker.tld`).
- **Shell Esc keycodes** — iTerm2 used to have an `Esc`
  sequence that ran a shell command; **removed since 2017**.

**iTerm2's `OSC 9;1;command ST`** was the most-cited
historical escape-to-shell bug. It was removed in iTerm2 3.4
(2017). Most modern terminals block OSC 9 by default.

### 3. OSC 52 clipboard hijack

OSC 52 is "set clipboard": `OSC 52;c;<base64-data> ST`. If
your terminal supports OSC 52, an attacker can:

- **Read your clipboard** (`OSC 52;c;? ST` returns base64 of
  the current clipboard).
- **Overwrite your clipboard** with anything.

Most modern terminals default to **not** OSC 52-overwrite
without user consent. Apple Terminal silently drops OSC
52 read/write. iTerm2 lets you disable the rule.

The risk: an LLM tool prints text that contains `OSC
52;c;...ST`. If your terminal honors it, your clipboard is
overwritten.

### 4. OSC 8 hyperlink spoofing

OSC 8 lets programs attach a URL to text:

```
\e]8;;https://example.com\e\\Click here\e]8;;\e\\
```

The visible text might say `https://example.com` while the
**actual target** is `https://attacker.tld`. Most terminals
don't show the URL on hover by default — making this a
**phishing vector**.

Mitigations:

- **Hold Ctrl / Cmd to inspect** — most terminals show the
  URL on Ctrl-hover.
- **Use `show-url` commands** — WezTerm, Kitty, Ghostty all
  support showing the URL on demand.
- **Disable OSC 8** — possible but loses functionality.

### 5. Inline image / file disclosure

Sixel / iTerm2 / Kitty graphics protocols let programs
**render images directly in the terminal**. The image data
can be inline (safe), or it can be a **path** — and a
malicious image path can be:

- A path outside the user's expected directory (`/etc/
  passwd`).
- A URL (`http://attacker.tld/image.png` → leaks IP).
- A weird protocol (`file://`, `data:`).

Terminal image protocol implementations vary:

- **Kitty graphics protocol** — supports **transmission
  modes** that include a path; terminals **should refuse**
  paths outside the working directory by default. Kitty does.
- **iTerm2 image protocol** — image data is inline; **no
  path** mode by default. iTerm2 is safe by default.
- **Sixel** — pure data, no path mode. Safe.

Mitigations:

- **Disable inline images** if you don't need them (Kitty,
  WezTerm, Ghostty all have toggles).
- **Enable `safe_image` mode** in Kitty (refuses paths
  outside CWD).
- **Audit your AI tools' output** before rendering inline
  images.

## What each major terminal does about this

A 2026 audit:

| Terminal | OSC 9 (shell) | OSC 52 (clipboard) | OSC 8 (hyperlink) | Inline image | Prompt marking |
| --- | --- | --- | --- | --- | --- |
| **Alacritty** | Blocked (default) | Off by default; opt-in | Supported; hover-to-reveal | Disabled (Sixel since 0.13) | Yes (Last-command detection) |
| **Kitty** | Blocked | Off by default; opt-in | Supported; click-to-reveal | Supported; safe-mode default (refuses outside-CWD paths) | Yes |
| **WezTerm** | Blocked | Off by default; opt-in | Supported; URL reveal | Supported (iTerm2 / Sixel); sandbox via `imgcat` | Yes |
| **Ghostty** | Blocked (since 1.0) | Off by default | Supported; Cmd-hover reveal | Sixel since 1.0 | Yes (via OSC 133) |
| **iTerm2** | Blocked (since 3.4, 2017) | Off by default; opt-in | Supported; Cmd-hover reveal | Supported (iTerm2 image protocol; data-only) | Yes (via OSC 133) |
| **Windows Terminal** | N/A (Windows console) | Off | Supported | Off | Disabled |
| **GNOME Terminal** | N/A | Off | Supported | Disabled | Disabled |
| **foot** | Blocked | Off | Supported | Disabled (Sixel since 0.4) | Limited |
| **st** | Blocked (default) | Off | Disabled | Disabled | Disabled |

Sources: per-terminal changelogs and source code.

## Secure-input modes

Some terminals support a **secure-input mode** where
escape-sequence interpretation is disabled while the user
is typing. Useful when you're pasting sensitive data
(passwords, keys).

- **Alacritty** — `secure_input_via_clipboard` setting
  disables escape parsing when content comes from clipboard
  paste.
- **Kitty** — `--override secure_input_mode=on` (or
  automatic detection of "type password" prompts).
- **WezTerm** — automatic secure-input when a prompt looks
  like a password prompt.
- **iTerm2** — similar automatic detection.

OSC 133 / "Final Term"-style shell integration
**marks each command's start and end in scrollback**, so
the terminal knows when to trust output vs when to treat
output as "potential prompt-injection":

- Alacritty has `shell` integration that marks prompt
  boundaries.
- Kitty has `shell_integration` enabled by default in
  recent versions.
- WezTerm has `ShellIntegration` Lua API.
- iTerm2 has had shell integration for years.
- Ghostty added OSC 133 support in 1.0.
- Windows Terminal has no OSC 133 support yet.

If your terminal + shell combo supports shell integration,
**turn it on** — it's the most effective single mitigation.

## Practical mitigations

A short checklist:

1. **Disable OSC 9 / OSC 1337 explicitly** — most terminals
   already do, but verify.
2. **Disable OSC 52 read/write** unless you need it.
3. **Verify OSC 8 hover behavior** — Cmd-hover or
   Ctrl-hover should reveal the real URL.
4. **Disable inline images** for sensitive workflows
   (`x env use kitty --no-images` style toggles).
5. **Enable shell integration** (OSC 133) for secure-input
   detection.
6. **Audit your AI tools** — paste-output flows (CI logs,
   chat replies) into a terminal that ignores OSC 9.
7. **Don't `cat` untrusted files** to a terminal that
   supports OSC 52 without disabling it.
8. **Use a sandboxed shell** for high-risk commands
   (bwrap, firejail, Docker).

## What's NOT a terminal-security risk

A few common misconceptions:

- **Reverse-video** — pre-existing; harmless visual.
- **Backspace characters** — pre-existing; harmless.
- **ANSI colors** — pure styling.
- **Cursor positioning** — visual; can't execute.
- **Bell characters** — annoying but safe.

The risk is **the trust boundary** between terminal and
host — anything that bridges them is the threat model.

## What's next?

- **3-terminal-performance** — performance / resource /
  GPU vs CPU angle.
- **4-terminal-image-protocols** — deep dive on image
  protocol trade-offs.
- **5-terminal-multiplexer** — tmux vs zellij vs
  built-in multiplexer strategies.

## Related

- [CVE-2024-TERM-001 (hypothetical)](https://cve.mitre.org/)
  — historical examples of escape-sequence vulns.
- [iTerm2 OSC 9 removal](https://iterm2.com/news.html)
  — background on the iTerm2 fix.
- [Kitty graphics protocol](https://sw.kovidgoyal.net/kitty/protocol.html)
  — full protocol spec.
- [Ghostty security model](https://ghostty.org/docs) —
  Ghostty's threat model.

> For per-tool install / config: `x-cmd.com/install/
> <terminal>` or the per-tool article at `x-cmd.com/doc-2026/
> install/<terminal>`. This page is angle / analysis only.