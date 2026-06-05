# EasyJob-Skills

> 一组开箱即用的 [Claude Code](https://docs.claude.com/en/docs/claude-code) Skills，把日常研发里那些"每次都要重复说一遍"的流程固化成可触发的工作流：需求拆解、分支管理、接口接入、国际化审计、日志清洗、提交前审查。

本仓库收集了 7 个从真实项目里沉淀、跑通验证后再脱敏发布的 skill。每个 skill 都包含**触发场景 / 输入约定 / 执行流程 / 输出模板 / 示例**，你能直接看到"用户说什么 → skill 做什么"。

---

## 解决什么问题

| 反复出现的痛点 | 对应 Skill |
|------|------------|
| 需求文档冗长、APP 与后台混在一起，每次评审都要重新理 | [`app-requirement-html-doc`](./app-requirement-html-doc) |
| 拉新需求分支要找最新 baseline、还要清 upstream | [`dev-feature-branch-rebase`](./dev-feature-branch-rebase) |
| 接口文档一来，要在网络层 / 模型 / 业务层 / 视图层四处改 | [`api-integration-workflow`](./api-integration-workflow) |
| 联调日志噪音多，请求响应难提取 | [`clean-log`](./clean-log) |
| 联调结果发给后端，每次都要重新整理一遍 | [`api-request-response-dump`](./api-request-response-dump) |
| 文案漏国际化 / 漏改主翻译文件，靠肉眼检查不靠谱 | [`feature-i10n-scope`](./feature-i10n-scope) |
| commit 前忘记自检 / 漏跑回归 / message 不规范 | [`pre-commit-auto-review`](./pre-commit-auto-review) |

> 这些 skill **不绑定特定语言或框架**。示例代码大多以 Flutter / GetX 写成（因为它们源自一个真实 Flutter 项目），但流程和判断标准对 React / Android / iOS 等都适用 —— 见下方[多栈适配](#多栈适配)。

## 快速上手

```bash
# 1. 克隆
git clone https://github.com/fentiaoflutter/EasyJob-Skills.git

# 2. 安装到 Claude Code 用户级 skills 目录
mkdir -p ~/.claude/skills
cp -R EasyJob-Skills/*/ ~/.claude/skills/

# 3. 重启 Claude Code
```

之后用对应触发短语对 Claude Code 说话，相应 skill 会自动生效（无需手动 `/skill`）。例如对它说 *"清理一下日志"*、*"把这个接口请求返回写到文件"*、*"提交前看一下改动"*，对应的 skill 就会接管。

## 7 个 Skill 一览

### `app-requirement-html-doc` · 需求文档生成器

把"全量需求文档"筛掉非 APP 内容，输出**管理后台风格**的中文单文件 HTML +同源 Markdown，含固定 8 章节（概览 / 高亮 / 需求 / 技术设计 / 接口联调 / 任务拆分 / 完成清单 / 自测用例），左侧导航、可勾选完成项、可编辑排期，并自动维护「需求文档首页」索引。

**触发短语**：`生成需求分析文档` / `更新技术设计文档` / `按接口文档持续更新接口联调章节` / `一个 HTML 文件包含全部内容`

### `dev-feature-branch-rebase` · 分支管理

两个高频 git 流程：从最新远端 `dev_v*` 集成分支创建功能分支（不设 push upstream，工作区脏时不切换），或把当前分支 rebase 到其源 `dev_v*`。冲突 / force-push 主动停下来等用户决策，不静默丢改。

**触发短语**：`拉新分支` / `从 dev 拉一个 PR 分支` / `更新代码` / `rebase 到最新 dev`

### `api-integration-workflow` · 接口接入

按接口文档分四层接入：网络层 → 数据模型 → 业务层 → 视图层。带字段类型核对、解析兼容（数字/字符串混用、字段别名）、基于日志的 6 项 checklist 校验，最后按需求粒度提交，不带无关文件。

**触发短语**：`我发接口文档给你，帮我接入` / `继续接入这个接口` / `按文档改字段` / `联调修复`

### `clean-log` · 日志清洗

粘贴任意 HTTP 客户端原始日志（Talker / Dio / OkHttp / axios / fetch / logcat / 控制台），输出可读的 `[http-request]` / `[http-response]` 块 + 两个独立 JSON 块，截断处明确标 `payload_status: partial`，**不脑补**。

**触发短语**：`清理一下日志` / `给我干净的 json` / `提取请求响应`

### `api-request-response-dump` · 联调日志落盘

`clean-log` 的进阶版：不只是清洗显示，而是写成单个 `<时间戳>_<endpoint>.md` 文件落盘到指定目录，附完整性状态、缺失字段说明、按需脱敏（token / sign / uid）。方便归档或发给后端。

**触发短语**：`把这个接口请求返回写到文件` / `导出完整请求响应` / `给后端联调包`

### `feature-i10n-scope` · 国际化审计

**在用户给定的范围内**（PR 编号 / 分支 / 路径前缀 / 文件列表）查 i18n —— 严格定界，不全仓库扫描。识别已国际化调用 vs. 真正硬编码，给豁免清单（占位符 / 后端直出 / 通用符号 / 调试日志），缺的 key 补到主翻译文件，输出可贴 PR 的清单表。

**触发短语**：`检查 PR-XXXXX 的文案` / `补一下这个目录下的翻译 key` / `这次需求文案有没有漏 zh_CN`

### `pre-commit-auto-review` · 提交前审查

`git commit` 前跑结构化审查：正确性 / 回归 / 安全 / 可维护性 / 测试 5 维度，输出阻断项 + 风险提醒 + 最小验证清单 + commit message 草稿（强调 why）。**用户没说"提交"就不自动 commit**。可以串到 git hook 里强制走。

**触发短语**：`帮我提交` / `准备 commit` / `提交前看一下` / `review 一下这些改动`

---

## 多栈适配

`api-integration-workflow` 和 `feature-i10n-scope` 的示例使用 Flutter / GetX 写法（`.tr`、`<biz>_api.dart` 等）。其它技术栈使用时，把例子替换成对应机制即可，思路是通用的：

| 概念 | Flutter / GetX | React (next-intl / i18next) | Android 原生 | iOS 原生 |
|------|----------------|------------------------------|--------------|----------|
| 翻译 key 调用 | `'key'.tr` | `t('key')` / `<FormattedMessage id="key" />` | `getString(R.string.key)` | `NSLocalizedString("key", ...)` |
| 主翻译文件 | `assets/I10n/zh_CN.json` | `locales/zh-CN.json` | `res/values-zh/strings.xml` | `zh-Hans.lproj/Localizable.strings` |
| 网络层 | `<biz>_api.dart` | `services/<biz>.ts` | `<Biz>Repository.kt` | `<Biz>Service.swift` |
| 数据模型 | `<Biz>Model.dart` | `types/<biz>.ts` | `<Biz>Dto.kt` | `<Biz>.swift` |
| 业务层 | Controller (GetX) | store / hook / context | ViewModel | ViewModel |
| 视图层 | Widget / Page | Component / Page | Activity / Compose | View / UIViewController |

## 占位符替换清单

仓库里所有 SKILL.md 使用了若干占位符（来自脱敏），首次接入到自己项目时，全局搜索替换：

| 占位符 | 含义 | 替换示例 |
|--------|------|----------|
| `<app-repo>` | 你的主仓库目录名 | `my-app` |
| `<main-module>` | 主模块目录 | `app` / `frontend` |
| `<lib-pkg>` | 内部依赖库 | `my-app-lib` |
| `<components-pkg>` | 内部组件库 | `my-ui-components` |
| `<sub-module-*>` | 各业务子模块 | `wallet` / `market` 等 |
| `PR-XXXXX` / `dev_vX.Y.Z` | PR 编号 / 集成分支版本号 | 按你实际命名 |
| `<biz-domain>` / `<resource>` | 业务域 / 资源名（接口落盘默认目录） | `user` / `order` 等 |

## 工作原理

Claude Code Skills 是 Claude Code 的"工作流插件"机制：每个 skill 是一个目录，里面至少有一个 `SKILL.md`，文件顶部的 YAML frontmatter 告诉 Claude Code **何时触发、做什么**：

```yaml
---
name: <skill-name>
description: <一句话能力 + Use when ... 触发短语>
---

# Skill 内容（Markdown 自由发挥）
```

Claude Code 启动时扫描 `~/.claude/skills/` 下所有目录，对话中根据用户说的话与每个 skill 的 `description` 做语义匹配，命中后把 `SKILL.md` 正文当作 system prompt 注入，按里面的流程执行。

> 想了解更多：[Claude Code Skills 官方文档](https://docs.claude.com/en/docs/claude-code/skills)

## 目录结构

```
EasyJob-Skills/
├── api-integration-workflow/
│   └── SKILL.md
├── api-request-response-dump/
│   └── SKILL.md
├── app-requirement-html-doc/
│   └── SKILL.md
├── clean-log/
│   └── SKILL.md
├── dev-feature-branch-rebase/
│   └── SKILL.md
├── feature-i10n-scope/
│   └── SKILL.md
└── pre-commit-auto-review/
    └── SKILL.md
```

## FAQ

**Q：和 Claude Code 内置 skill 是什么关系？**
A：互不冲突。内置 skill（如 `verify` / `run` / `review`）是 Claude Code 自带的通用能力；本仓库是用户级 skill，放在 `~/.claude/skills/`，可以与内置 skill 并存，互相不重名即可。

**Q：会不会和我已有的 skill 冲突？**
A：只要目录名不重名就不会冲突。建议先 `ls ~/.claude/skills/` 看一眼，重名的话改名或先备份再装。

**Q：可以只装一两个吗？**
A：可以，每个 skill 都是独立目录，单独 `cp -R` 一个就行。

**Q：触发短语支持改吗？**
A：直接改对应 SKILL.md frontmatter 里的 `description`，重启 Claude Code 即可生效。

## 贡献

欢迎 issue / PR：发现某个 skill 的流程在你的栈下不通用、有更好的写法、新增其它工作流，都可以提。提 PR 时请保留每个 skill 的统一结构（触发场景 / 输入约定 / 执行流程 / 输出模板 / 禁止事项 / 示例）。

## License

MIT
