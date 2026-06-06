# mk-toolkit

我个人的 **Agent Skill 集合**，同时也是一个 **Claude Code 插件 marketplace**。

这是一个会持续扩充的 skill 工具箱，**不局限于某一领域**——研发、写作、运维、信息检索、日常自动化等任何能沉淀为 skill 的能力，未来都会陆续收录进来。所有 skill 遵循 [agentskills.io](https://agentskills.io/specification) 规范，任何支持该规范的 AI 编程工具（Claude Code、Codex CLI 等）都能安装使用。

## Skills

所有 skill 打包为 `mk-toolkit` 插件。下面是当前已收录的 skill（列表会随时间增长）。

### 研发流程

强制执行结构化的研发流程：先写文档，再写代码。

### `/dev-workflow`

研发全生命周期编排器。从 User Story 到发布分 6 步引导，每步需人工确认后继续：

1. **User Story** — 明确用户体验和验收标准
2. **技术方案** — 架构边界与术语统一
3. **测试方案** — 每条验收标准都有测试覆盖
4. **TDD 实现** — 测试先行编码
5. **E2E 全量验证** — 回归检查
6. **提交与发布** — 交付上线

### `/tech-doc-system`

技术文档结构与写作规范：严格的写作顺序（文档先于代码）、User Story 规范（纯用户视角，禁止技术细节）、`docs/` 目录约定、文档审查清单、会话产物归并规则。

## 安装

### Claude Code

```bash
# 添加 marketplace 并安装插件
/plugin marketplace add maj1999/mk-toolkit
/plugin install mk-toolkit@mk-skills
```

开发期间可从本地路径添加：

```bash
/plugin marketplace add /path/to/mk-toolkit
/plugin install mk-toolkit@mk-skills
```

### Codex CLI

Codex 通过 `~/.agents/skills/` 目录自动发现 skill，无需 marketplace。

```bash
# 1. 克隆仓库
git clone https://github.com/maj1999/mk-toolkit.git ~/.codex/mk-toolkit

# 2. 软链到 Codex 的 skill 发现目录
mkdir -p ~/.agents/skills
ln -s ~/.codex/mk-toolkit/dev-workflow    ~/.agents/skills/dev-workflow
ln -s ~/.codex/mk-toolkit/tech-doc-system ~/.agents/skills/tech-doc-system

# 3. 重启 Codex 即可生效
```

Windows：

```powershell
git clone https://github.com/maj1999/mk-toolkit.git "$env:USERPROFILE\.codex\mk-toolkit"
New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\.agents\skills"
cmd /c mklink /J "$env:USERPROFILE\.agents\skills\dev-workflow"    "$env:USERPROFILE\.codex\mk-toolkit\dev-workflow"
cmd /c mklink /J "$env:USERPROFILE\.agents\skills\tech-doc-system" "$env:USERPROFILE\.codex\mk-toolkit\tech-doc-system"
```

### 手动安装（通用）

本仓库的每个 skill 都是一个独立目录，内含 `SKILL.md`。直接把对应目录放进你的工具的 skill 发现路径即可。

## 目录结构

```
mk-toolkit/
├── .claude-plugin/
│   └── marketplace.json   # Claude Code marketplace 清单
├── dev-workflow/
│   └── SKILL.md
└── tech-doc-system/
    └── SKILL.md
```

## 设计理念

- **文档即规格。** 每个步骤的产出是下一步的输入。
- **User Story 保持纯净。** 验收标准中不允许出现类名、API 路径、数据库字段。
- **测试先于代码。** 每个功能都需要测试；每个 bug 修复都从失败的测试开始。
- **人在回路中。** 流程在每步暂停等待确认。

## 许可证

MIT
