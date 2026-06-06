# 维护规范

本项目（`mk-toolkit`）的开发与发布规范。**任何对 skill 的开发迭代都必须遵守。** 面向人类维护者和 AI Agent 共同执行。

## 三条硬性规则

1. **环境隔离**：任何 skill 的开发/迭代都在独立的 **git worktree + 分支** 里进行，绝不直接改 `main`。
2. **版本维护**：每次改动 skill 都要 bump 该 skill 自己的版本，并写 CHANGELOG。
3. **发布打 tag 前确认**：每次项目发布，先问「是否要打 tag」，**得到确认后**才创建项目级 git tag。

---

## 版本模型

两级版本，各自独立，互不强制同步：

| 级别 | 版本号所在 | 历史记录 | 谁来 bump |
|---|---|---|---|
| **单个 skill** | `<skill>/SKILL.md` frontmatter 的 `version` | `<skill>/CHANGELOG.md` | 每次改这个 skill 时 |
| **整个项目 / 插件** | `.claude-plugin/marketplace.json` 的 `plugins[0].version`，并对应 git tag `vX.Y.Z` | 根 `CHANGELOG.md` | 发布时聚合 |

两级都用 [SemVer](https://semver.org/lang/zh-CN/) `主.次.补丁`：

- **补丁（patch）**：措辞优化、错字、不改变触发条件与行为的修复。
- **次版本（minor）**：新增能力、扩展触发场景，向后兼容。
- **主版本（major）**：触发条件或行为的重大/不兼容改变、重写。

skill 版本**独立演进**；项目版本在**发布时聚合**这段时间内落地的所有 skill 改动，不强制随每次 skill 改动同步。

---

## 流程 A：开发 / 迭代一个 skill

> 全程在 worktree 里完成，main 保持干净。

1. **建隔离环境**：从 `main` 开 worktree + 分支。
   ```bash
   git worktree add ../mk-toolkit-<skill>-<简述> -b skill/<skill>-<简述>
   ```
   （在 Claude Code 里可用 `superpowers:using-git-worktrees` skill 自动完成。）
2. **改动**：在 worktree 里修改 `<skill>/SKILL.md` 及其引用文件。
3. **bump 版本**：按上面的 SemVer 规则改 `SKILL.md` frontmatter 的 `version`。
4. **写 CHANGELOG**：在 `<skill>/CHANGELOG.md` 顶部加一条新版本记录（含日期与 Added/Changed/Fixed）。
5. **提交、合并、清理**：提交后合回 `main`（PR 或直接合），然后删掉 worktree 与分支。
   ```bash
   git worktree remove ../mk-toolkit-<skill>-<简述>
   ```

> 新增 skill 同样走此流程；额外两步见 [CLAUDE.md](./CLAUDE.md)「新增一个 skill」（建目录 + 在 marketplace.json 登记），初始 `version` 设 `1.0.0`。

---

## 流程 B：发布一个项目版本

> 当累积的 skill 改动够一次发布时执行。同样建议在 worktree 里准备。

1. **定项目版本**：聚合上次 tag 以来的 skill 改动，按 SemVer 决定项目版本号（最大的那一类改动决定级别：有 skill major → 项目 major，依此类推）。
2. **更新两处**：
   - `.claude-plugin/marketplace.json` 的 `plugins[0].version`；
   - 根 `CHANGELOG.md` 加一条发布记录，列出本次涉及的各 skill 及其新版本。
3. **提交**：`chore(release): vX.Y.Z`。
4. **确认是否打 tag**（硬性规则 3）：明确询问维护者「本次发布是否创建 tag `vX.Y.Z`？」
   - **确认后**才执行：
     ```bash
     git tag -a vX.Y.Z -m "vX.Y.Z"
     git push origin main --follow-tags
     ```
   - 未确认则只推 commit，不打 tag。
5. tag 命名固定 `vX.Y.Z`，与 marketplace.json 的 plugin version 完全一致。

---

## 速查

- 改了某个 skill 但没 bump 它的 `version` / 没写 CHANGELOG → 不合规。
- 直接在 `main` 上改 skill（没开 worktree）→ 不合规。
- 未经确认就 `git tag` → 不合规。
- tag 是**项目级**的，不给单个 skill 打 tag。
