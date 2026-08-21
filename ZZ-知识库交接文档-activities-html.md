---
title: ZZ-activities-html 知识库交接文档（副本）
tags: [交接, 索引, 活动/ai, meta]
type: 自动化采集
date: 2026-08-07
---

> ⚠️ **本文件是 workspace 主文档的完整副本（非纯指针）**。主文档修改后须在**同一回合**同步更新本副本，避免内容过期。
> 主文档：[`activities-html-知识库交接文档.md`](file:///C%3A/Users/v_yitcai/WorkBuddy/20260728154244/%E4%BA%A4%E6%8E%A5%E6%96%87%E6%A1%A3/activities-html-%E7%9F%A5%E8%AF%86%E5%BA%93%E4%BA%A4%E6%8E%A5%E6%96%87%E6%A1%A3.md) ｜ [在 Obsidian 打开](obsidian://open?path=C%3A%5CUsers%5Cv_yitcai%5CWorkBuddy%5C20260728154244%5C%E4%BA%A4%E6%8E%A5%E6%96%87%E6%A1%A3%5Cactivities-html-%E7%9F%A5%E8%AF%86%E5%BA%93%E4%BA%A4%E6%8E%A5%E6%96%87%E6%A1%A3.md)

---
title: 知识库交接文档 · 文化活动 HTML 资产体系
tags:
  - 协作手册
  - 方法
  - 自动化
  - 活动/ai
source: "C:/Users/v_yitcai/WorkBuddy/20260728154244/交接文档/activities-html-知识库交接文档.md"
---

# 交接文档 · 文化活动 HTML 资产体系（activities-html）

> 写给**完全没上下文的新会话**。读完这份 + `MEMORY.md`，你就能接手。
> 最后更新：2026-08-06（目录改名 + 主持稿 v5 + Obsidian 自动沉淀规则）

---

## 0. 一句话背景

用户 **y（蔡依彤 / v_yitcai，腾讯云技服 HRBP+PM）** 在搭建一套**文化活动标准文档体系**，把活动策划经验做成**可复用、可交互的 HTML 资产**，托管在 GitHub Pages 公开站。AI（小柚）负责把她的原始素材和指令落成 HTML 并上线。

核心活动类型：**员工大会** 与 **Offsite（异地深度研讨）**，每类都有标准三件套；外加创意实验室、物料手册、讲师提纲、Open Day、下午茶研讨等衍生页。

---

## 1. 我们在做什么（项目目标）

- **文化活动三件套（铁律，详见 §7）**：`SOP`（标准流程表）/ `Rundown`（当天分钟级剧本）/ `Checklist`（按阶段打勾防漏）。员工大会、Offsite 各一套。
- **衍生可交互 HTML**：创意实验室（vol1-5）、物料 Banner&PPT 手册、平滑切换模板、讲师提纲、Open Day 互动设计、下午茶研讨、公司活动案例借鉴、英雄风云录 Offsite demo 等。
- **交付形式**：全部单文件自包含 HTML（内联 CSS/JS），活泼配色（多巴胺撞色，非模板化），可编辑（contenteditable）+ 本地自动保存，能直接浏览器打开 / GitHub Pages 访问。
- **托管**：统一在 `https://yitongcaii.github.io/activities-html/`。

---

## 2. 仓库与环境（硬事实，务必记死）

| 项 | 值 |
|---|---|
| **主仓（对外公开）** | `yitongcaii/activities-html`（public + Pages 已开） |
| **本地源** | `D:\activities-html-yitongcaii` |
| **推送方式** | **SSH**（公钥已加 yitongcaii 账号）。`remote` 已是 SSH，**直接 `git add . && git commit -m "..." && git push`，不碰 token、不碰 gh auth** |
| **退役仓 1** | `yutttyi/activities-html`：2026-07-29 设 **PRIVATE + 停用 Pages**（对外 404）。根因：yutttyi 套餐不支持私有 Pages |
| **退役仓 2** | `yutttyi/hero-offsite`：2026-08-03 内容迁移到 yitongcaii 后，**DELETE 其 Pages** 停用（对外 404） |
| **当前对外出口** | **仅 `yitongcaii.github.io/activities-html/`** —— yutttyi 账号下已无任何公开 Pages |
| **gh 登录态** | 当前 `gh` 登录的是 yutttyi 账号，但**与 yitongcaii 推送无关**（走 SSH）；不要因为它就以为要重新登录 |
| **Git / bash** | `D:\AI程序\Git\bin\bash.exe` 跑 `gh`/`git`；gh 装在 `D:\tools\gh\bin\gh.exe` |
| **本地预览** | `python -m http.server 8126 --directory D:\activities-html-yitongcaii`，然后开 `http://localhost:8126/<file>` |

⚠️ **上传 GitHub / 任何对外公开动作，先确认。** 这是用户对外出口，误发会暴露。

---

## 3. 当前资产清单（活跃仓 `D:\activities-html-yitongcaii`，全量 44 个 HTML）

```
# 导航与总览
index.html / index-proto.html / culture-doc-map.html

# 员工大会（含主持稿）
staff-meeting/staff-meeting-design.html        环节设计 SOP
staff-meeting/staff-meeting-design-proto.html  环节设计·原型
staff-meeting/staff-meeting-highlights.html    环节亮点
staff-meeting/staff-meeting-highlights-proto.html
staff-meeting-games.html         暖场互动库
staff-meeting-notices.html       全周期注意事项（责任到人）
staff-meeting/host-notes.html    主持 notes（与根目录同步）
host-notes.html                  主持人注意事项
host-script-single.html          单主持串词
host-script-double.html          双主持串词

# Offsite
offsite/offsite-design.html / -proto
offsite/offsite-highlights.html / -proto
offsite/offsite-after.html / -after-tips.html
offsite/hero-offsite.html        英雄风云录 Offsite demo（8-03 从 yutttyi 迁来）
offsite-activities.html           活动素材库（根目录）

# Open Day / 标杆 / 午后茶
openday.html  cases.html  afternoon-tea.html
activity-cards-hrbp.html         ⚠️ 重定向页（自动跳 index）

# 物料（material/）
banner-ppt.html  smooth-switch.html  smooth-switch-demo.html

# 创意实验室（creative/）
vol1~vol5.html  idea-cards.html  quality-stability-analysis.html

# 讲师提纲（trainer/）
trainer-brief-bowen.html / -shared.html / -v2.html / -v3.html  trainer-poster.html

# 博闻看板 / 反馈（8-04 新增，根目录）
bowen_dashboard_20260804_1538.html
bowen_dashboard_20260804_2052.html
bowen_feedback_20260804_1749 (7).html
bowen_feedback_20260804_1749 (8).html
```

> 标「·原型」的是可交互原型（偏演示），同名无 proto 的是正式 SOP/亮点版。
> `trainer/` 四版提纲 + 海报，可能需合并（待用户决定）。
> 实时全量以仓目录 `find . -name '*.html' | sort` 为准。
> **全部 44 个 HTML 均含统一页脚**（见 §6 第 10 条硬约束）。

---

## 4. 已完成的事（时间线）

- **2026-07-22**：文化活动文档体系搭建。员工大会三件套 + Offsite 三件套（初版框架被否 → 重做定位"异地深度研讨"，框架 Why→立项→logistics→内容→冲刺→当天→会后）。
- **2026-07-24**：确认可视化交付物偏好 **HTML**；修复 GitHub Pages 中文文件名 404 坑（全 ASCII 重命名 + `.nojekyll`）。
- **2026-07-28~29**：`yutttyi → yitongcaii` 迁移收口。建新仓、ASCII 重命名、开 Pages、旧仓设私有+停用。
- **2026-07-31**：**HRBP 锦囊拆分**。
  - Open Day 卡片块并入 `openday.html` 第四章（7 张卡，玩法+坑点双行）。
  - 下午茶研讨独立成 `afternoon-tea.html`（轻互动 9 卡 + 轻研讨 5 卡）。
  - 多轮精简：活泼配色 → 2 列 → 去序号角标 → 删 toolbar/页脚可编辑句 → 删节奏/规模/人均成本 meta → 删迷你世界咖啡 → 盲盒配对改"结束引子" → 落点·Tip→Tip → 删底部 note。
  - `activity-cards-hrbp.html` 改为**重定向**到总目录（commit `8a41a02`）。
- **2026-08-03**：`hero-offsite` 收口（commit `4d9308b`）。内容迁到 `offsite/hero-offsite.html` + index 加入口；DELETE yutttyi 旧 Pages（已 404）。**yutttyi 彻底无公开站。**
- **2026-08-05**：
  - **主持稿精修收口**（commit `a74673a`）：3 份 `-refined` 精修版覆盖原版 + index 补 g/h/i 入口 + 删无实质 `hero-offsite-README.md`。
  - **主持稿二轮精修**（commit `75589e6`）：删必做/选做标签 + 删时间轴必/选列 + 节奏收紧 + KEY 升 v2。
  - **页脚全量补齐**（commit `ec77b13`）：发现 44 个 HTML 仅 10 个有页脚，批量补齐剩余 34 个，现 **100% 覆盖**。
  - **音乐库入目录 + 补页脚**（commit `a88c65e`）：`music-gallery.html`（流程音乐库）补统一页脚并加入 `index.html`/`index-trimmed.html` 目录入口（素材组编号 e/c）。
  - **主持稿删流程时间轴**（commit `c4f12e4`）：单人/双人主持稿删整块「流程时间轴」+ 暖场 cue 倒推残留，KEY 升 v4→v5。
  - **精选目录改名 + 好的 Offsite 链画报版 + 删原型卡**（commit `e0f721f`/`1e402f4`）：`index-trimmed.html` 卡片显示名对齐文件语义（好的员工大会/注意事项/单主持串词等）；②a 改链画报版 `offsite/offsite-design-proto.html`；删员工大会+Offsite 共 4 张「·原型」卡，两组副标题去"原型"。
- **2026-08-06**：
  - **Obsidian 沉淀规则切换为自动**：每个 HTML 交付默认自动写 Markdown 笔记进 vault（不再逐次询问）；沉淀格式=Markdown 嵌链接、不留 HTML 副本；文件名稳定、用户让我改文档即 Edit 覆盖同名笔记。
  - 补沉淀 backlog：单/双主持串词、精选目录、流程音乐库 均已落 vault `素材库/` + 根目录。

---

## 5. 之前卡在哪 + 解决方案（重点）

| # | 卡点 | 解决方案 |
|---|---|---|
| 1 | **GitHub Pages 中文文件名全 404**（Nginx 前端不支持中文路径，连 `.nojekyll` 也救不了） | 全部 ASCII 英文名 + 根目录放 `.nojekyll`；乐享索引等引用也同步改 ASCII |
| 2 | **yutttyi 账号套餐不支持私有 Pages**（`public:false` PATCH 返回 404） | 换 `yitongcaii` 账号（SSH 推送），旧仓设私有+停用 Pages 达成"他人不可查看" |
| 3 | **gh api 端点带前导斜杠被 MSYS 转成路径**（`/repos/...` → `D:/AI程序/Git/repos/...`，invalid endpoint） | 端点**不带前导斜杠**：写 `repos/...` 而非 `/repos/...` |
| 4 | **结构性删除 HTML 卡片后，旧 localStorage 草稿（按旧 KEY）自动覆盖新模板**，用户看不到删除 | 升本地草稿 KEY 版本（如 `afternoon_tea_hrbp_v1`→`v2`），旧草稿失效、直接显示新模板 |
| 5 | **乐享 API 无 iframe/embed block**，交互式 HTML 不能站内内嵌保真（导入会变静态快照） | 用 GitHub Pages 公开链接做"链接卡片" / 或乐享网页端手动插网页组件；API 只能生成 markdown 链接 |
| 6 | **PowerShell 5.1 管道喂 token/长串给 gh/curl 被传脏** | 改「写文件 + `@文件`」或 `--%` 原生透传 |
| 7 | **漏收口一个独立仓库**（`hero-offsite` 是 yutttyi 下另一个仓，不在 activities-html 私有化范围内，一直公开） | 做任何"全部收口"前，**逐项核对账号下所有仓库**，别只盯单个仓 |
| 8 | **PowerShell `Set-Content` 默认 UTF8 带 BOM**，GitHub 拒 BOM | 写 JSON 用 `-Encoding ASCII` |
| 9 | **`ssh-keygen -N ""` 空串被吞致参数错位** | 改用 Git bash `ssh-keygen -N ''` 或 `ssh-keygen --%` |
| 10 | **bash `-c` 内双引号在 PS 会 EOF 报错** | 改单引号包裹或 `ssh --%` 透传 |

---

## 6. 踩过的坑（硬约束 · 别再踩）⚠️ 最重要

1. **GitHub Pages 中文文件名必 404** → 一律 ASCII 命名 + 根目录 `.nojekyll`。**绝不再用中文文件名**。
2. **对外出口只认 `yitongcaii`（SSH）**；`yutttyi` 已退役，不要再往它推或指望它公开。
3. **gh api 端点无前导斜杠**（`repos/...`）。
4. **PowerShell 三坑**：① 管道喂 token 脏 → 写文件+@文件；② `Set-Content` UTF8 BOM → `-Encoding ASCII`；③ `ssh-keygen -N ""` 空串吞 + bash `-c` 双引号 EOF → 用 Git bash 单引号 / `--%`。
5. **乐享 API 不能保真内嵌交互 HTML** → 链接卡片 / 网页端手动 embed。
6. **结构性删除 HTML 内容前要升 localStorage KEY**，否则旧草稿覆盖。
7. **winget 在本机不可用**（Administrator 账户失效别名桩）→ 装 CLI 走 git 自带 curl（`D:\AI程序\Git\mingw64\bin\curl.exe`）+ 抓 `/releases/latest` 302 重定向。
8. **任何对外公开动作（GitHub 推送 / 乐享发布）先确认**，这是用户对外出口。
9. **SOP / 经验库 / Checklist 三者严格分开**，SOP 必须含 Why 层、责任人单一主责（详见 §7）。
10. **每个 HTML 必须含统一页脚**（硬约束）：在 `</body>` 前插入
    ```html
    <footer style="text-align:center;padding:22px 16px;color:#999;font-size:13px;letter-spacing:.5px;">📌 本页由 yitong 沉淀整理 · 文化活动知识库</footer>
    ```
    新增 HTML 第一时间补上；改/重构 HTML 后**必须复查页脚没被覆盖丢失**（结构性替换整文件最易丢）。验收口令：`grep -rl "yitong 沉淀整理" --include='*.html' | wc -l` 应等于 HTML 总数。

---

## 7. 用户偏好与文档铁律（强约束）

**协作风格**
- 极简命令驱动（"要""改了吗""补"），中文，结论先行，不喜冗长解释。
- 先给方案再迭代；可编辑字段（如活动主题）保留人工所有权，AI 不乱改。
- 排错：直接贴错误码/截图，要求直接修且不破坏现有功能，改完必须验证闭环（"改了吗"是硬关卡）。
- 视觉：活泼有活力、排斥模板化；UI 像素级敏感；优化策略"先删减后修复"。
- 重复方案高度厌恶；对单一主题倾向多轮深挖（"还有别的吗"）。

**文化活动文档铁律**
- **三件套各司其职**：SOP = 环节/内容/责任人/完成标准 的流程表；Rundown = 当天分钟级剧本（时间/环节/负责人/时长/物料）；Checklist = 按阶段打勾防漏。不能混成一坨或写成知识库式方法论。
- **SOP 必须含 Why 层**：先"厘清 Why"再定 How/What，不能只写做什么。
- **严格区分 SOP 与经验库/避坑点**：避坑点归档只是配套经验，不能叫 SOP。
- **单一主责人**：每条事项只挂 1 个主责人标签（被合并角色视为其下属/配合方不单列）。总责归主 PM、执行对接归子 PM、技术归控台、行政协调归秘书、异地驱动归业务接口人。
- **Offsite 必含异地 logistics**（交通/住宿/跨城物料/应急预案），定位"向内收·小圈·深度产出"；员工大会"对外扩·全员"。

**可视化交付物**
- 用户多次选 **HTML** 作为展示型/参考型交付物（活泼交互：渐变/卡片/滚动动效），优先做 HTML 而非 MD/Word。
- **页脚硬约束**（见 §6 第 10 条）：每个 HTML `</body>` 前必须有 `📌 本页由 yitong 沉淀整理 · 文化活动知识库`，代表依彤本人沉淀。当前 44/44 全覆盖。
- **Obsidian 自动沉淀**（2026-08-06 起）：每个 HTML 交付默认自动写 Markdown 笔记到 `C:\Users\v_yitcai\Documents\Obsidian\活动\`（按类型子目录如 `素材库/`），正文嵌 Pages 线上链接 + 本地路径；源 HTML 不删、vault 不留 HTML 副本；文件名固定、用户让我改文档即覆盖同名笔记（不新建重复笔记）。

---

## 8. 当前状态 / 未决 / 待办

**已完成收尾**
- afternoon-tea 最终态：2 列卡片、活泼多巴胺配色、去顶部彩条、去序号角标、删 toolbar、删页脚可编辑句、删节奏/规模/人均成本 meta、删迷你世界咖啡、盲盒配对改结束引子、落点·Tip→Tip、删底部 note。KEY=`afternoon_tea_hrbp_v2`。
- activity-cards-hrbp 重定向到 index。
- hero-offsite 收口（迁 yitongcaii + 停用 yutttyi Pages）。
- 主持稿精修：精修版替换原版 + index 补入口 + 删必做/选做标签 + 节奏收紧 + KEY 升 v2（commit `a74673a`/`75589e6`）。
- **页脚 100% 覆盖**：44 个 HTML 全部含统一页脚（commit `ec77b13`）。
- 音乐库 `music-gallery.html` 补页脚 + 进目录（index/index-trimmed 素材组 e/c）。
- 主持稿 v5：删流程时间轴区块，KEY 升 v5（commit `c4f12e4`）。
- 精选目录 `index-trimmed.html`：卡片显示名对齐文件语义、②a 好的 Offsite 链画报版、删员工大会+Offsite 共 4 张「·原型」卡（commit `1e402f4`）。
- Obsidian 沉淀规则改为自动（见 §7）。

**可能待办（未决，等用户拍板）**
- `trainer/` 四版提纲 + 海报是否合并。
- 是否要一份"总览页 HTML"或导出清单文件（之前已给过纯文本链接汇总）。
- 其他活动类型（团建 57 套方案池、AI 文化周、英语角等）是否继续做 HTML。

---

## 9. 给新会话的接手清单（第一步做什么）

1. **先读**：本文件 + `MEMORY.md`（`.workbuddy/memory/MEMORY.md`）。
2. **要改/加 HTML**：在 `D:\activities-html-yitongcaii` 操作，文件名 ASCII，引用资源用相对路径，根目录已有 `.nojekyll`。
3. **本地验证**：`python -m http.server 8126 --directory D:\activities-html-yitongcaii` → 开 `http://localhost:8126/<file>` 检查。
4. **提交推送**：`git add . && git commit -m "..." && git push`（SSH，无需登录）。
5. **线上生效**：GitHub Pages 约 1 分钟生效，改完用 `web_fetch` 验一下线上链接。
6. **改完必须回用户验证闭环**，结论先行、简短汇报 commit 号与改动点。
7. **任何对外公开 / 删除旧页 / 改仓库设置** → 先确认。

---

*附：本交接文档由小柚基于 `MEMORY.md` 与各日日志整理，2026-08-06 刷新至最新落地状态。*
