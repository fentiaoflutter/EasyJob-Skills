# Claude Code Skills · 通用研发工作流

一组面向日常研发的 Claude Code Skills，覆盖需求拆解、分支管理、接口接入、日志处理、国际化审计、提交前审查等场景。**不绑定特定语言或框架**，示例多以 Flutter / GetX 写就，但思路和流程对其它技术栈同样适用。

> 仓库中的占位符（如 `<app-repo>`、`<main-module>`、`<lib-pkg>`、`<sub-module-*>`、`PR-XXXXX`、`dev_vX.Y.Z`）需要替换成自己项目里的真实名字后再使用。

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
| 需求整理 | [`app-requirement-html-doc`](./app-requirement-html-doc) | 把需求文档拆成技术设计、任务清单、自测项，输出单文件 HTML + 同源 MD |
| 分支管理 | [`dev-feature-branch-rebase`](./dev-feature-branch-rebase) | 从最新 `dev_v*` 集成分支创建需求分支，或把当前分支 rebase 到源分支 |
| 接口接入 | [`api-integration-workflow`](./api-integration-workflow) | 按接口文档分层接入（API → Model → 业务层 → 视图层），自带字段校验与日志核对 |
| 需求对齐验证 | `verify`（Claude Code 内置） | 全量读取相关代码，核对实现与需求/产品文档是否一致 |
| 日志清洗 | [`clean-log`](./clean-log) | 把任意 HTTP 客户端原始日志清成可读请求 / 响应 |
| 日志落盘 | [`api-request-response-dump`](./api-request-response-dump) | 把联调请求 / 响应写入指定目录，方便追溯与共享 |
| 国际化检查 | [`feature-i10n-scope`](./feature-i10n-scope) | 在需求范围内审计文案与翻译 key 是否对齐主翻译文件 |
| 提交前审查 | [`pre-commit-auto-review`](./pre-commit-auto-review) | commit 前检查 diff、风险点、自测项，并生成提交信息草稿 |

> 注：`verify` 是 Claude Code 自带的内置 skill，未包含在本仓库内，直接在 Claude Code 中使用即可。

## 目录结构

每个 skill 都是一个独立目录，至少包含一个 `SKILL.md`（带 YAML frontmatter）。Claude Code 会读取 frontmatter 的 `description` 字段来判断触发场景。

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

## 关于跨栈适配

`feature-i10n-scope` 和 `api-integration-workflow` 的示例代码片段使用 Flutter / GetX 的写法（如 `.tr`、`<biz>_api.dart` 这类）。其它技术栈使用时，把例子替换成对应机制即可：

| 概念 | Flutter / GetX | React / Next | Android 原生 | iOS 原生 |
|------|----------------|--------------|--------------|----------|
| 翻译 key 调用 | `'key'.tr` | `t('key')` / `useTranslation` | `getString(R.string.key)` | `NSLocalizedString("key", ...)` |
| 翻译主文件 | `zh_CN.json` | `locales/zh-CN.json` | `values-zh/strings.xml` | `zh-Hans.lproj/Localizable.strings` |
| 分层架构 | API / Model / Controller / Page | client / model / store / component | Repository / Model / ViewModel / Activity | Service / Model / ViewModel / View |
