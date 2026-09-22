# x-cmd/terminal — 终端模拟器专题文库

终端模拟器的文章、横向对比与深度介绍。在 <https://x-cmd.com/terminal>
以专题文库形式发布。

> 🌐 **English version: [README.md](./README.md)** — same
> content, English front matter.

本仓库托管 x-cmd 官网「终端」专题的英中双语文章。文章为内容导向，
事实优先，欢迎任何人提交**修改 PR** — 文章写作流程与 frontmatter
规范见 [`CONTRIBUTING.md`](./CONTRIBUTING.md)。

## 仓库结构

```
x-cmd/terminal/
├── README.md                 # 本文件（英文）
├── README.cn.md              # 中文版
├── CONTRIBUTING.md           # 文章写作流程 + frontmatter 规范 + FAQ schema
├── SKILL.md                  # AI agent 使用本专题文库的配方
├── LICENSE                   # Apache-2.0
└── docs/
    ├── 0-latest-terminals.{en,cn}.md        # 终端模拟器最新动态
    ├── 0-latest-terminals.llms.md
    ├── 0-latest-terminals.faq.yml
    ├── 1-terminal-overview.{en,cn}.md       # 概览 + 横向对比
    ├── 1-terminal-overview.llms.md
    ├── 1-terminal-overview.faq.yml
    ├── 2-alacritty.{en,cn}.md               # Alacritty 深度介绍
    ├── 2-alacritty.llms.md
    └── 2-alacritty.faq.yml
```

文件名前缀的数字就是阅读顺序。每个槽位的四个文件保持同步：
`.en.md`、`.cn.md`、`.llms.md`、`.faq.yml`。

## 文章槽位

| 槽位 | 文章 | 用途 |
| --- | --- | --- |
| `0-` | 最新动态 | 终端模拟器领域最近发生的事情 — 新版本发布、趋势、破坏性变更。Newsletter 风格。 |
| `1-` | 概览 + 横向对比 | 终端模拟器领域的整体地图；一张对比表覆盖主流选项。 |
| `2-…` | 单工具深度介绍 | 每个值得介绍的终端一篇文章 — 它是什么、如何安装、如何配置、何时使用 / 不使用。 |

## 姊妹仓库

- [`x-cmd/cve`](https://github.com/x-cmd/cve) — CVE / CWE 情报（专题文库模式参考）。
- [`x-cmd/gpg`](https://github.com/x-cmd/gpg) — 团队 GPG 公钥环（专题文库模式参考）。
- [`x-cmd/browser`](https://github.com/x-cmd/browser) — 浏览器专题文库（平行结构）。
- [`x-cmd/install`](https://github.com/x-cmd/install) — 安装数据库（驱动 `x install <name>`）。
- [`x-cmd/x-cmd`](https://github.com/x-cmd/x-cmd) — 模块源码（`mod/`）。

## 许可

Apache License 2.0 — 见 [`LICENSE`](./LICENSE)。