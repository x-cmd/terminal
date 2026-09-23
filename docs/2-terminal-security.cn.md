---
x-title: 终端模拟器安全 — 攻击面、沙箱化与隔离
x-desc: >-
  终端模拟器的安全对比 —— 转义序列注入、逃逸到 shell、OSC 信任边界、
  屏幕打印到主机。Alacritty、Kitty、WezTerm、Ghostty、iTerm2、Windows
  Terminal、foot 中内建的实用缓解措施。
x-sidebar: 终端安全
x-keywords: 终端安全, 转义序列注入, osc 信任, 逃逸到 shell, 沙箱化, 隔离
x-json-ld:
  '@context': https://schema.org
  '@graph':
    - '@type': TechArticle
      headline: '终端模拟器安全'
      inLanguage: 'cn'
      about: '终端模拟器安全分析'
---

# 终端模拟器安全 — 攻击面、沙箱化与隔离

终端模拟器是大多数服务器上最受信任的程序。它接收**任意不受信文本**
（来自网络日志、AI 工具输出、用户聊天、在 iTerm2 内嵌预览中渲染的网页
等），并被期待如实打印文本——但绝不能**执行**该文本。

本页是角度文章——安全分析，而非 per-tool 教程。安装 / 配置见
[x-cmd.com/install/<terminal>](https://x-cmd.com/install/)。

> **TL;DR。** 每个支持 OSC 52、OSC 7、OSC 8、OSC 133、超链接或
> 内嵌图像的终端模拟器，在终端输出与主机之间都存在**信任边界**。
> 五个值得知道的攻击：（1）转义序列注入，（2）逃逸到 shell，
> （3）OSC 52 剪贴板劫持，（4）OSC 8 超链接欺骗，（5）内嵌图像 /
> 文件泄露。缓解措施随 secure_input 模式、OSC 过滤、沙箱化图像解码器
> 与 prompt 标记一同发出——覆盖范围在终端间差异很大。

## 五个攻击面

### 1. 转义序列注入

终端解释 **ANSI/VT 转义序列**来做光标移动（`\e[2A`）、改色（`\e[31m`）、
画进度条（`\e[?25l`）等事。其中大部分是无害的——但有一小部分是**信任边界
输入**。

如果你的终端盲目信任打印的**任何东西**（网络日志、AI 输出、粘贴的
代码），攻击者可以：

- 移动光标并覆盖你的屏幕（视觉混淆）。
- 重置你的终端（`\ec`）—— 烦人但通常无害。
- 操纵 scrollback。
- 通过反视频技巧隐藏输入。

### 2. 逃逸到 shell（危险的）

逃逸到 shell 攻击：终端发出一个序列，在**GUI 点击或特定输入**时触发
**外部命令**。变种：

- **OSC 9 / OSC 777** —— "conEmu" / iTerm2 的旧 `OSC 9;1;command ST`
  在主机上跑 shell 命令。**大多数现代终端移除**，但仍值得检查。
- **OSC 1337** —— KDE Konsole 的 `OSC 1337;...ST` "点击运行" —— **现代
  Konsole 移除**。
- **OSC 8 超链接** —— 可见 URL ≠ 执行 URL；设计上安全，但可用于**钓鱼
  攻击**（链接文字 `https://example.com`，实际指向 `https://attacker.tld`）。
- **Shell Esc 键码** —— iTerm2 曾有跑 shell 命令的 `Esc` 序列；**自
  2017 年起移除**。

**iTerm2 的 `OSC 9;1;command ST`** 是历史上被引用最多的逃逸到 shell
bug。在 iTerm2 3.4（2017）中移除。大多数现代终端默认屏蔽 OSC 9。

### 3. OSC 52 剪贴板劫持

OSC 52 是 "set clipboard"：`OSC 52;c;<base64-data> ST`。如果你的终端
支持 OSC 52，攻击者可以：

- **读取你的剪贴板**（`OSC 52;c;? ST` 以 base64 返回当前剪贴板）。
- **用任意内容覆盖你的剪贴板**。

大多数现代终端默认**不**在用户同意下 OSC 52 覆盖。Apple Terminal 静默
丢弃 OSC 52 读 / 写。iTerm2 允许禁用该规则。

风险：LLM 工具打印包含 `OSC 52;c;...ST` 的文本。如果你的终端尊重它，
你的剪贴板被覆盖。

### 4. OSC 8 超链接欺骗

OSC 8 允许程序把 URL 附加到文本：

```
\e]8;;https://example.com\e\\点击这里\e]8;;\e\\
```

可见文字可能写 `https://example.com`，而**实际目标**是 `https://attacker.tld`。
大多数终端默认不在 hover 时显示 URL——使这成为**钓鱼向量**。

缓解：

- **按住 Ctrl / Cmd 检查** —— 大多数终端在 Ctrl-hover 时显示 URL。
- **使用 `show-url` 命令** —— WezTerm、Kitty、Ghostty 都支持按需显示
  URL。
- **禁用 OSC 8** —— 可行但会失去功能。

### 5. 内嵌图像 / 文件泄露

Sixel / iTerm2 / Kitty graphics 协议允许程序**直接在终端渲染图像**。
图像数据可以内联（安全），也可以是**路径**——而恶意的图像路径可以是：

- 用户预期目录之外的路径（`/etc/passwd`）。
- URL（`http://attacker.tld/image.png` → 泄露 IP）。
- 奇怪的协议（`file://`、`data:`）。

终端图像协议实现各异：

- **Kitty graphics 协议** —— 支持包含路径的**传输模式**；终端**应该**
  默认拒绝 CWD 外的路径。Kitty 这样做。
- **iTerm2 图像协议** —— 图像数据是内联的；默认**无路径**模式。iTerm2
  默认安全。
- **Sixel** —— 纯数据，无路径模式。安全。

缓解：

- **禁用内嵌图像**（如果你不需要它们）。Kitty、WezTerm、Ghostty 都有
  开关。
- **启用 `safe_image` 模式**（Kitty 拒绝 CWD 外的路径）。
- **审计你的 AI 工具输出**再渲染内嵌图像。

## 每个主流终端对此做了什么

2026 年审计：

| 终端 | OSC 9 (shell) | OSC 52 (剪贴板) | OSC 8 (超链接) | 内嵌图像 | Prompt 标记 |
| --- | --- | --- | --- | --- | --- |
| **Alacritty** | 屏蔽（默认） | 默认关；可选开 | 支持；hover 揭示 | 禁用（自 0.13 起 Sixel） | 是（最后命令检测） |
| **Kitty** | 屏蔽 | 默认关；可选开 | 支持；点击揭示 | 支持；safe-mode 默认（拒绝 CWD 外路径） | 是 |
| **WezTerm** | 屏蔽 | 默认关；可选开 | 支持；URL 揭示 | 支持（iTerm2 / Sixel）；通过 `imgcat` 沙箱 | 是 |
| **Ghostty** | 屏蔽（自 1.0） | 默认关 | 支持；Cmd-hover 揭示 | 自 1.0 起 Sixel | 是（通过 OSC 133） |
| **iTerm2** | 屏蔽（自 3.4，2017） | 默认关；可选开 | 支持；Cmd-hover 揭示 | 支持（iTerm2 图像协议；仅数据） | 是（通过 OSC 133） |
| **Windows Terminal** | N/A（Windows 控制台） | 关 | 支持 | 关 | 禁用 |
| **GNOME Terminal** | N/A | 关 | 支持 | 禁用 | 禁用 |
| **foot** | 屏蔽 | 关 | 支持 | 禁用（自 0.4 起 Sixel） | 有限 |
| **st** | 屏蔽（默认） | 关 | 禁用 | 禁用 | 禁用 |

来源：各终端 changelog 与源码。

## Secure-input 模式

一些终端支持**secure-input 模式**，在用户输入时禁用转义序列解释。粘贴
敏感数据（密码、密钥）时有用。

- **Alacritty** —— `secure_input_via_clipboard` 设置在剪贴板粘贴内容时
  禁用转义解析。
- **Kitty** —— `--override secure_input_mode=on`（或自动检测 "type
  password" 提示）。
- **WezTerm** —— 提示看起来像密码提示时自动 secure-input。
- **iTerm2** —— 类似自动检测。

OSC 133 / "Final Term" 风格的 shell 集成**在 scrollback 中标记每个
命令的开始与结束**，让终端知道何时信任输出 vs 何时将输出视为"潜在的
prompt 注入"：

- Alacritty 有 `shell` 集成标记 prompt 边界。
- Kitty 在新版本中默认启用 `shell_integration`。
- WezTerm 有 `ShellIntegration` Lua API。
- iTerm2 多年支持 shell 集成。
- Ghostty 在 1.0 加入 OSC 133 支持。
- Windows Terminal 尚无 OSC 133 支持。

如果你的终端 + shell 组合支持 shell 集成，**启用它**——它是最有效的
单一缓解。

## 实用缓解

速查清单：

1. **明确禁用 OSC 9 / OSC 1337** —— 大多数终端已经做了，但要确认。
2. **禁用 OSC 52 读 / 写**，除非你需要。
3. **验证 OSC 8 hover 行为** —— Cmd-hover 或 Ctrl-hover 应该揭示真实
   URL。
4. **对敏感工作流禁用内嵌图像**（`x env use kitty --no-images` 风格
   开关）。
5. **启用 shell 集成**（OSC 133）以检测 secure-input。
6. **审计你的 AI 工具** —— 把粘贴输出流（CI 日志、聊天回复）送入忽略
   OSC 9 的终端。
7. **不要 `cat` 不受信任的文件**到支持 OSC 52 但未禁用的终端。
8. **对高风险命令使用沙箱 shell**（bwrap、firejail、Docker）。

## 什么**不是**终端安全风险

一些常见误解：

- **反视频** —— 既有；视觉无害。
- **退格字符** —— 既有；无害。
- **ANSI 颜色** —— 纯样式。
- **光标定位** —— 视觉；不能执行。
- **响铃字符** —— 烦人但安全。

风险是**终端与主机之间的信任边界** —— 任何跨越它的东西是威胁模型。

## 下一步？

- **3-terminal-performance** —— 性能 / 资源 / GPU vs CPU 角度。
- **4-terminal-image-protocols** —— 图像协议取舍深度。
- **5-terminal-multiplexer** —— tmux vs zellij vs 内置多路复用器策略。

## 相关

- [iTerm2 OSC 9 移除](https://iterm2.com/news.html) —— iTerm2
  修复背景。
- [Kitty graphics 协议](https://sw.kovidgoyal.net/kitty/protocol.html) —— 完整协议规范。
- [Ghostty 安全模型](https://ghostty.org/docs) —— Ghostty 的威胁模型。

> per-tool 安装 / 配置：`x-cmd.com/install/<terminal>` 或
> `x-cmd.com/doc-2026/install/<terminal>` 的 per-tool 文章。本页
> 仅为角度 / 分析。