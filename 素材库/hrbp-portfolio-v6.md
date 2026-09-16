---
title: HRBP 作品集 v6（分栏编辑式 · 提白 + 高级感 + 视频卡兼容）
---

- 线上：https://yitongcaii.github.io/activities-html/portfolio/hrbp-portfolio-v6.html
- 源文件：D:\AI\workbuddy\2026-08-25-18-13-30\hrbp-portfolio-v6.html
- 设计方向：沿用 v3/v4/v5 的 Editorial Dossier（墨黑+纸白+朱红、零圆角阴影渐变、细线+留白+衬线），仅做精修，不重新设计。

## 本轮改动（第二十二轮，v6 / 基于 v5）
1. **主色调再提白**：纸色 `#FAF8F2`（v5）→ `#FCFBF8`，次级底 `#EFEBE0`→`#F2EFE8`，去黄更干净；分隔线统一为发丝线 `rgba(26,26,26,.14)`（`--hair`），不再用实黑 `--line` 做普通分隔（仅保留在封面/大数字/Before-After 块的强调边框）。
2. **分栏整体"高级感"提升**：
   - 目录栏分隔线由实黑改为发丝线 `--hair`，去掉 `active` 的灰底块（`.paper2` 填充），改纯左侧细红线 + 文字转黑，更克制；栏内 padding 加大（36→46px 顶 / 24→30px 侧）。
   - **每页补编辑式大标题**：新增 `.phead > .ptitle`（衬线 clamp 30→52px），每页 = 红色小编号 + 大衬线标题 + 内容，建立清晰层级（之前只有小标签直接进内容，缺标题是"不高级"主因）。
   - 内容页 padding 加大（顶 46→50/98、侧 26→28/88）。
3. **视频封面与卡片兼容**：`.vcard` 去黑底（`background:var(--ink)`）→ 改为 `background:var(--paper)` + `1px solid var(--line)` 细线卡，与全页其他卡片同源；播放红点缩到 56px、hover 改为轻微 `brightness(.96)` 而非黑块。3 个视频封面（ai-comic / training-points / knowledge-base）保留。
4. 数据、链接、联系方式、localStorage（`hrbp6_` 隔离）均沿用 v5，无回归。

## 可靠性锚点
- 视频资源与封面图真实路径（Pages CDN）：resume/portfolio-assets/{ai-comic,training-points,knowledge-base}.mp4 + -01.jpg
- 作品链接 200：hr-activity-playbook.html / culture-doc-map.html / bowen_dashboard_20260804_2052.html / salary/comp-salary-diagnosis.html（salary-sample.html 是死链，已避开）
- 联系方式真实值：电话 18665138212 / 邮箱 18665138212@163.com（取自 resume-cai.html）
