---
name: 4-terminal-image-protocols
description: Comparison angle on terminal image protocols — Sixel (1980s, pixel-level), iTerm2 image (2009, frame-based, PNG/JPEG), Kitty graphics (2018, path mode + shared memory). Capability matrix across major terminals, security risks of path mode.
type: summary
---

# Core Content

core_features:
  - Three image protocols: Sixel, iTerm2 image, Kitty graphics
  - Sixel — pixel-level, 6 bits/char, no path mode, data-only (safe)
  - iTerm2 image — base64 PNG/JPEG, frame-based, no path mode (safe)
  - Kitty graphics — multi-command, supports path mode (security risk) + shared memory mode
  - Per-terminal capability matrix (10 terminals × 3 protocols)
  - Practical tooling: yazi (file manager), viu / chafa / imgcat (image viewers), ls --icons / eza
  - Security: only Kitty path mode is a risk; safe-mode default refuses outside-CWD paths

# Key Information

highlights:
  - Sixel: oldest (1980s), pixel-level, pure data, slow for large images
  - iTerm2 image: 2009, base64 PNG/JPEG, widely supported, fast for animations
  - Kitty graphics: most feature-rich, supports path mode + shared memory
  - Alacritty and foot support Sixel only
  - Kitty is the only major terminal with full Kitty graphics support
  - Ghostty ships Sixel since 1.0; iTerm2 image + Kitty graphics to follow
  - yazi file manager uses all three protocols for image previews

# Use Cases

use_cases:
  - Choosing a terminal that supports image protocols for image-rich workflows
  - Selecting image protocol when building terminal-graphics tools
  - Vetting AI tools that may print image paths (security)
  - Understanding why some terminals show inline images and others don't

# Related Resources

related:
  - name: Kitty graphics protocol spec
    url: https://sw.kovidgoyal.net/kitty/protocol.html
  - name: iTerm2 image protocol
    url: https://iterm2.com/documentation-images.html
  - name: Sixel reference
    url: https://en.wikipedia.org/wiki/Sixel
  - name: yazi file manager
    url: https://github.com/sxyazi/yazi
  - name: viu image viewer
    url: https://github.com/atanunq/viu

# Summary

Three terminal image protocols coexist: Sixel (1980s DEC VT340, pixel-level 6 bits/char, pure data, slow for large images), iTerm2 image (2009, base64 PNG/JPEG, frame-based, widely supported, fast for animations), and Kitty graphics (2018, multi-command with path mode + shared memory mode, most feature-rich). Capability matrix: Kitty supports all three; WezTerm supports Sixel + iTerm2 image; Ghostty (since 1.0) supports Sixel; iTerm2 supports Sixel + iTerm2 image natively; Alacritty + foot + GNOME Terminal + Konsole support Sixel only; Windows Terminal + st have no image protocols. Practical tooling: yazi file manager uses all three protocols for image previews; viu / chafa / imgcat are modern image viewers; ls --icons / eza provide inline icons. Security: only Kitty graphics path mode is a risk — terminals refuse paths outside CWD by default (safe-mode default). Sixel and iTerm2 image are pure data — no security risk.