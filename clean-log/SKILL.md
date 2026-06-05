---
name: clean-log
description: 把粘贴的 HTTP 客户端原始日志（Talker / Dio / OkHttp / axios / fetch / logcat / 浏览器控制台等）清洗成可读的请求/响应文本，并产出可直接复制的 JSON 块。Use when the user asks to 清理日志 / 给干净 json / 提取请求响应 / 把这段日志整理一下.
---

# Clean Log（纯日志清洗）

> 一句话目标：把混在日志里的 HTTP 请求 / 响应，整理成可读、可复制的内容，不做任何业务改动。

## 触发场景

用户这样说时调用：

- 「清理一下日志」「给我干净的 json」「提取请求响应」「把这段日志整理给我」
- 用户粘贴任意 HTTP 客户端日志（Talker / Dio / OkHttp / axios / fetch / logcat / 浏览器控制台 等）混合文本，需要提炼请求 / 响应。

## 只做这些事

| 类别 | 内容 |
|------|------|
| 去噪 | ANSI 颜色码、时间戳、PID / 进程前缀、无关系统日志行 |
| 提取 | method、url、headers（若有）、request body、response status、response body |
| 输出 | 1）请求 + 响应可读块；2）request body / response body 纯 JSON 块 |

## 完整性规则

- **禁止脑补**：日志不完整时，不得伪造 JSON。
- 若截断或缺失：在输出顶部标注 `payload_status: partial | unusable`，并写明缺失项（如 `response_body` / `headers` / `truncated_tail`）。

## 输出模板

```text
[http-request]
method: POST
url: https://...
headers: {...}
body: {...}

[http-response]
status: 200
url: https://...
body: {...}
payload_status: complete | partial | unusable
```

```json
// request body
{ ... }
```

```json
// response body
{ ... }
```

## 禁止事项

- 不改业务代码、不做发布流程。
- 不自动写文件（除非用户明确要求；若需写文件，转交 `api-request-response-dump`）。

## 示例

> **用户**：把这段日志清一下，给我请求和响应。
> **Skill**：去掉 ANSI 与时间戳 → 抽出 method/url/body → 输出 [http-request] / [http-response] 两块 + 两个 JSON 块；若 response 截断，在顶部标 `payload_status: partial`。
