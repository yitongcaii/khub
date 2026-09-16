---
tags: 素材库, HR作品集
---

# HRBP 作品集 · v8（分栏修复 + 海报灯箱版）

> 线上：https://yitongcaii.github.io/activities-html/portfolio/hrbp-portfolio-v8.html
> 源文件：D:\AI\workbuddy\2026-08-25-18-13-30\hrbp-portfolio-v8.html
> localStorage 前缀：`hrbp8_`（与 v1–v7 隔离）

## 设计方向
沿用 v3–v7 的 **Editorial Dossier**（墨黑+纸白+朱红、衬线+黑体、零圆角阴影渐变、细线+留白）。v1–v7 全部保留在仓库同目录。

## 本轮改动（v8，基于 v7）
- **修复分栏失效 bug**：v7 的 `.page` 同时写了 `display:none` 和 `display:flex`（后者覆盖前者），导致 6 页同时显示、导航切换"看起来"失效。改为 `.page{display:none;…}` + `.page.active{display:flex}`，分栏导航恢复正常。
- **海报改为页内封面 + 灯箱放大**：按用户要求不放全部海报、只放一张封面（业务连续性文化周 `culture-week.jpg`），点击在**页内灯箱**放大查看全图（不跳走、点击任意处关闭），不再用外跳 `<a href>`。
- 其余沿用 v7：每页 `min-height:100vh` 撑满一屏、删 footer「📌 本页由 yitong 沉淀整理」、文化活动页用精简版知识库首页 `index-trimmed.html` + 海报。

## 内容结构（7 页分栏）
00 定位 / 01 招聘交付 / 02 AI 自动化 / 03 文化活动 / 04 数据看板 / 05 SSC·跨文化 / 06 关于
每页只聚焦一主题；3 个作品视频保留带真实封面（ai-comic / training-points / knowledge-base）。

## 版本线
v1(原版Bento)→v2(hero加电话邮箱+视觉止血)→v3(创意编辑式 Dossier 初版)→v4(Editorial Cut 对外展示版)→v5(分栏+视频封面)→v6(主色更白+分栏高级感+视频卡兼容)→v7(每页一屏+删footer+海报替换)→v8(分栏bug修复+海报灯箱)。线上活跃：`v8`。
