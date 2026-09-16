# HRBP 求职作品集 v2（优化版）

> 线上：https://yitongcaii.github.io/activities-html/portfolio/hrbp-portfolio-v2.html
> 源文件：D:\AI\workbuddy\2026-08-25-18-13-30\hrbp-portfolio-v2.html

基于 v1（hrbp-portfolio.html，第15轮 `0ea371d`）做的视觉/交互优化版，**原文件保留不动**。

## 本轮改动（v2）
- hero 加电话/邮箱联系入口（**已填简历真实值**：电话 18665138212 / 邮箱 18665138212@163.com，内容取自 `D:\AI\workbuddy\2026-08-21-19-34-05\resume-cai.html` 与简历 docx 文件名）；位置演进：先放名字下 → 再移到"求职方向"标签后 → **最终改为与"求职方向：HRBP"同一行左右分布**（`.hero .role` 加 `display:flex;justify-content:space-between`，左方向、右电话·邮箱），commit `abe4fb3`
- 修 hover 硬编码紫 bug：`.ed:focus` / `.lnk:hover` 改为中性灰 / `var(--ac)`，不再永远紫色
- 去 hero 右上白光晕装饰（`:after`）
- 卡片 hover 去掉 `translateY` 上浮（原暗示"可点"但卡不可点，误导），仅留阴影加深
- hero padding 略回血（16/14 → 22/20）以容纳联系行，仍保持窄
- localStorage key 前缀 `hrbp_` → `hrbp2_`，避免与 v1 同域串数据

## 未动（保留 v1 既有）
- 七彩分区色系、数据墙 60px 大数字、案例卡徽章、emoji 页脚、内容结构（AI 区与工具区重复未合并）
