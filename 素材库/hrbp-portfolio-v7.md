---
标题：HRBP 求职作品集 v7（每页撑满屏 + 链接/海报替换）
来源：蔡女士 HRBP 求职作品集（编辑式 Editorial Dossier 系列第 7 版）
生成日：2026-08-26
线上：https://yitongcaii.github.io/activities-html/portfolio/hrbp-portfolio-v7.html
本地：D:\AI\workbuddy\2026-08-25-18-13-30\hrbp-portfolio-v7.html

## 设计方向
沿用 v3–v6 的 Editorial Dossier（墨黑/纸白/朱红、衬线+黑体、零圆角阴影渐变、细线+留白），本版只做三处定向改动，不重新设计。

## 本轮改动（v6 → v7，v1–v6 全部原文件保留）
1. **每页适应一屏、无需下滑**：`.page` 加 `min-height:100vh` + `display:flex;flex-direction:column;justify-content:center`（内容垂直居中、紧凑落在一屏内）；数据墙数字略缩（82–92px）防溢出、招聘案例 `align-items:center`、各页间距微调；移动端（≤860px）`min-height:auto` 退回自然高度（避免手机上强行撑满导致空白过大）。
2. **删 footer 那句话**：`📌 本页由 yitong 沉淀整理` 整行删除（用户要求"这句话都删掉"）。
3. **文化活动页链接/素材替换**：
   - "维护活动实操手册"（hr-activity-playbook.html）→ **精简版知识库首页** `index-trimmed.html`（旧作品集"知识库完整目录"链接，已验证 200），文案"文化活动知识库 · 精简版"。
   - "文化文档地图"（culture-doc-map.html）→ **我做的海报**：新增 `.poster-card` 纸底细线图卡，用 `resume/portfolio-assets/culture-week.jpg`（业务连续性文化周海报，已验证 200），点击放大；与视频卡同源。
   - 视频（培训积分商城 / 知识库 Demo）保留。

## 链接资产校验（200）
- 知识库精简版首页：https://yitongcaii.github.io/activities-html/index-trimmed.html
- 海报图：https://yitongcaii.github.io/activities-html/resume/portfolio-assets/culture-week.jpg
- 其他页链接（薪酬看板 comp-salary-diagnosis.html、培训看板 bowen_dashboard、3 个 mp4 封面）沿用 v6 已校验项。

## localStorage 隔离
key 前缀 `hrbp7_`（不串 v1–v6）。

## 提交
commit `9c5ebaf`（bare 仓 `git show origin/main` 校验：min-height=1、footer 移除、index-trimmed 替换、旧链接归零、poster-card=1、culture-week.jpg 就位）。
