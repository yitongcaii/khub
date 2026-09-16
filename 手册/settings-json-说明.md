---
title: WorkBuddy settings.json 配置说明（脱敏）
created: 2026-09-04
tags: [workbuddy, 配置, 脱敏]
---

> 一句话：这是 WorkBuddy **用户级全局配置**文件（`~/.workbuddy/settings.json`）的结构说明与字段字典。**本笔记已脱敏，不含任何密钥明文。**
> ⚠️ 原文件含 `botSecret` / `botToken` / `webhookUrl` / `sessionId` 等通道凭证，**切勿提交到任何远程仓库（含 GitHub / Obsidian 远程同步）**。

## 文件位置
- 原文件：`C:\Users\v_yitcai\.workbuddy\settings.json`（用户级，随用户走，不随单个项目变）
- 自动备份：`settings.json.bak.20260904102754`（改动前系统留底，配错可回滚）

## 四大区块

### 1. `enabledPlugins` — 插件/技能开关
控制 WorkBuddy 启动时加载哪些插件。当前启用（共 15 个）：
`finance-data`、`agent-browser`、`playwright-cli`、`skills-sec-audit`、`find-skills`、`document-skills`、`pptx`、`pdf`、`docx`、`xlsx`、`weixinpay`、`tencent-docs-plugin`、`tencent-pptx`

### 2. `claw` — 数字员工（Claw）多通道接入
| 字段 | 作用 | 敏感值 |
|---|---|---|
| `claw.channels.wechatmp` | 微信公众号通道，webhook 模式 | webhookUrl 🔒 / sessionId 🔒 |
| `claw.users.{uid}.channels.wecomaibot` | **企业微信 bot 入站通道**（企微 → WorkBuddy） | botId `aibYv1mC...` 🔒、botSecret 🔒、webhookUrl（codebuddy.cn 代理）🔒、sessionId 🔒；`replyPolicy.mode=all` |
| `claw.users.{uid}.channels.weixinClawBot` | 微信 bot 通道 | botToken 🔒、accountId 🔒、userId 🔒；baseUrl=`https://ilinkai.weixin.qq.com`（公开域名，可保留）、connectionMode=polling |
| `claw.legacyOwnerUid` | 数字员工归属用户 uid | — |

### 3. `sandbox.extraAllowWrite` — 沙箱可写白名单
允许沙箱写入的目录：`~/.tencent-cloudq`、`~/.andonq`、`~/.xiaoe-cloud`、`~/.config/wecom`、`~/.lark-cli`、`~/.lark-channel`、`~/.dws`、`/tmp/dws-cache`、`~/.tmeet`、`~/Library/Application Support/tmeet`

### 4. 运行态字段
- `wecomaibot.lastChatId`：上次企微会话 ID（`T64710047A`），运行残留，非固定配置。

## 安全红线
- `botId` / `botSecret` / `botToken` / `webhookUrl` / `sessionId` 均为通道凭证，禁止明文入库或上云。
- 改动需重启 / 重新加载 WorkBuddy 才生效；改错会让企微 / 微信通道断连。

## 相关
- 企微双通道机制：本文件的 `wecomaibot` 是「企微 → WorkBuddy」**入站**桥；`mcp.json` 的「企微机器人」是企微官方 robot-doc MCP（apikey，**出站**，WorkBuddy → 企微）。另还有 `wecom 企业微信` 连接器（`connectors/` 授权）为第三套独立接入。
