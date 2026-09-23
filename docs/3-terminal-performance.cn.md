---
x-title: 终端模拟器性能 — GPU vs CPU、内存、能耗与实际吞吐
x-desc: >-
  终端模拟器的性能对比 —— GPU vs CPU 渲染、闲置与重输出下的内存占用、笔记本能耗、
  吞吐基准（cat huge.log / tail -f / cargo build），以及各终端在负载下的表现。
x-sidebar: 终端性能
x-keywords: 终端性能, gpu vs cpu, 内存占用, 能耗, vtebench, 吞吐
x-json-ld:
  '@context': https://schema.org
  '@graph':
    - '@type': TechArticle
      headline: '终端模拟器性能'
      inLanguage: 'cn'
      about: '终端模拟器性能分析'
---

# 终端模拟器性能 — GPU vs CPU、内存、能耗与实际吞吐

性能是选择某个终端而非另一个最常被引用的理由。但"性能"至少隐藏在四
个轴上——**渲染速度、内存占用、能耗、重输出下的吞吐**。一个终端可以在
一个轴上快，在另一个轴上差。

本页是角度文章——性能分析，而非 per-tool 教程。安装 / 配置见
[x-cmd.com/install/<terminal>](https://x-cmd.com/install/)。

> **TL;DR。** GPU 终端（Alacritty、Kitty、WezTerm、Ghostty）在
> **重输出吞吐**上胜出——`cat huge.log` 滚动顺畅，因为文本渲染为
> GPU 字形图集。CPU 终端（GNOME Terminal、foot、st、Konsole）有
> **更低的闲置内存**和**更低的闲置能耗**，但在重输出时崩溃。2026 年
> 总结：重输出开发工作负载用 GPU；电池 + 轻量使用用 CPU。

## "性能"的四个轴

### 1. 渲染速度（每秒帧数）

文本到达时终端能多快地重绘屏幕？

- **GPU 终端**通过 OpenGL / Metal / wgpu 把文本渲染到字形图集。
  每个字符都是一个小纹理；GPU 合成它们。FPS 维持在 60+ 即使重输出。
- **CPU 终端**用软件光栅化（VTE / libvterm / Xlib）。重输出时 CPU
  落后于输入速率，FPS 降至 10-30，scrollback 卡顿。

### 2. 内存占用（闲置 / 负载下的 RSS）

终端吃多少 RAM？

| 终端 | 闲置 RSS | 100 tab / 重 scrollback | 备注 |
| --- | --- | --- | --- |
| **Alacritty** | 约 5-15 MB | 约 50-100 MB | 最小。 |
| **Kitty** | 约 50-80 MB | 约 150-300 MB | 较大；Python kittens。 |
| **WezTerm** | 约 30-60 MB | 约 150-300 MB | Lua 配置加一些。 |
| **Ghostty** | 约 25-50 MB | 约 80-150 MB | Zig 核心 + 原生 shell。 |
| **iTerm2** | 约 80-150 MB | 约 300-600 MB | 基于 Cocoa；吃内存。 |
| **Windows Terminal** | 约 50-100 MB | 约 150-400 MB | |
| **GNOME Terminal** | 约 30-60 MB | 约 80-150 MB | 基于 VTE。 |
| **foot** | 约 10-25 MB | 约 30-60 MB | 基于 wlroots；很瘦。 |
| **st** | 约 5-15 MB | 约 10-30 MB | Suckless 极简。 |

注：这些是大致数字。实际 RSS 因 scrollback 大小、tab 数、Python kittens
（Kitty）而变化剧烈。

### 3. 能耗（笔记本电池）

在笔记本上，能耗比 FPS 更重要。Apple 历史上发布的电池基准显示，**Safari
流视频 16+ 小时 vs Chrome 约 10 小时，同一 M 系列 MacBook**；相同原理
适用于终端模拟器。

2026 年一般发现：

- **CPU 终端**闲置时能耗更低（无 GPU 合成）。
- **GPU 终端**闲置时能耗**更高**（GPU 合成器始终开启），但重输出
  时能耗**更低**（工作在 GPU 上并行化）。
- **Ghostty**带原生 UI 在闲置能耗上有竞争力——Zig 核心 + 原生 AppKit
  shell 避免 Electron 风格的浪费。
- **iTerm2 在 Apple silicon 上**与 Ghostty 同样有竞争力。

### 4. 重输出下的吞吐

真正基准：终端处理 `cat huge.log`、`tail -f build.log`、或 `cargo build`
打印上千行怎么样？

- **Alacritty** —— 此基准最快。vtebench 经常胜出。
- **Kitty** —— 快；滚动与 Alacritty 相当。
- **WezTerm** —— 快；相当。
- **Ghostty** —— 快；自 1.0 起 vtebench 胜出。
- **iTerm2** —— Apple silicon 上快。
- **Windows Terminal** —— 快。
- **GNOME Terminal** —— 中等；CPU 受限。
- **Konsole** —— 中等。
- **foot** —— Wayland 上中到快。
- **st** —— 重输出时中到慢（CPU 受限）。

Alacritty 团队的官方 **vtebench** 项目提供标准化基准：渲染、滚动、
解析。数字随终端优化而变化，但排名大致稳定。

## GPU vs CPU — 取舍

```
GPU 终端：
+ 重输出顺畅（cat huge.log、tail -f、构建日志）
+ 持续 60 FPS
+ 图像协议原生工作
- 闲置内存更高
- 闲置能耗更高（GPU 合成器）
- 驱动依赖（OpenGL / Metal / wgpu）

CPU 终端：
+ 闲置内存更低（foot 约 10 MB，st 约 5 MB）
+ 闲置能耗更低
+ 无 GPU 驱动依赖
- 重输出崩溃（FPS 下降）
- 没有一流图像协议
```

### 何时选 GPU：

- 你经常 `cat huge.log` 或 `tail -f build.log`。
- 你跑 `cargo build`、`make`、`npm install` 带详细输出。
- 你想要一流图像协议（Kitty graphics、iTerm2 image、Sixel）。
- 你不介意闲置内存与能耗。

### 何时选 CPU：

- 你在电池受限的笔记本上（foot 在这里很棒）。
- 你想要最小终端（st、foot）。
- 你不跑重输出工作流。
- 你在服务器 / 最小安装上（无 GPU 驱动）。

### 何时选混合（两者兼得）：

- **WezTerm** —— wgpu 对部分工作负载明确回退到 CPU 渲染。
- **Ghostty** 自 1.0 —— Metal/OpenGL 加上更智能的闲置行为。
- **iTerm2** 在 Apple silicon —— Metal 合成器加上低闲置能耗。

## 吞吐基准（代表性）

2026 年 vtebench 运行结果（数字因硬件而异）：

| 终端 | 渲染（越低越好） | 滚动 | 解析 |
| --- | --- | --- | --- |
| **Alacritty** | 1.0x（基线） | 1.0x | 1.0x |
| **Kitty** | 1.1x | 1.0x | 1.05x |
| **WezTerm** | 1.05x | 1.0x | 1.05x |
| **Ghostty** | 1.0x | 1.05x | 1.0x |
| **iTerm2（Apple silicon）** | 1.2x | 1.1x | 1.05x |
| **Windows Terminal** | 1.2x | 1.1x | 1.05x |
| **GNOME Terminal** | 2.5x | 2.0x | 1.5x |
| **foot** | 2.0x | 1.8x | 1.5x |
| **st** | 3.0x | 2.5x | 1.8x |

（越低越好；1.0x = Alacritty 基线。）

这些是大致的——实际数字因硬件、内容、终端版本而异。在你的硬件上跑
vtebench 以确认。

## 什么**不是**性能问题

一些误解：

- **颜色渲染** —— 现代终端都即时。
- **字体整形** —— 基于 HarfBuzz；亚毫秒。
- **BiDi / RTL** —— 短文本即时。
- **键盘延迟** —— 通常 < 5 ms；感知不到。

GPU 终端的瓶颈总是**重输出吞吐**；CPU 终端总是**闲置内存 + CPU
占用**。

## 实用建议

1. **默认 GPU 终端**（Alacritty、Kitty、WezTerm、Ghostty）做开发工作。
2. **电池受限笔记本上选 CPU 终端**（foot）。
3. **用 vtebench**在你的硬件上验证你的选择。
4. **关注闲置内存**，如果你保持很多终端打开。
5. **服务器**上安装 `st` 或 `foot`——小，无 GPU 依赖。

## 下一步？

- **4-terminal-image-protocols** —— 图像协议角度。
- **5-terminal-multiplexer** —— 多路复用器策略角度。

## 相关

- [vtebench — Alacritty 的基准](https://github.com/alacritty/vtebench)
- [Alacritty FAQ — 性能](https://alacritty.org/faq.html)
- [Kitty 文档 — 性能](https://sw.kovidgoyal.net/kitty/)
- [Ghostty 1.0 发布说明 — 能耗](https://ghostty.org/)

> per-tool 安装 / 配置：`x-cmd.com/install/<terminal>` 或
> `x-cmd.com/doc-2026/install/<terminal>` 的 per-tool 文章。本页
> 仅为角度 / 分析。