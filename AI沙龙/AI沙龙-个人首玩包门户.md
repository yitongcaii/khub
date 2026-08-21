---
title: AI 沙龙 · 个人首玩包门户
tags: [AI沙龙, 活动, 部署, 工具]
created: 2026-08-06
---

# 个人首玩包门户 · 6 件 AI 利器一键直达

> 一句话：为 y 部署的 6 件 AI 工具聚合门户（私人 ChatGPT / 本地出图 / 拖拽 Agent / 企业平台 / 提示词库 / 系统课），每张卡含部署脚本、直达链接，以及「📖 能干啥 · 怎么用」折叠操作指引。

本地访问（点开即看，源文件未删）：
[ai-starter-pack.html](file:///C:/Users/v_yitcai/WorkBuddy/2026-08-06-09-56-06/ai-starter-pack.html)

线上访问（多设备/随时，GitHub Pages）：
[ai-starter-pack.html](https://yitongcaii.github.io/activities-html/ai-starter-pack.html)

## 6 件工具速览
| 工具 | 类别 | 用途 | 入口 |
|---|---|---|---|
| Ollama + Open WebUI | 一键脚本 | 本地私人 ChatGPT（免费+隐私） | http://localhost:8080 |
| ComfyUI | 一键脚本 | 本地 AI 画图/视频 | http://localhost:8188 |
| Langflow | 一键脚本 | 拖拽搭 Agent（需 API Key） | http://localhost:7860 |
| Dify | 一键脚本 | 企业级 AI 平台（需 Docker） | http://localhost |
| f/prompts.chat | 在线直达 | 提示词灵感库 | https://prompts.chat |
| generative-ai-for-beginners | 本地资源 | 微软 21 课系统学 AI | 已克隆 ai-learning/ |

## 部署脚本（本机双击/cmd 运行）
工作区 `C:\Users\v_yitcai\WorkBuddy\2026-08-06-09-56-06\` 下：`setup-ollama-openwebui.bat` / `setup-comfyui.bat` / `setup-langflow.bat` / `setup-dify.bat`。

> ⚠️ 关键踩坑（已修复）：bat 必须 CRLF 换行；cmd 用 goto 写法避开 `if (...) (...)` 单行块陷阱；必须非 Administrator 的 v_yitcai 普通账号跑（ollama 已是 `--scope user` 免 UAC）。
