---
x-title: 终端模拟器概览 — GPU、多路复用、配置、平台横向对比
x-desc: >-
  主流终端模拟器的横向对比 —— Alacritty、Kitty、WezTerm、Ghostty、Rio、Tabby、iTerm2、
  Windows Terminal、GNOME Terminal、foot、Konsole —— 覆盖渲染器、多路复用、配置格式、平台支持与最佳场景。
x-sidebar: 终端概览
x-keywords: 终端模拟器, 横向对比, alacritty, kitty, wezterm, ghostty, iterm2, windows terminal
x-json-ld:
  '@context': https://schema.org
  '@graph':
    - '@type': TechArticle
      headline: '终端模拟器概览'
      inLanguage: 'cn'
      about: '终端模拟器对比'
---

# 终端模拟器概览

2026 年大家真正在用的终端模拟器横向对比。当你知道自己想要
「*一个*终端」但还没决定要哪个时，看这一页。

## 对比表

| 终端 | 渲染器 | 多路复用 | 配置 | 平台 | 最佳场景 |
| --- | --- | --- | --- | --- | --- |
| **Alacritty** | OpenGL ES 2.0 | 外部（tmux / zellij） | TOML | Linux、macOS、BSD、Windows | 最小、最快、"只是个终端"；搭配 tmux |
| **Kitty** | OpenGL 3.3 | 内置（kitty-session）+ tmux | 纯文本 | Linux、macOS、BSD | 图像协议、kittens、GPU 可扩展 |
| **WezTerm** | wgpu（Vulkan / Metal / DX12 / OpenGL） | 内置（Lua） | Lua | Linux、macOS、Windows、BSD | 多路复用 + 脚本一体 |
| **Ghostty** | Metal / OpenGL | 外部（tmux / ghostty-session） | TOML / MoonBit | Linux、macOS、Windows | 每平台原生 UI，快速启动 |
| **Rio** | wgpu | 外部（tmux） | TOML | Linux、macOS、Windows | Alacritty 的较新 Rust 替代 |
| **Tabby** | CPU（Electron） | 内置（Electron 标签 / 面板） | YAML | Linux、macOS、Windows | 可视化配置、内置 SSH 管理 |
| **iTerm2** | CPU + Metal | 内置（面板 / 分屏 / 标签） | 偏好 UI / JSON | 仅 macOS | macOS 用户，要功能丰富的终端 |
| **Windows Terminal** | DirectX | 内置（面板） | JSON | 仅 Windows | Windows 用户，微软支持 |
| **GNOME Terminal** | CPU（VTE） | 外部（tmux） | GSettings / dconf | Linux | Linux 默认；GNOME 用户 |
| **Konsole** | CPU（VTE） | 内置（分屏 / 标签） | KConfig / ini | Linux、BSD | KDE 用户 |
| **foot** | CPU（wlroots） | 外部（tmux） | ini | Linux（Wayland） | 极简 Wayland 终端 |
| **st** | CPU（Xlib） | 外部（tmux） | C 源码补丁 | Linux、BSD | Suckless 极简；自己打补丁 |

## 如何读这张表

### 渲染器

- **GPU**（Metal / OpenGL / wgpu）—— 文本渲染到 GPU 上的字形图集。重输出（`cat huge.log`、`cargo build`、`tail -f`）时滚动顺滑。2026 年的默认选择。
- **CPU**（VTE / Xlib / Electron）—— 由 CPU 渲染文本。日常够用；重输出时卡顿。GNOME Terminal、Konsole、st、foot、iTerm2（旧）、Tabby。

### 多路复用

- **外部**——终端搭配 tmux、zellij 或平铺窗口管理器。代码不重复；用户自选最爱。
- **内置**——终端自带会话管理器。单应用工作流；可组合性差。

### 配置

- **TOML**——精简、支持注释、有 section。Alacritty、Rio、Ghostty 用。
- **纯文本**——Kitty 自有格式。
- **Lua**——可编程；WezTerm 用。
- **JSON**——冗长；Tabby、Windows Terminal 用。
- **偏好 UI**——点击式；iTerm2。

### 平台

Linux、macOS、BSD、Windows。一些是平台特定的（iTerm2 = 仅 macOS；Windows Terminal = 仅 Windows）。

## 如何选

一个简单的决策树：

1. **要最小、最快？** → Alacritty。搭配 tmux。
2. **要内置多路复用 + 脚本？** → WezTerm。
3. **要图像协议 / kittens？** → Kitty。
4. **要在 macOS 用原生 AppKit、Windows 用原生 Win32？** → Ghostty。
5. **要 GUI 配置编辑器？** → iTerm2（macOS）、Windows Terminal（Windows）、Tabby（任意）。
6. **GNOME / KDE 用户，要默认？** → GNOME Terminal / Konsole。
7. **要极简，Wayland？** → foot。
8. **要极简，自己打补丁？** → st。

## 平台说明

### macOS

最强的阵容。iTerm2 是长期默认；Alacritty 与 Kitty 有大量 macOS 用户；Ghostty 1.0 提供原生 AppKit UI。**Tabby** 与 **Rio** 也在 macOS 上可用。

### Linux

Alacritty、Kitty、WezTerm、Ghostty、foot、st 都是 Linux 原生。GNOME Terminal 与 Konsole 随各自桌面环境默认提供。大多数终端同时支持 X11 与 Wayland；st 仅 X11。

### Windows

历史上选择不多。2026 年的格局：

- **Windows Terminal** —— 微软官方终端；DirectX 渲染器；内置标签与面板。
- **Alacritty** —— 自 0.9 起通过 ConPTY 完整支持 Windows。
- **WezTerm** —— 完整 Windows 支持；跨平台配置的人气选择。
- **Ghostty** —— 自 1.0 起原生 Win32 支持。
- **Tabby** —— 基于 Electron；在 Windows 上表现良好。

Kitty 在 Windows 上需要 WSL 或 Cygwin；不是一线支持。

### BSD

Alacritty、Kitty、WezTerm、Ghostty、st、Konsole 都在 *BSD 变体上工作。foot 仅 Linux。

## 什么*不是*终端模拟器

为完整起见：

- **tmux / zellij / screen** —— 终端*多路复用器*，不是模拟器。它们跑在终端模拟器*里*。
- **xterm / mintty / ConEmu** —— 终端模拟器，但在 2026 年大体被取代；这里不深入。
- **浏览器 shell**（如 VS Code 的集成终端）—— 内嵌编辑器，不是独立终端模拟器。

## 相关

- [`docs/0-latest-terminals.cn.md`](./0-latest-terminals.cn.md) — 近期发布与趋势。
- [`docs/2-alacritty.cn.md`](./2-alacritty.cn.md) — Alacritty 深度介绍。
- [`docs/3-kitty.cn.md`](./3-kitty.cn.md) — Kitty 深度介绍（计划中）。
- [`docs/4-wezterm.cn.md`](./4-wezterm.cn.md) — WezTerm 深度介绍（计划中）。
- [`docs/5-ghostty.cn.md`](./5-ghostty.cn.md) — Ghostty 深度介绍（计划中）。