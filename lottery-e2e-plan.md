# 抽奖项目 · 端到端测试（e2e）方案

> 单文件 HTML 方案页（Bento 卡片墙风格，A 风格；已含「📖 大白话版」讲解章节，hero 有跳转锚点）
> 线上访问：[lottery-e2e-plan.html](https://yitongcaii.github.io/activities-html/tech/lottery-e2e-plan.html)（GitHub Pages，构建约 1–2 分钟，CDN 最长 10 分钟；若打开是旧的加 `?v=1` 绕过缓存）
> 本地访问：[lottery-e2e-plan.html](file:///D:/AI/workbuddy/2026-08-21-20-15-38/lottery-e2e-plan.html)

## 一句话结论
用 **Playwright** 做 e2e（非 Cypress），先给后端加 `DATA_DIR` 环境变量隔离防污染 `lottery.db`，现有功能拆出 **~20 条主链路用例**，其中 **3 处** 一写测试就会暴露数据逻辑缺口。

## 关键模块
- 工具选型：Playwright（UI + API 一把梭）
- 隔离：Phase 0 给 `server/db/init.js` 加 `DATA_DIR` 覆盖（约 5 行）
- 用户端 `/` 7 条 / 管理端 `/admin` 7 条 / 数据一致性 3 条
- 落地 1.5–2 人日，阶段 P0–P5，CI 走 GitHub Actions

## 写测试必暴露的 3 个风险点（待 y 拍板：设计如此 / bug）
1. 防重复抽奖未生效：`/draw` 只收 category、插 `user_id=0`，从不置 `users.has_drawn=1`（lottery.js:116）
2. 中奖名单/领奖信息/导出可能恒空：admin.js 多处用 `lottery_records.user_id=users.id` JOIN，但记录 user_id 恒为 0
3. 抽奖随机性：`Math.random()` 选奖，边界用例靠 seed / admin 接口降库存或券池

## 来源
- 基于 `D:/AI/workbuddy/抽奖/` 真实代码审计（server/routes/*、server/db/init.js、client/src）
