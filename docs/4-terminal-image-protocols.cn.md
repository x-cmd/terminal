---
x-title: 终端图像协议 — Sixel、iTerm2 与 Kitty Graphics
x-desc: >-
  三种终端图像协议的并排对比 —— Sixel（DEC VT340）、iTerm2 图像协议、
  Kitty graphics 协议 —— 各自的历史、编码、各终端的能力矩阵、以及使用它们
  的实用工具（带图像预览的 ls、图像查看器、图表）。
x-sidebar: 终端图像协议
x-keywords: 终端图像协议, sixel, iterm2 image, kitty graphics, ls --icon, viu
x-json-ld:
  '@context': https://schema.org
  '@graph':
    - '@type': TechArticle
      headline: '终端图像协议'
      inLanguage: 'cn'
      about: '终端图像协议对比'
---

# 终端图像协议 — Sixel、iTerm2 与 Kitty Graphics

三种协议允许程序**直接在终端渲染图像**：**Sixel**、**iTerm2 image**、
**Kitty graphics**。每个有不同的历史、编码与支持矩阵。大多数现代终端
都实现了三种。

本页是角度文章——图像协议对比，而非 per-tool 教程。安装 / 配置见
[x-cmd.com/install/<terminal>](https://x-cmd.com/install/)。

> **TL;DR。** Sixel（1980 年代）仅数据，像素级。iTerm2 image
> （2009）仅数据，帧级。Kitty graphics（2018）仅数据或基于路径
> （路径模式是危险的那个——拒绝 CWD 外）。大多数现代终端都实现三
> 种。用 **ls --icon / eza --icons / yazi** 做文件管理器预览；用
> **viu / chafa / imgcat** 看图；用 **Klipper / lsix** 处理遗留
> Sixel。

## 三种协议

### Sixel（DEC VT340，1980 年代）

Sixel 是**最老**的。它是 DEC VT340 终端（1983）的一部分。
编码：每字符 6 位（A-P 对应值 0-15；特殊字符用于换行、重复、帧结束）。

```sixel
\ePq
#0;2;0;0;0#1;2;100;100;0#2;2;0;100;0
~~CDDDDDDDD`????????
\e\\
```

每个字符编码 6 个竖直像素。整个图像作为 Sixel 字符流发送。

优点：

- 仅数据——无路径模式，无安全风险。
- 在支持 Sixel 的任何终端都能用（今天大多数都支持）。

缺点：

- 颜色有限（原本 16 色；现代终端通过调色板扩展支持更多）。
- 像素级（无图像压缩）。
- 大图像慢（流很大）。

### iTerm2 image protocol（2009）

由 iTerm2 设计来解决 Sixel 的慢。编码：**base64 编码的 PNG /
JPEG**，作为单个 OSC 序列发送，带分块传输模式。

```
\e]1337;File=name=<base64>;size=<bytes>;width=<px>;height=<px>;preserveAspectRatio=1:<base64-chunk>\e\\
```

现代终端（Kitty、WezTerm、Ghostty、Konsole）实现了 iTerm2
图像协议。

优点：

- 原生支持 PNG / JPEG（压缩）。
- 帧级——动画快。
- 广泛支持。

缺点：

- 仅数据——标准中无路径模式。
- 一些终端（Kitty）偏好自己的 graphics 协议；iTerm2 image
  是后备。

### Kitty graphics protocol（2018）

由 Kovid Goyal（Kitty 作者）设计。编码：**多命令协议**带明确
传输模式。

模式：

- **直接数据** —— 协议中的 base64 编码图像数据（类似
  iTerm2）。
- **文件路径** —— 协议指向文件路径；终端读取文件。
- **共享内存** —— 通过进程与终端之间的共享内存实现高吞吐。

```
\e_Gi=1;f=100;t=PNG;s=<size>;v=<height>;a=T,C,U<base64-chunk>\e\\
\e_Ga=m,f=100,t=PNG,s=<size>;...<next-chunk>\e\\
\e_Ga=quit\e\\
```

优点：

- 三者中功能最丰富。
- 支持动画、位置控制、虚拟位置、传输模式。
- 共享内存模式用于高吞吐。

缺点：

- **路径模式**是安全风险——恶意路径可以读 `/etc/passwd`。
  由 Kitty 的 safe-mode 默认缓解。

## 能力矩阵（2026）

| 终端 | Sixel | iTerm2 image | Kitty graphics | 备注 |
| --- | --- | --- | --- | --- |
| **Alacritty** | ✓（自 0.13 起） | ⚠️（第三方） | ❌ | 有限；Alacritty 哲学是极简。 |
| **Kitty** | ✓ | ✓ | ✓（原生） | 三者都支持。 |
| **WezTerm** | ✓ | ✓ | ⚠️（部分） | 两个主要协议。 |
| **Ghostty** | ✓（自 1.0） | ⚠️（第三方） | ❌ | 1.0 仅 Sixel；iTerm2 / Kitty 计划中。 |
| **iTerm2** | ✓ | ✓（原生） | ❌ | iTerm2 image 是 iTerm2 原生。 |
| **Windows Terminal** | ❌ | ❌ | ❌ | 无图像协议。 |
| **GNOME Terminal** | ✓ | ⚠️（第三方） | ❌ | 仅 Sixel。 |
| **foot** | ✓（自 0.4） | ❌ | ❌ | 仅 Sixel。 |
| **st** | ❌ | ❌ | ❌ | Suckless 极简。 |
| **Konsole** | ✓ | ❌ | ❌ | 仅 Sixel。 |

✓ = 支持；⚠️ = 第三方或部分；❌ = 不支持。

## 实用工具

### 带图像预览的文件管理器

```
ls --icons                  # 现代 ls 替代
eza --icons                 # eza 是流行的 ls 替代
yazi                        # 带图像预览的终端文件管理器
broot                       # 树视图（无图像）
```

yazi 用 iTerm2 / Sixel / Kitty graphics 做图像预览。图像丰富
工作流强烈推荐。

### 图像查看器

```
viu                         # 小型 Rust 图像查看器（iTerm2 / Kitty / Sixel）
chafa                       # 更灵活的图像到终端转换器
imgcat                      # macOS / iTerm2 经典
lsix                        # 仅 Sixel 图像查看器（较老）
```

`viu` 和 `chafa` 是现代选择。它们支持多种协议并处理 PNG /
JPEG / GIF。

### 图表与图形

```
chart-image                 # graphviz → 终端图像
go-echarts                  # Go 图表 → 终端
spark                       # sparkline 风格图表
```

用于终端的数据可视化。

## 选协议

大多数情况下，**在你的终端配置里启用三种**。每个程序选它支持的。
Kitty 支持三种，是最安全的默认。

遗留代码或仅需 Sixel 的脚本，**Alacritty、WezTerm、Ghostty、foot、
GNOME Terminal、Konsole** 都支持 Sixel。

动画与高吞吐，**Kitty graphics** 最好（带共享内存模式追求性能）。

只要图（无动画），**iTerm2 image** 不错且广泛支持。

## 安全：哪个协议最安全？

- **Sixel** —— 纯数据。安全。
- **iTerm2 image** —— 纯数据。安全。
- **Kitty graphics** —— 除数据模式外还支持**路径模式**。路径模式
  是安全风险——终端应该默认拒绝 CWD 外的路径。

缓解：

- **Kitty 的 `safe_image_protocol` 默认开启**——拒绝 CWD 外的路径。
- **在敏感工作流禁用图像协议**。
- **审计打印图像路径的 AI 工具**。

## 下一步？

- **5-terminal-multiplexer** —— tmux vs zellij vs 内置多路复用器
  策略。

## 相关

- [Kitty graphics 协议规范](https://sw.kovidgoyal.net/kitty/protocol.html)
- [iTerm2 图像协议](https://iterm2.com/documentation-images.html)
- [Sixel 参考](https://en.wikipedia.org/wiki/Sixel)
- [yazi 文件管理器](https://github.com/sxyazi/yazi)
- [viu 图像查看器](https://github.com/atanunq/viu)

> per-tool 安装 / 配置：`x-cmd.com/install/<terminal>` 或
> `x-cmd.com/doc-2026/install/<terminal>` 的 per-tool 文章。本页
> 仅为角度 / 分析。