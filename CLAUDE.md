# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 这是什么

一个 **Agent Skill 集合**，同时是一个 **Claude Code 插件 marketplace**。仓库没有代码、没有构建/测试/lint —— 内容就是若干个遵循 [agentskills.io](https://agentskills.io/specification) 规范的 skill。一个 skill = 一个顶层目录 + 目录内的 `SKILL.md`（纯 Markdown + YAML frontmatter）。

本地目录名是 `mk-skills`，但 GitHub 仓库名是 `mk-toolkit`。两者指同一个项目。

## 三个名字别搞混

| 概念 | 值 | 出处 | 影响 |
|---|---|---|---|
| GitHub 仓库名 | `mk-toolkit` | `git remote` / GitHub | `/plugin marketplace add maj1999/mk-toolkit` 的地址 |
| marketplace 名 | `mk-skills` | `.claude-plugin/marketplace.json` 的 `name` | 安装命令 `…@mk-skills` 的后缀 |
| 插件名 | `mk-toolkit` | marketplace.json 里 `plugins[].name` | 安装命令 `mk-toolkit@…` 的前缀 |

安装命令因此是：`/plugin install mk-toolkit@mk-skills`。改动其中任何一个名字时，务必同步 README 的安装命令、克隆地址，以及（改仓库名时）GitHub 上的实际仓库名。

## 结构

```
mk-toolkit/
├── .claude-plugin/marketplace.json   # 唯一的「装配清单」，把各 skill 目录聚合进 mk-toolkit 插件
├── <skill-name>/SKILL.md             # 每个 skill 一个顶层目录（扁平，不套 skills/ 这层）
└── README.md
```

所有 skill 都打包进**单个** `mk-toolkit` 插件，由 `marketplace.json` 的 `plugins[0].skills` 数组（相对路径列表）声明。skill 目录是扁平放在仓库根下的，不在 `skills/` 子目录里 —— 这是有意的，marketplace.json 的 `skills` 字段支持任意相对路径，所以不需要那层包装。

## 维护规范（必须遵守）

所有 skill 的开发/迭代/发布都遵循 **[MAINTAINING.md](./MAINTAINING.md)**，三条硬性规则：

1. **worktree 隔离**：任何 skill 改动都在独立 git worktree + 分支里做，绝不直接改 `main`。
2. **版本维护**：每次改 skill 都要 bump 它 `SKILL.md` frontmatter 的 `version`（SemVer），并在该 skill 的 `CHANGELOG.md` 加记录。
3. **发布前确认 tag**：项目发布时先问维护者「是否打 tag」，确认后才创建项目级 tag `vX.Y.Z`。

两级版本：**单个 skill** 版本独立演进（frontmatter `version` + 各自 CHANGELOG）；**整个项目** 版本在发布时聚合（`marketplace.json` 的 `plugins[0].version` + 根 `CHANGELOG.md` + git tag）。tag 只打项目级，不给单个 skill 打。

## 新增一个 skill

走 MAINTAINING.md 的「流程 A」（worktree 隔离），并完成这三步登记：

1. 在仓库根新建 `<skill-name>/SKILL.md`，frontmatter 含 `name`（与目录同名）、`description`（决定何时被触发，写清楚「use when…」）、`version: 1.0.0`。
2. 在 `.claude-plugin/marketplace.json` 的 `plugins[0].skills` 数组里加一行 `"./<skill-name>"`。
3. 建 `<skill-name>/CHANGELOG.md`，并在 `README.md` 按领域分节登记（如 `### 研发流程`、`### 写作`）。

第 1、2 步缺一不可：只建目录不登记，Claude Code 不会发现它。

## 定位

通用、持续扩充、**不限领域**的 skill 工具箱（研发只是当前已收录的一类）。新增其它领域 skill 时，保持 README 按领域分节，别把整个项目重新窄化成「研发工具」。
