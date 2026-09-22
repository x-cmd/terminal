---
x-title: 最新终端模拟器 — 2026 年动态
x-desc: >-
  终端模拟器领域的最新发布、趋势与破坏性变更 —— Ghostty 1.0、WezTerm 2026、
  Alacritty 0.13、Kitty 0.43，以及 GPU 加速渲染的崛起。
x-sidebar: 最新终端
x-keywords: 终端模拟器, ghostty, kitty, wezterm, alacritty, rio, 2026
x-json-ld:
  '@context': https://schema.org
  '@graph':
    - '@type': TechArticle
      headline: '最新终端模拟器 — 2026'
      inLanguage: 'cn'
      about: '终端模拟器新闻与发布'
---

# 最新终端模拟器 — 2026 年动态

2026 年终端模拟器领域的新发布、新变化与新方向快照。每季度更新。

## 近期发布

| 日期 | 终端 | 版本 | 主要变化 |
| --- | --- | --- | --- |
| 2026-08 | **Ghostty** | 1.0 | 经历约两年预发布后的首个稳定版。Linux 用原生 GTK / libadwaita，macOS 用原生 AppKit，Windows 用原生 Win32。 |
| 2026-06 | **WezTerm** | 2026.06.00 | 支持 Wayland 1.24 协议；导入 iTerm2 图像协议；改进 Lua 配置体验。 |
| 2026-05 | **Kitty** | 0.43 | 新的图形光标（GPU 渲染）；iTerm2 / KiTTY / Sixel 图像协议稳定化。 |
| 2026-03 | **Alacritty** | 0.13.2 | 0.13 系列的 bug fix；0.14 在积极开发。 |
| 2026-02 | **Rio** | 0.2 | wgpu 渲染器稳定化；多窗口支持。 |
| 2025-12 | **Tabby** | 1.0 | 基于 Electron；长期 beta；首个稳定版。 |

## 趋势

### GPU 渲染成为默认

每个值得注意的新终端都自带 GPU 加速渲染：Ghostty 在 macOS 上用 Metal；WezTerm 用 wgpu（Vulkan / Metal / DX12 / OpenGL）；Kitty 用 OpenGL；Alacritty 用 OpenGL ES 2.0；Rio 用 wgpu。CPU 渲染已成过去 —— GNOME Terminal 与 xterm 是仅存的主要坚守者。

### 图像协议趋于稳定

三种图像协议并存：**iTerm2**（基于转义序列）、**Kitty 图形协议**（基于转义序列，Kitty 原生）、**Sixel**（DEC VT340 风格，最老）。大多数终端至少实现一种；许多实现两到三种。网站有关于取舍的独立深度文章。

### 配置格式远离 JSON

TOML 成为新默认：Alacritty（自 0.12 起）、Rio。WezTerm 选择 Lua 以获得可编程性。JSON（Tabby、Windows Terminal）可以接受但冗长。整个领域正向允许注释与内联文档的格式迁移。

### "终端复用是别人的事"

Alacritty 的设计哲学 —— *只做一件事，搭配 tmux 或 zellij* —— 已成为主流。Ghostty、WezTerm、Kitty 都内置一些复用功能，但每个都推荐 tmux / 自家会话管理器作为主力。

## 值得关注的破坏性变更

- **Alacritty 0.13** 将配置从 YAML 改为 TOML。现有 `alacritty.yml` 需要重命名并重新格式化。
- **WezTerm 2026.x** 改变了默认 Lua 配置布局；旧的 `.wezterm.lua` 需要更新模块导入。
- **Ghostty 1.0** 移除了 GTK3 兜底；Linux 现在要求 GTK4 / libadwaita。
- **Kitty 0.42+** 要求 OpenGL 3.3（旧为 3.0）。一些较老的虚拟化 GPU 需要更新驱动。

## 值得关注的动向

- **Alacritty 0.14** — 2026 年末的主要发布候选，重构渲染器并改进 scrollback。
- **Ghostty 多窗口 / 标签页** — Ghostty issue tracker 中被请求最多的功能。1.0 中没有；目标 1.2。
- **统一的 Sixel 方案** — 提议一种统一图像协议来替代 iTerm2 + Kitty + Sixel。早期阶段；尚未发布。

## 相关

- [`docs/1-terminal-overview.cn.md`](./1-terminal-overview.cn.md) — 概览与对比表。
- [`docs/2-alacritty.cn.md`](./2-alacritty.cn.md) — Alacritty 深度介绍。