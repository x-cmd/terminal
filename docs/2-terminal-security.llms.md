---
name: 2-terminal-security
description: Security-focused angle on terminal emulators — five attack surfaces (escape injection, escape-to-shell, OSC 52 clipboard hijack, OSC 8 hyperlink spoofing, inline image disclosure), per-terminal mitigations, secure-input modes, and shell integration (OSC 133).
type: summary
---

# Core Content

core_features:
  - Five terminal attack surfaces: escape-sequence injection, escape-to-shell (OSC 9 / OSC 1337), OSC 52 clipboard hijack, OSC 8 hyperlink spoofing, inline image / file disclosure
  - Per-terminal mitigation matrix (Alacritty, Kitty, WezTerm, Ghostty, iTerm2, Windows Terminal, GNOME Terminal, foot, st)
  - Secure-input modes — disable escape interpretation while user types
  - Shell integration (OSC 133) — marks command boundaries for trusted vs untrusted output
  - Trust boundary between terminal output and host execution

# Key Information

highlights:
  - iTerm2's OSC 9 escape-to-shell removed in 3.4 (2017); most modern terminals block OSC 9 by default
  - OSC 52 clipboard read/write is opt-in in most terminals
  - OSC 8 hyperlinks can phish — visible text ≠ actual URL
  - Kitty graphics protocol supports safe-mode (refuses paths outside CWD)
  - Sixel is data-only — no path mode — safe by default
  - OSC 133 / shell integration is the most effective single mitigation
  - AI tool output (CI logs, chat) is a primary threat vector for OSC 9 / OSC 52

# Use Cases

use_cases:
  - Auditing terminal security for sensitive workflows (admin, CI/CD, financial)
  - Choosing a terminal that supports shell integration
  - Disabling risky OSC features (OSC 9, OSC 52 read/write, inline images)
  - Sandboxing terminals for high-risk command flows
  - Vetting AI tools for terminal-output attack vectors

# Related Resources

related:
  - name: iTerm2 OSC 9 fix
    url: https://iterm2.com/news.html
  - name: Kitty graphics protocol
    url: https://sw.kovidgoyal.net/kitty/protocol.html
  - name: Ghostty docs
    url: https://ghostty.org/docs

# Summary

Terminal security is the trust boundary between arbitrary untrusted text (CI logs, AI tool output, chat) and the host. Five attack surfaces: (1) escape-sequence injection (cursor / scrollback manipulation), (2) escape-to-shell via OSC 9 / OSC 1337 (iTerm2's famous historical bug, removed 2017), (3) OSC 52 clipboard hijack (read or overwrite the clipboard), (4) OSC 8 hyperlink spoofing (visible text ≠ actual URL), (5) inline image / file disclosure (Kitty graphics path mode, mitigated by safe-mode). Per-terminal mitigation matrix shows: most modern terminals block OSC 9 by default, OSC 52 is opt-in, OSC 8 hyperlinks require Ctrl/Cmd-hover to reveal. The most effective single mitigation is OSC 133 shell integration (marks command boundaries); supported by Alacritty, Kitty, WezTerm, Ghostty, iTerm2 — not Windows Terminal or GNOME Terminal. Secure-input modes disable escape parsing while the user types. Practical checklist: disable OSC 9 + OSC 1337, disable OSC 52 read/write, verify OSC 8 hover, disable inline images for sensitive workflows, enable OSC 133 shell integration, audit AI tool output, sandbox the shell for high-risk commands.