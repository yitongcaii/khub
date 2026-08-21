---
title: AI沙龙-部署交接文档
tags: [AI沙龙, 部署, 交接, 开源工具]
source: handoff-ai-starter-pack.html
---

# AI 沙龙选题库 + 个人首玩包部署 · 交接文档

> 反向链接：[[00-活动总览]] / [[AI沙龙选题库]] / [[AI沙龙-个人首玩包门户]]
> 本页写给完全没上下文的新会话，读完即可无损接手。

## 一句话
GitHub AI 开源工具检索（选题库）→ 个人首玩包本机部署（bat 脚本）→ 两页推 GitHub Pages → Open WebUI 本地服务跑通。全链路 08-06 起、08-07 闭环。

## 访问入口
- 本地源：`C:\Users\v_yitcai\WorkBuddy\2026-08-06-09-56-06\handoff-ai-starter-pack.html`
- 交接文档线上：https://yitongcaii.github.io/activities-html/handoff-ai-starter-pack.html
- 选题库线上：https://yitongcaii.github.io/activities-html/ai-salon-topics.html
- 首玩包线上：https://yitongcaii.github.io/activities-html/ai-starter-pack.html
- 本机服务：http://localhost:8080

## 六大板块速览
1. **我们在做什么**：AI 沙龙选题库 + 个人首玩包部署
2. **完成了什么**：选题库 HTML / 门户 HTML / 4 个 setup-*.bat / 克隆资源 / Obsidian 2 篇 / 服务跑通
3. **项目脉络**：08-06 检索+排障 → 08-07 加说明+推 Pages+修 AppLocker
4. **卡点&方案**：6 坑（双击秒退 / GBK 乱码 / LF 换行 / Administrator / WindowsApps stub / AppLocker 4551）
5. **踩坑铁律**：bat 五律 + Python 大依赖五律 + Pages 五律 + 协作五律（见 HTML 详表）
6. **环境信息 / 待办残留**：路径、账号 v_yitcai、SSH key、待清理 venv 残留

## 最痛三坑（先记死）
- 公司机器 **AppLocker 拦 torch.dll** → venv 挪 `%LOCALAPPDATA%` + 卸 torch
- Windows **.bat 必须 CRLF** → Write 默认 LF，写完 Python 转
- **禁用 Administrator** 跑脚本 → winget 缺失、路径全乱

## 标签
#AI沙龙 #部署 #交接文档 #开源工具 #GitHubPages #AppLocker
