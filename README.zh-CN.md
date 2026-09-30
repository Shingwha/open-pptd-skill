# open-pptd-skill

**[open-pptd](https://github.com/Shingwha/open-pptd) 的内容面（知识包）**——围绕 **PPTD 格式**（把 OOXML 抽象为自包含页面的 YAML 中间 DSL）构建的演示文稿创作与导出技能。本仓库是纯文本：`SKILL.md` 与它链接的 `references/`。**不含任何可执行文件**——没有脚本、没有构建、没有依赖。所有预览、校验、渲染、导出动作都由引擎仓的 `open-pptd` 命令行工具完成。

```
open-pptd-skill/
├── SKILL.md                    # 技能入口（方法论 + CLI 命令面）
├── references/
│   ├── design.md               # 场景指南、视觉风格、配色、字体系统
│   ├── pptd.md                 # PPTD v2 格式规范（唯一事实来源）
│   ├── shapes.md               # 预置形状速查表（177 形状 + 参数）
│   └── slides_categories/      # 分场景深入指南（×8）
├── README.md / README.zh-CN.md
├── LICENSE
└── .github/workflows/drift-guard.yml
```

## 1. 先装 CLI

技能只知道 CLI 的**命令面**，它自己不能画、不能校验、不能导出。每台机器装一次（装到 `~/.open-pptd`，不需要管理员）：

**Windows（PowerShell）**

```powershell
irm https://raw.githubusercontent.com/Shingwha/open-pptd/main/install.ps1 | iex
```

**Linux / macOS**

```sh
curl -fsSL https://raw.githubusercontent.com/Shingwha/open-pptd/main/install.sh | sh
```

安装器从 GitHub Releases 下载最新运行时 zip、校验 SHA256、解压到 `~/.open-pptd/cli/versions/<ver>`、把 `~/.open-pptd/cli/bin` 加入**用户级** PATH，并默认安装 Font Awesome 图标资产。幂等可重复执行。可选：`-Version <ver>`（PowerShell）/ `--version <ver>`（sh）锁定版本；`-WhatIf` / `--dry-run` 预演步骤不落盘。

验证安装：

```sh
open-pptd doctor
```

如果找不到 `open-pptd`，先新开一个终端（PATH 变更对已运行的进程无效）。

## 2. 安装本技能

把本仓库复制（或 clone）到你所用 agent 的技能目录，使技能目录下出现一个同时包含 `SKILL.md` 与 `references/` 的 `open-pptd/` 文件夹：

```sh
git clone https://github.com/Shingwha/open-pptd-skill.git <你的技能目录>/open-pptd
```

常见位置（按你的 agent 调整）：

| Agent | 技能目录（示例） |
|---|---|
| ZCode | `~/.zcode/skills/` |
| Claude Code | 项目/会话技能目录 |
| pi 及其他 | 该 agent 配置的技能目录 |

要求只有一条：`SKILL.md` 与 `references/` 位于**同一目录**——这是标准 Agent Skills 布局，`SKILL.md` 以相对路径引用 `references/*`。

> 装技能**不会**装 CLI，反之亦然。这个拆分是有意的：引擎按自己的节奏发版，技能只依赖稳定的命令面（`serve` / `check` / `export` / `ensure` / `render` / `assets` / `fonts` / `doctor` / …）。

## 3. 漂移守卫

`references/` 是手写文档，但它描述的契约存在于引擎代码中。`drift-guard` 工作流（`.github/workflows/drift-guard.yml`）拉取引擎最新 release，对其做五项只读比对（前四项为规定动作）：

1. `pptd.md §5` 各类型必填字段 ↔ 引擎 `validate.js` schema 键集合；
2. `pptd.md §3` Theme 结构 ↔ `theme.js` / `theme-presets.js` 键名；
3. `shapes.md` 形状/连接线数量 ↔ `scripts/gen-preset-geometry.mjs` 几何数据计数；
4. `design.md §4` 注册字体名 ↔ `assets/fonts/registry.json` family/alias 集合；
5. `design.md` 预设配色表 ↔ `theme-presets.js` `THEME_PALETTES`（继承自引擎原 `tests/regression/theme-presets.mjs` 的守卫，文档迁入本仓后随之落地于此）。

任何不一致都会让工作流失败，并提示 `references/` 需要更新以对齐引擎。该检查纯只读，不引入两个仓库之间的依赖。

## License

MIT — 见 [LICENSE](LICENSE)。

English: [README.md](README.md)
