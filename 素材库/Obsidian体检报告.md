---
tags: [meta, 知识库, 体检]
type: 分析报告
date: 2026-08-13
source: 小柚 Obsidian 只读扫描
---

# Obsidian 体检报告（2026-08-13）

> 131 篇笔记的只读体检，按严重度分级。完整可视化报告见 HTML：
> [Obsidian 体检报告（Bento 卡片版）](file:///C:/Users/v_yitcai/WorkBuddy/2026-08-13-10-55-50/obsidian-audit.html)

## 一句话结论
链接断裂（采集库缺 `知识采集库/` 前缀）+ 嵌套 `.obsidian` 污染 是 P0；孤儿率 49.6%、tag/命名混乱、跨目录副本是 P1。

## 关键发现
- 🔴 P0 ①：知识采集库 15 处真实断裂链接（索引→汇总全断，run 卡回链也断），根因是链接用 `[[素材/...]]` 缺 `知识采集库/` 前缀。
- 🔴 P0 ②：嵌套 `.obsidian` 配置污染，含 `活动/Templates/.obsidian/.obsidian/.obsidian` 三重嵌套。
- 🟡 P1 ③：孤儿笔记 65/131（49.6%），45 张采集 run 卡成孤岛。
- 🟡 P1 ④：Tag 中英+大小写重复（offsite/Offsite、staff-meeting/员工大会、icebreaker/破冰、"Open Day" 拆成 Open+Day）。
- 🟡 P1 ⑤：run 卡命名混用「slug+第N批」与「中文名+第N轮」。
- 🟡 P1 ⑥：员工大会去中心化 / 实时投票词云 跨目录重复（已于 2026-08-13 合并去重；活动笔记模板经确认无用同期删除）。
- 🟢 P2 ⑦：12 篇手写策划文档无 frontmatter。
- 🟢 P2 ⑧：缺顶层统一入口 MOC。
- 🟢 P2 ⑨：模板/示例占位符被当真实 wikilink。
- 🟢 P2 ⑩：元数据字段不齐（updated 43/119、created 5）。

## 修复优先级
先修 ①②（批量替换链接前缀 + 清嵌套 .obsidian），再统一 tag 字典与命名模板，最后补 MOC 与 frontmatter。

## ✅ 修复记录（2026-08-13 已做）
- **P0① 已修并验证**：知识采集库内 17 处 `[[素材/...]]` → `[[知识采集库/素材/...]]`。复验全库 213 条 wikilink，**真实断裂 = 0**。
- **P0② 更正理解**：原以为"活动/.obsidian 是污染"，实际是**根 `Obsidian/.obsidian` 被改名成 `.obsidian_bak`**（仅 208B app.json），用户用的就是 `Obsidian` 根 vault。已恢复 `Obsidian/.obsidian`（复制 `_bak`）。`活动/.obsidian` 是嵌套配置、根 vault 打开时自动忽略，留着无害（已还原，未删）。`活动/Templates` 三重嵌套 `.obsidian` 确为纯污染，已移出（备份在 `kb_backup`）。
- 备份位置：`C:\Users\v_yitcai\WorkBuddy\2026-08-13-10-55-50\kb_backup\`（可一键还原）。
- 未做：P1（孤儿/tag/命名/重复）与 P2（占位符 8 处、无 frontmatter 12 篇、缺 MOC）。
