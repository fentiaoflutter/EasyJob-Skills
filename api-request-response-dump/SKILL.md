---
name: api-request-response-dump
description: 把指定接口 / 功能点的请求-响应整理成单个 Markdown 文件落盘到指定目录，附完整性状态与脱敏选项。Use when the user asks to 把接口请求返回写到文件 / 落盘 / 导出联调日志 / 给后端联调包.
---

# 接口请求返回落文件

> 一句话目标：把指定接口或功能点的请求 / 响应，整理成一个可追溯的 Markdown 文件，写到用户指定目录。

> 与 [`clean-log`](../clean-log) 的区别：clean-log 只清洗并直接显示；本 skill **必须落盘成文件**，并附完整性状态与可选脱敏，便于发给后端 / 归档追溯。

## 触发场景

用户这样说时调用：

- 「把这个接口请求返回写到文件」「导出完整请求响应」「落到指定目录」「给后端联调包」
- 用户提供日志（Talker / Dio / 控制台）并要求生成可复制文档。

## 输入约定（缺则主动询问）

| 项 | 说明 | 缺省行为 |
|----|------|----------|
| 目标接口 / 功能名 | 例 `<biz-domain>/<resource>/list` | 必填，问用户 |
| 输出目录 | APP 内路径（用户指定优先） | 默认 `<main-module>/debug_logs/api_dumps/` |
| 是否脱敏 | token / sign / uid 等敏感头 | 默认不脱敏；用户要求时再脱 |

## 执行流程

1. **配对**：按时间和 URL 把请求 / 响应配对。
2. **完整性检查**：
   - JSON 是否闭合
   - 请求 / 响应是否成对
   - 是否存在截断尾部
3. **生成单文件**（每次一个）：
   - 文件名：`<yyyyMMdd_HHmmss>_<endpoint_slug>.md`
   - 内容：请求 + 响应 + 完整性状态 + 缺失字段说明
4. **写盘**：目标目录不存在则先创建。
5. **回报**：文件绝对路径、`payload_status`、是否可直接发后端。

## 完整性规则

- **禁止脑补**：日志截断时不得伪造完整 JSON。
- 不完整时显式标注：
  - `payload_status: partial | unusable`
  - `missing_parts: request_body / response_body / headers / truncated_tail`

## 文件模板

```markdown
# API Dump - <endpoint>

- generated_at: <ISO8601>
- payload_status: complete | partial | unusable
- missing_parts: <none | ...>
- source: talker | dio | logcat

## Request
- method: POST
- url: https://...
- headers:
```json
{ ... }
```
- body:
```json
{ ... }
```

## Response
- status: 200
- url: https://...
- body:
```json
{ ... }
```

## Notes
- 文档字段差异：
- 参数大小写核对：
- 分页 / 重复风险：
```

## 脱敏规则（按需）

按你项目里实际的鉴权 / 设备头名称替换：

- `<auth-token-header>`（如 `Authorization` / 自定义 token 头）
- `<sign-header>`（如 `X-Sign`）
- `<uid-header>`（如 `X-Uid`）
- `<device-id-header>`（如设备 UUID 头）

## 输出要求

回答中必须给出：

- 生成文件绝对路径
- `payload_status`
- 是否可直接发后端（是 / 否 + 原因）

## 示例

> **用户**：把刚才那个 list 接口的请求返回写到 `debug_logs/api_dumps/` 里。
> **Skill**：配对请求 / 响应 → 完整性检查 → 写入 `20260605_233012_list.md` → 回报路径 + `payload_status: complete` + 「可直接发后端」。
