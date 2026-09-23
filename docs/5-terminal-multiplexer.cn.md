---
x-title: 终端多路复用器策略 — tmux vs zellij vs 内置
x-desc: >-
  终端多路复用器的实用角度文章 —— tmux（经典）、zellij（现代 Rust 替代），
  以及 Kitty / WezTerm / Ghostty / iTerm2 / Windows Terminal 的内置多路复用器。
  何时选外部 vs 内置，持久化，会话共享，脚本化。
x-sidebar: 终端多路复用器
x-keywords: 终端多路复用器, tmux, zellij, wezterm-mux-server, kitty-session, ghostty-session
x-json-ld:
  '@context': https://schema.org
  '@graph':
    - '@type': TechArticle
      headline: '终端多路复用器策略'
      inLanguage: 'cn'
      about: '终端多路复用器分析'
---

# 终端多路复用器策略 — tmux vs zellij vs 内置

终端**多路复用器**让一个窗口管理多个会话——面板、标签、断开后仍存活的
持久连接。2026 年有三种风格：

1. **外部多路复用器**（tmux、zellij、screen）—— 跑在终端模拟器*里面*。
2. **内置多路复用器**（Kitty、WezTerm、iTerm2、Windows Terminal）——
   终端本身的一部分。
3. **无多路复用器**（Alacritty、Ghostty 1.0、foot、st、没扩展的 GNOME
   Terminal）—— 搭配 tmux 使用。

