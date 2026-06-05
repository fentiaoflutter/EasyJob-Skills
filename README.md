# Claude Code Skills · Flutter App 工作流

这是一组面向 Flutter / GetX 项目日常开发的 Claude Code Skills，覆盖需求拆解、分支管理、接口接入、需求对齐验证、日志处理、国际化、联调开关、提交前审查等场景。

> 仓库中的占位符（如 `<app-repo>`、`<main-module>`、`<lib-pkg>`、`<sub-module-*>`、`PR-XXXXX`、`dev_vX.Y.Z`）需要你替换成自己项目里的真实名字后再使用。

## 安装

将本仓库中任意 skill 目录拷贝到 `~/.claude/skills/`：

```bash
cp -R app-requirement-html-doc ~/.claude/skills/
# 其它 skill 同理
```

重启 Claude Code 后，对应 skill 会在 `/` 列表中可用。

## Skill 列表

| 场景 | Skill | 作用 |
| --- | --- | --- |
| 需求整理 | [`app-requirement-html-doc`](./app-requirement-html-doc) | 把需求文档拆成技术设计、任务清单、自测项，输出单文件 HTML |
| 分支管理 | [`dev-feature-branch-rebase`](./dev-feature-branch-rebase) | 从正确的 `dev_v*` 分支创建需求分支，或把当前分支同步到最新 dev |
| 接口接入 | [`api-integration-workflow`](./api-integration-workflow) | 按接口文档接 API / Model / Controller / Page，自带字段校验 |
| 需求对齐验证 | `verify`（Claude Code 内置） | 全量读取相关代码，核对实现与需求/产品文档是否一致 |
| 日志清洗 | [`clean-log`](./clean-log) | 把 Talker / Dio / logcat 原始日志清成可读请求响应 |
| 日志落盘 | [`api-request-response-dump`](./api-request-response-dump) | 把联调请求/响应写入指定目录，方便追溯与共享 |
| 国际化检查 | [`feature-i10n-scope`](./feature-i10n-scope) | 检查页面文案、`.tr` key 与 `zh_CN.json` 是否对齐 |
| 提交前审查 | [`pre-commit-auto-review`](./pre-commit-auto-review) | commit 前检查 diff、风险点、自测项，并生成提交信息草稿 |

> 注：`verify` 是 Claude Code 自带的内置 skill，未包含在本仓库内，直接在 Claude Code 中使用即可。

## 目录结构

每个 skill 都是一个独立目录，至少包含一个 `SKILL.md`（带 YAML frontmatter）。Claude Code 会读取 frontmatter 的 `description` 字段来判断触发场景。

```
claude-skills/
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

## 关于项目耦合

部分 skill（如 `feature-i10n-scope`、`pre-commit-auto-review`、`api-integration-workflow`）原本针对某个具体的 Flutter / GetX 项目编写，已脱敏为占位符形式。迁移到自己项目时，请把 `<app-repo>`、`<main-module>`、`<lib-pkg>`、`<sub-module-*>` 等占位符替换成实际路径，并根据自家命名规范调整文件名 / 类名约定。