本页是角度文章——策略对比，而非 per-tool 教程。安装 / 配置见
[x-cmd.com/install/<tool>](https://x-cmd.com/install/)。

> **TL;DR。** 大多数开发工作流适合**内置多路复用器**（Kitty /
> WezTerm / iTerm2）加上**tmux**（需要持久化或会话共享时）。**zellij**
> 是 tmux 的现代替代，UX 默认更友好。**Alacritty + tmux** 是经典
> Unix 组合。

## 三种风格

### 外部多路复用器

外部多路复用器作为独立进程跑在终端模拟器*里面*。

```
┌──────────────────────────────────┐
│ Alacritty（终端模拟器）          │
│  ┌────────────────────────────┐ │
│  │ tmux（多路复用器）          │ │
│  │  ┌────┐ ┌────┐ ┌────┐      │ │
│  │  │面板│ │面板│ │面板│      │ │
│  │  └────┘ └────┘ └────┘      │ │
│  └────────────────────────────┘ │
└──────────────────────────────────┘
```

**tmux** —— 经典（2007 起）。配置在 `~/.tmux.conf`。插件生态（tpm、
tmux-resurrect、tmux-continuum）。持久会话跨断开连接。脚本化用
`tmux send-keys`。学习曲线陡。

**zellij** —— 现代 Rust 替代（2021 起）。UX 默认更好——可发现的键绑定、
默认状态栏、layouts。配置在 YAML。插件系统用 WASM 模块。生态比 tmux
年轻。

**screen** —— 更老，今天少见。

### 内置多路复用器

现代终端自带多路复用器。

```
┌──────────────────────────────────┐
│ WezTerm（终端 + 多路复用器）     │
│  ┌────┐ ┌────┐ ┌────┐             │
│  │面板│ │面板│ │面板│             │
│  └────┘ └────┘ └────┘             │
│  （无独立进程）                    │
└──────────────────────────────────┘
```

- **Kitty** —— `kitty @ launch` + `Ctrl+Shift+Enter` 分屏。配置
  在 `kitty.conf`。
- **WezTerm** —— `wezterm-mux-server`（Lua 可脚本化的多路复用器）。
  配置在 `wezterm.lua`。
- **iTerm2** —— 内置面板、标签、分屏。原生 macOS 体验。
- **Windows Terminal** —— 内置面板 + 标签。
- **Konsole** —— 内置分屏 + 标签。

### 无多路复用器

故意不带多路复用器的终端：

- **Alacritty** —— 设计如此（搭配 tmux）。
- **Ghostty 1.0** —— 尚无标签（目标 1.2）。
- **foot、st、GNOME Terminal** —— 搭配 tmux。

## 对比矩阵

| 工具 | 类型 | 持久化 | 跨机器 | 脚本化 | 生态 | 最佳场景 |
| --- | --- | --- | --- | --- | --- | --- |
| **tmux** | 外部 | ✅ 是 | ⚠️ 需要配置 | `tmux send-keys` 等 | 成熟（tpm、插件） | 持久会话、服务器工作 |
| **zellij** | 外部 | ✅ 是 | ⚠️ 需要配置 | `zellij action` 等 | 增长中 | 现代 tmux 替代 |
| **screen** | 外部 | ✅ 是 | ⚠️ 需要配置 | `screen -X` | 老 | 遗留 |
| **Kitty** | 内置 | ❌（关闭即失） | ❌ | `kitty @` | Kitty 生态 | 轻量 + 图像协议 |
| **WezTerm** | 内置 | ✅ 是（通过 Lua） | ✅ 通过 `wezterm connect ssh://` | Lua | Lua | 多路复用 + 脚本一体 |
| **iTerm2** | 内置 | ⚠️ 有限 | ❌ | AppleScript | 仅 macOS | macOS 用户 |
| **Windows Terminal** | 内置 | ⚠️ 有限 | ❌ | 有限 | 仅 Windows | Windows 用户 |
| **Konsole** | 内置 | ❌ | ❌ | 有限 | KDE | KDE 用户 |
| **Ghostty（1.0）** | 无 | ❌ | ❌ | ❌ | 原生 UI | 原生 UX + tmux 做分屏 |

## 何时选哪个

### 何时选外部（tmux / zellij）：

- 你想要断开连接后仍存活的**持久会话**（SSH、笔记本休眠）。
- 你做**服务器工作**——tmux 是 Linux 服务器上的通用语。
- 你需要**会话共享**——结对编程、演示。
- 你想要**成熟插件生态**（tmux-resurrect、tmux-continuum、tpm）。
- 你搭配**Alacritty / foot / st**（无内置多路复用器）。

### 何时选内置（Kitty / WezTerm / iTerm2 / Windows Terminal / Konsole）：

- 你大部分工作**本地**完成。
- 你想要**多路复用器 + 终端一个二进制**（无额外进程要管）。
- 你想要**Lua / AppleScript / 原生配置**而非 `.tmux.conf`。
- 你想要**图像协议 + 多路复用器一起**（Kitty）。

### 何时选混合（内置 + tmux）：

- 你用**Alacritty / foot / Ghostty**（无内置多路复用器）。
- 你想要**持久会话 + 快速本地面板**两者。
- Alacritty + tmux 组合是经典的。

### 何时选 zellij：

- 你是多路复用器新手（zellij 拥有更好的 UX 默认与可发现键绑定）。
- 你想要**现代脚本模型**（WASM 插件）。
- 你还没锁定 tmux 肌肉记忆。

## 实用配方

### 服务器上的 tmux

```sh
# SSH 进入服务器，启动 tmux
ssh user@server
tmux new -s work

# 断开 → 之后重连
ssh user@server
tmux attach -t work
```

会话活在服务器上。断开连接不会丢失。

### Alacritty + tmux（经典组合）

```sh
alacritty  # 终端
tmux       # 里面的多路复用器
```

Alacritty 极简；tmux 做其他一切。

### WezTerm 内置多路复用器

```lua
-- wezterm.lua
mux_enable_default_keybindings = true

local wezterm = require 'wezterm'
local act = wezterm.action

return {
  keys = {
    { key = '|', mods = 'CTRL|SHIFT', action = act.SplitHorizontal { domain = 'CurrentPaneDomain' } },
    { key = '_', mods = 'CTRL|SHIFT', action = act.SplitVertical { domain = 'CurrentPaneDomain' } },
    { key = 'h', mods = 'CTRL|SHIFT', action = act.ActivatePaneDirection 'Left' },
  },
}
```

纯 WezTerm；无需外部多路复用器。

### zellij（现代）

```sh
zellij --layout default  # 用默认 layout 启动
```

状态栏显示键绑定。无需 `.tmux.conf` 即可发现。

### 会话共享（结对编程）

```sh
# 服务端：创建共享会话
tmux new -s pair

# 每个参与者：attach
ssh user@server -t 'tmux attach -t pair'
```

多个用户实时看到相同面板。

## 常见陷阱

1. **tmux 没有 `reattach-to-user-namespace`** —— 在 macOS 上破坏剪贴板。
   用 `brew install reattach-to-user-namespace` 修复，`set -g default-
   command "reattach-to-user-namespace -l zsh"`。
2. **tmux scrollback** —— `Ctrl-b [` 进入 copy mode；`q` 退出。zellij
   用类似 `Ctrl-o`。
3. **断开连接时会话丢失** —— 内置多路复用器（Kitty、iTerm2、Windows
   Terminal）**不**跨断开持久化；tmux / zellij 持久化。
4. **跨平台** —— tmux 是 Linux / macOS；zellij 是 Linux / macOS /
   Windows；Kitty / WezTerm / Ghostty 全部跨平台。
5. **配色方案** —— tmux 配 256 色终端：`set -g default-terminal
   "screen-256color"`。WezTerm / Kitty：自动处理。

## 什么**不是**多路复用器

- **OS 窗口管理器分屏** —— i3 / Sway / Aerospace / Rectangle
  —— 这些管窗口，不管终端。
- **编辑器分屏** —— Vim / Emacs / VS Code —— 在 buffer / 面板中分，
  不在终端会话中。
- **浏览器 shell** —— VS Code 的终端 —— 嵌在编辑器里，不是独立
  多路复用器。

## 下一步？

- `0-latest-terminals` / `1-terminal-overview` 中的交叉引用。
- per-tool 安装见 `x-cmd.com/install/<tool>`。

## 相关

- [tmux 主页](https://github.com/tmux/tmux)
- [zellij 主页](https://github.com/zellij-org/zellij)
- [WezTerm 多路复用器文档](https://wezfurlong.org/wezterm/config/lua/mux.html)
- [Kitty 多路复用器](https://sw.kovidgoyal.net/kitty/overview.html#sessions)
- [Alacritty + tmux 组合](https://alacritty.org/)

> per-tool 安装 / 配置：`x-cmd.com/install/<tool>` 或
> `x-cmd.com/doc-2026/install/<tool>` 的 per-tool 文章。本页
> 仅为角度 / 分析。