---
title: 新人答辩自动化 MCP 应用设计方案
tags: [活动应用, MCP, 自动化]
source: C:/Users/v_yitcai/WorkBuddy/20260728111214/新人答辩MCP应用设计方案.md
version: v1.1
---

# 新人答辩自动化 MCP 应用设计方案

> 版本：v1.1 ｜ 设计：MCP 构建专家 ｜ 适用：部门新人发展答辩（每月 1 日触发，按小组维度聚合拉群）
> 现状基线：命名规范「新人发展答辩」、抓 HR 新人看板、客服号通道自动拉群；流程变更（2026-08-03）= 不再自动建会（**特例**：转正前 5 天跟进若导师回复"未安排"，则本分支自动建次日会 + 自动回填会议号/链接，见 §5），会议由导师自建、导师为第一责任人，排除竞业 / 干部，拉群按「部门 + 中心 + 小组」维度聚合。

---

## 1. 目标与范围

把「每月新人答辩」从人工排期 + 手动拉群，升级为 **WorkBuddy 定时编排 + 客服号通道自动拉群** 的闭环：

- ✅ 每月 1 日自动识别本月应答辩新人（来自企微新人看板，排除竞业 / 干部）
- ✅ **按「部门 + 中心 + 小组」维度聚合**，把同组新人 + 各自导师拉进同一个群（不再一人一群）
- ✅ 群内自动推送「新人发展答辩 · 组织说明」话术（适用规则 / 导师第一责任人 / 答辩流程 / PPT 模板）
- ✅ （可选）回填看板「组织方式」列
- ✅ 生成月度答辩汇总报告

> ⚠️ **流程变更（2026-08-03）**：自动化**不再创建腾讯会议**（**特例**：转正前 5 天跟进分支，若导师回复"未安排"且经 y 转述，Agent 自动建"次日"会议并回填会议号/链接，见 §5 未安排→自动建会分支），其余场景会议由导师自建、自行组织；导师为答辩第一责任人，负责邀中心负责人 + 新人、全程录屏并回传 HR（会议不邀 HR）。原「自动建会 + 归集录制 + 归档微盘」链路移除。

---

## 2. 三产品角色定位

| 产品 | MCP 中的角色 | 提供的能力 | 对应已连 MCP 工具 |
|------|-------------|-----------|------------------|
| **WorkBuddy** | MCP Client / 编排大脑 | 每月 1 日定时触发、按业务规则串工具、持有评委/命名/归档配置 | 自动化(recurring) + MCP Client |
| **企微** | 数据面 + 触达面 + 归档面 | 新人看板(智能表格)、消息通知、企微文档/微盘归档 | 企微机器人 MCP：smartsheet_* / create_doc / upload_doc_file / edit_doc_content |
| **腾讯会议** | 答辩会场 | 预约会议、参会人、录制、转写、智能纪要 | 腾讯会议 MCP：schedule_meeting / meeting_invitees_add / get_transcripts_* / get_record_addresses / get_smart_minutes |

**关键判断**：现有两个产品 MCP 已覆盖 80% 能力，但直接让 Agent 拼装会有「批量/固定会议室/微盘」三处缺口。因此新增强一个**领域层 MCP Server `mcp-server-rookie-defense`**，把业务规则封装成高层工具，内部直连企微开放 API + 腾讯会议开放 API（REST），把三个待验证项变成该 Server 的明确实现点 + 回执校验。

---

## 3. 整体架构（分层）

```
┌─────────────────────────────────────────────────────────────┐
│  WorkBuddy（MCP Client / 编排大脑）                          │
│  · 每月1日 recurring automation 触发                          │
│  · 按业务规则顺序调用下方 MCP 工具                            │
└───────┬──────────────────────┬──────────────────┬───────────┘
        │                       │                  │
┌───────▼────────┐   ┌─────────▼─────────┐  ┌─────▼──────────────┐
│ mcp-server-    │   │ 腾讯会议 MCP       │  │ 企微机器人 MCP      │
│ rookie-defense │   │ (会场/录制/转写)   │  │ (智能表格/消息/文档) │
│ 【本次新建】   │   │  [已连]           │  │  [已连]            │
│ 领域工具+规则  │   └─────────┬─────────┘  └─────┬──────────────┘
└───────┬────────┘             │                  │
        │ 直连 REST（批量/会议室/微盘实现点）        │
┌───────▼─────────────────────▼──────────────────▼──────────────┐
│  企微开放平台 API ｜ 腾讯会议开放平台 API ｜ 企微微盘/文档存储     │
└───────────────────────────────────────────────────────────────┘
```

**数据流（一次月度运行）**：
`新人看板(企微智能表格)` → `fetch_rookie_defense_list(排除竞业/干部)` → `create_defense_groups(客服号建群+小组聚合)` → `send_group_notice(四段式话术)` →〔导师自建会议/答辩〕→（可选）`sync_board_org(回填组织方式)` → `generate_defense_summary(月报)`

---

## 4. MCP Server 工具设计（`mcp-server-rookie-defense`）

> 6 个高层工具，命名即意图，参数全部 Zod 校验，输出结构化 JSON。

### T1 `fetch_rookie_defense_list`
- 入参：`{ month: string(YYYY-MM), status?: "pending"|"done" }`
- 行为：读企微智能表格「新人看板」，过滤本月应答辩新人
- 出参：`[{ employee_id, name, department, center, team, mentor: string[], entry_date, defense_type }]`（mentor 为数组，支持多名导师）

### T2 `create_defense_groups`　【覆盖：按小组聚合拉群】
- 入参：`{ rookies: Rookie[], group_by: ["department","center","team"], chat_name_template:"新人发展答辩沟通群-{center}-{team}" }`
- 行为：把 `rookies` 按 `部门 + 中心 + 小组` 聚合，**同一小组的新人 + 各自导师（多名导师去重合并）拉进同一个群**（客服号通道 `chat/create`，客服号自动进群）；群命名 `新人发展答辩沟通群-<中心>-<小组>`；幂等防重——本月已建的群跳过，不重复建
- 出参：`[{ group_key, chat_id, chat_name, members: [] }]`
- ⚠️ 说明：拉群前先剔除竞业 / 干部（见排除规则）；会议不由系统创建，导师自建

### T2' `create_defense_meetings`（可选，v1 不默认调用）
- 原「批量建会 + 自动录制」链路（2026-08-03 变更：**自动化不再建会**，会议由导师自建）。腾讯会议 MCP 建会能力保留备用，仅当后续需系统代建时才调用；v1 流程不触发。

### T3 `invite_defense_participants`
- 入参：`{ meetings: { employee_id, meeting_id, name?, mentors?: string[] }[], reviewers?: string[], notify_newcomer: boolean }`
- 行为：`meeting_invitees_add` 把**评委（主持人）** + **每位新人的导师（支持多名）** + **新人本人** 一并邀请入会；企微消息通知（聊天消息当前被企业禁用，真实触达以会议邀请为准）
- 出参：`{ hostInvited: [], attendeeInvited: [], notified: [] }`
- ⚠️ 说明（2026-07-29 真实建会实测修正）：用蔡依彤个人 token 调用 `schedule_meeting` 时，返回 `hosts` 直接为蔡依彤本人 —— **评委天然即为会议主持人，无需 host transfer**。此前"建会者为机器人、主持人为机器人"的假设不成立（那仅在改用机器人/服务账号 token 建会时才成立）。故 v1 可让评委直接主持，约束收窄为"仅当改用服务账号 token 建会时才需处理主持人转移"。

### T4 `collect_defense_artifacts`
- 入参：`{ meeting_codes: string[], after_date: string }`
- 行为：拉录制地址 + 转写段落 + 智能纪要
- 出参：`[{ meeting_code, recording_url, transcript, minutes }]`

### T5 `archive_defense`　【覆盖：微盘写入】
- 入参：`{ artifacts: Artifact[], target: { type:"wedoc"|"wedisk", folder_id } }`
- 行为：上传到企微文档(兜底)或微盘(验证后切换)统一目录
- 出参：`[{ meeting_code, archive_url }]`
- ⚠️ 验证点：微盘写入权限

### T6 `generate_defense_summary`
- 入参：`{ month: string }`
- 行为：汇总生成企微文档月报
- 出参：`{ doc_url }`

---

## 5. 端到端工作流（WorkBuddy 编排）

每月 1 日 09:00（recurring automation，rrule：`FREQ=MONTHLY;BYMONTHDAY=1;BYHOUR=9;BYMINUTE=0`）：

1. `fetch_rookie_defense_list({ month })` → 本月应答辩新人 = **看板「答辩状态=待安排」**（HR 每月初前标记；**排除竞业 / 干部**）
2. **按 `部门 + 中心 + 小组` 聚合**：同一小组的新人 + 各自导师（多名导师去重合并）拉进**同一个群**（群命名 `新人发展答辩沟通群-<中心>-<小组>`）；幂等防重——本月已建的群跳过
3. 群内发「本月新人发展答辩沟通」话术（模板见下，含核心目的/答辩流程/PPT 模板/导师自检清单；适用范围：竞业/干部不述职）
4. （可选）回填看板「组织方式」列
5. `generate_defense_summary({ month })` → 月报推送至部门群

> 注：会议由导师自建，自动化不建会、不归集录制；导师答辩后录屏回传 HR。

### 拉群话术模板（2026-08-03 最终版，⚠️ 已按看板字段推送测试群 ww231306481776832 验证）

> **占位符 → 看板字段映射**：`<中心>`=看板「中心」、`<组>`=看板「组」、`<导师>`=看板「导师」(多名顿号分隔，发送时拆分为多个 @mention)、`<新人>`=看板「姓名」(发送时转为 @mention)。
> **@ 规则**：`<导师>` 处 @ 所有导师（RTX）、`<新人>` 处 @ 该新人；均用 userid。
> **表情**：原文 `[玫瑰]` 在企微文本 API 不自动转义，实际发送以 🌹 呈现。

```
【本月新人答辩安排--<中心>-<组>】

各位好～本群为本月新人发展答辩的沟通群。为提高答辩组织效率，由小组自行组织新人答辩，完成新人转正闭环；🌹各位导师作为新人成长的【第一负责人】<导师>，请在新人转正前7天，跟进完成以下答辩事宜：

一、新人答辩的核心目的：
关注新人成长，给新人提出未来发展建议

二、答辩流程（共计 20 分钟） ：
15 分钟答辩陈述 + 5 分钟评委 QA 环节

三、答辩 PPT 模板 ：
请<新人>按此模板准备答辩材料：
https://doc.weixin.qq.com/slide/p3_AR8AfgYaAOYCNgPdVfWjaSCmARYrp?scode=AJEAIQdfAAouSz0i1JAaUAsQZIADc
四、导师自检清单（✅ 答辩前请逐项确认）：
 🔴【参会邀请】邀请中心负责人、leader 参与本次新人答辩（若无需要，可以不用邀请HR）
 🔴【会议录制】开启自动录制❗，将录屏回传给 v_yitcai
 🔴【信息保密】若多位新人一同答辩，请导师注意回避非自己带教的新人的答辩内容

以上请各位知悉，如有疑问随时沟通，辛苦各位！🌹
```

> **相对旧版（2026-08-03 优化版）变更**：① 标题加 `<中心>-<组>` 占位；② intro 加"请在新人转正前7天"+`<导师>`占位（@导师）；③ PPT 处加`<新人>`占位（@新人）；④ 自检「参会邀请」改为"若无需要，可以不用邀请HR"（旧版"会议无需邀请 HR 进会"）；⑤ **移除了旧版「适用范围：竞业/干部不述职」前置段**——该硬规则是否保留待 y 确认，本发送版未含此段。
> 本模板已于 2026-08-03 按看板实时值（中心=test中心、组=test组、导师=christao/eileenyyang、新人=v_yitcai）以 rich_text 内联 @ 推送至测试群 `ww231306481776832` 验证（返回 ok）。

### 答辩进度跟进子模板（2026-08-03 确认版，转正前 5 天触发）

> **触发**：新人转正日前 5 天（由看板「入职日期」+ 公司试用期规则推算；⚠️ 试用期月数待 y 确认，如 6 个月）。话术仅展示"还有 5 天"，不展示具体转正日期。与「每月 1 日拉群」平行，为第二条触发线。
> **占位符**：`<中心>`=看板中心、`<组>`=看板组、`<导师>`=看板导师(多名顿号分隔→多个 @mention)、`<新人>`=看板姓名(@mention)。
> **@ 规则**：`@<导师> @<新人>` 内联于"温馨提醒"之后；均用 RTX(userid)。
> **适用范围段**：跟进话术不含"竞业/干部不述职"前置段——拉群时已剔除该类成员，群内均为应答辩新人，无需重复。

```
【新人答辩进度跟进--<中心>-<组>】

🌹各位好～距离 <新人> 转正还有 5 天，为确保新人转正闭环准时完成，温馨提醒@<导师> @<新人> 确认：
🔷 请问答辩是否已经完成？若已完成，麻烦将录屏回传给 v_yitcai
🔷 若尚未完成，请导师尽快邀请中心负责人、leader在新人转正前完成答辩（15 分钟陈述 + 5 分钟 QA），并开启自动录制

辛苦各位确认~~🌹
```

### 未安排→自动建会分支模板（2026-08-03 拍板，转正前 5 天跟进的下游分支）

> **触发链路**：转正前 5 天跟进话术发出 → 导师群内回复"未安排" → **由 y 转述给 Agent**（客服号纯单向，读不到群消息，印证硬约束）→ Agent **立即**用腾讯会议 MCP 建"触发日第二天"的会议，并发生成话术（建会通知 + @导师 + @所有人）。
> **拍板（2026-08-03 y 指令"1和2都按现在的要求来"）**：① 仅本分支**特例启用系统自动建会**，其余场景仍维持 2026-08-03"导师自建、不自动建会"决策，**不全面回退**；② 建会后**自动回填**看板「会议号/会议链接」列（因建会产生确定值），推翻原"手填"临时约定。
> **占位符**：`<中心>`=看板中心、`<组>`=看板组、`<导师>`=看板导师(多名→多个 @mention)、`<新人>`=看板姓名(@mention)、`<时间>`=建会具体时间、`<会议号>`/`<会议链接>`=建会 API 返回（y 原稿 `<>` 空占位规范为 `<时间>`）。
> **@ 规则**：首段末 `@<导师>`（内联 mentioned RTX 名）；结尾 `@<所有人>`（rich_text @all，⚠️ 格式待实测，之前仅用过指定 RTX）。
> **建会参会人**：建议系统建会默认邀**导师 + 新人**（必邀），中心负责人/leader 由导师后续添加（话术亦引导）；**HR 不进会**（沿用 2026-08-03 决策）。
> ⚠️ 会议时间待定：仅定"第二天"，具体时刻（如 15:00–15:20，答辩 20min）待 y 确认。

```
新人发展答辩旨在帮助新人明确成长方向、提供未来发展建议，同时让团队对新人的成长有更全面的了解。请各位导师积极推进答辩组织工作，并邀请中心负责人、leader 一同参会@<导师>

<新人> 的新人答辩会议已创建 ✅
📅 时间：<时间>
🔗 腾讯会议：<会议号>，<会议链接>

请各位准时参加🌹如需调整会议时间，请及时同步，麻烦各位！@<所有人>
```

---

## 6. 三大待验证项处理方案

| 待验证项 | 现状（基于已连 MCP） | 处理方案 |
|---------|---------------------|---------|
| **批量建会** | 腾讯会议 MCP `schedule_meeting` 为单次接口 | 在 `mcp-server-rookie-defense` 内串行创建 + 退避重试，并发上限 3；先用测试会议验证限流阈值，再上生产 |
| **微盘写入权限** | 企微机器人 MCP **无微盘工具**（仅 `upload_doc_file` 到企微文档） | 先用「企微文档 + 智能表格」作归档兜底；`archive_defense` 用 `target.type` 切换，验证通过再切微盘 |

> **决策（2026-07-28）**：固定会议室 ID 在 v1 **不纳入**。理由：① 普通会议的会议号/链接已足够让新人+评委入会，无需行政分配实体会议室；② 腾讯会议 MCP 不暴露会议室参数，绑定固定室需直连 REST 增加复杂度；③ 录制策略由企业后台接管（与是否固定室无关），固定室不解决录制问题。→ 原「固定会议室」待验证项取消，`create_defense_meetings` 的 `room_id` 改为可选，v1 留空即普通会议；若后续要体验一致性，再作为可选增强补 REST 集成。

### 6.1 已知平台约束（2026-07-29 实测，影响交付边界）

| 约束 | 现状 | 对方案的影响 | 解决路径 |
|------|------|--------------|----------|
| **企微智能表格「多选」** | 已连企微机器人 MCP 的 `smartsheet_add_fields` 不暴露 `is_multiple` 开关，建出的「选择」列实为单选（`is_multiple:false`） | 「导师」列无法做成真·多选下拉 | 退回**文本列**，多名导师用 `、` 顿号分隔，代码读取时拆分并逐一邀请（已落地并验证） |
| **自动录制** | 腾讯会议 MCP `schedule_meeting` 支持 `auto_record_type=cloud` 请求云录制，但企业版录制策略由**企业管理后台**接管 | 代码已传 `auto_record_type=cloud`，但**是否真自动录取决于企业后台是否对该应用/账号开启云录制** | 找 IT 在企业后台给会议应用开启云录制权限；否则需主持人手动点录制 |
| **评委为主持人** | `schedule_meeting` 用个人 token 建会时，`hosts` 直接填为调用者本人 | 评委（蔡依彤）即为会议主持人，**无需 host transfer**；导师与新人作为受邀参会人 | 仅当改用机器人/服务账号 token 建会时主持人才是机器人，届时需创建者转移或由 IT 后台配置。v1 用个人 token 建会，评委直接主持 ✅（2026-08-05 已真实验证） |
| **企微聊天消息触达** | 企业禁用了机器人的「消息/通讯录」权限（`wecom-cli` 报"暂不支持授权"） | 月报/提醒无法以聊天消息弹窗推送 | 触达改走「**会议邀请**（强通知）+ **企微文档**（月报/归档）」；若要聊天消息，需 IT 在后台开机器人「消息」权限 |

> 说明：以上约束均非用户权限问题，而是企业层面的开关/API 能力边界。新人看板、建会、拉人入会、归档文档等核心链路均可走已连 MCP 零凭证跑通；真正卡住的只有「聊天消息推送」与「录制/主持人」两项需 IT 后台配合。

### 6.2 外发审批 + 看板唯一性硬约束（2026-08-04 y 明确，最高优先级）

> **背景**：企微客服号纯单向、无回调，群内无法点"批准"；任何 send/建会若直接执行，可能**发错内容**或**发太多打扰人**。故所有外发动作须先过 y 审批，且**每条单独确认**（不批量"执行全发"）。

**① 看板唯一性（防写错表）**
- 全项目只读写**那一个**新人看板：分享链接 docid=`s3_ALgAH3ghAGgCNaEjl35sfT2aiVbME_a` / MCP 长 ID=`dcibmZH1Gm22woDm_9KS7ywrfHu-Y1KhmE930tI4o7a3NuG79PtoR1ht_Wm4pryvBXns2wtuuaGrhRVtrxuxvh4A` / sheet_id=`q979lj`。
- 代码层硬编码为常量（`wecom_gate.py` 的 `BOARD_MCP_ID` / `BOARD_SHEET_ID`），**禁止新建看板、禁止写其他看板**。y 随时自查，Agent 不抢改；仅 y 明确指令（如未安排→建会回填）才动，且只动这唯一看板。

**② 企微外发逐条审批（防发错 + 防打扰）**
- 任何 `send_message` / `schedule_meeting` 真实调用前，必经 y 审批；**群聊中发出的每一句话术（含 @导师 / @新人 / @所有人、拉群提醒、进度跟进、建会通知、以及未安排/未完成等任意分支话术）都须 y 确认全文无误后才发，逐条单独确认，绝不批量**。
- 实现（`wecom_gate.py`）：
  - `DRY_RUN` 默认 `True`：`propose_message` / `propose_meeting` **只登记不发送**，写入 `pending_wecom_<日期>.json` + 同名 `.md` 供 y 眼审。
  - y 审阅后回「发 #ID」→ Agent 调 `execute(#ID, dry_run_override=True)` 才真发；支持「砍 #ID」丢弃、「改 #ID …」修改后执行、「延 #ID」延后。
  - 同一群多条提醒在清单中合并；y 可对任何一条否决，未说发的永远不发。
- 审批入口只能在 **WorkBuddy 会话**（企微单向无回调，群内无法点批准）。
- recurring automation（每月1日 / 转正前5天）的 prompt 须改为「只 propose 出草稿、等 y 逐条确认」，不得自动 execute。

**④ 看板读取实时性（防用旧快照，2026-08-04 y 明确）**
- 我看板**无自动推送**：y 改了数据我不会自动察觉，须主动读取才知最新。
- 每次 `smartsheet_get_records` 均向企微服务器**实时拉取**，返回当下最新值，无缓存层、无独立「刷新」步骤；但**上下文里记过的旧值会过时，不能信**。
- 规程：**任何 `propose` / 外发 / 建会 / 拉群动作前，必须重新调用 `smartsheet_get_records` 实时拉取**，以最新看板为准，**严禁依赖历史会话快照**做判定或填占位。
- recurring automation 每次运行为新上下文，启动即 `get_records`，天然取最新。

**③ 操作红线**
- 🚫 禁止在未经 y 逐条确认的情况下调用 `send_message` / `schedule_meeting`。
- 🚫 禁止向非看板唯一 ID 的表写入。
- 🚫 禁止依赖历史会话快照做判定/填占位；任何动作前必须重新 `smartsheet_get_records` 实时拉取。
- ✅ 测试群 `ww231306481776832` 仅用于话术验证；真实触发须指向真实群 / 真实看板。

---

## 7. 部署与配置

- 技术栈：TypeScript + `@modelcontextprotocol/sdk` + `zod`（与现有抽奖项目 Node 栈一致）
- 传输：Stdio（本地由 WorkBuddy 拉起）或 SSE（远程）
- 凭证：企微 `CORP_ID/AGENT_ID/SECRET`、腾讯会议 `APP_ID/SECRET`，走环境变量，不入仓
- 业务配置（`config.ts`）：评委 open_id 列表、命名模板、归档 folder_id、触发日

WorkBuddy `mcp.json` 注册（仓库已附 `mcp-rookie-defense/mcp.json`，可直接复制到 `.workbuddy/mcp.json`）：
```json
{
  "mcpServers": {
    "rookie-defense": {
      "command": "node",
      "args": ["dist/index.js"],
      "cwd": "c:/Users/v_yitcai/WorkBuddy/20260728111214/mcp-rookie-defense",
      "env": {
        "WECOM_CORP_ID": "",
        "WECOM_AGENT_ID": "",
        "WECOM_SECRET": "",
        "WECOM_ROOKIE_BOARD_DOCID": "",
        "WECOM_ARCHIVE_FOLDER_WEDOC": "dchw_aKbc-jZehYyn8hKi5FuCfuq8nzxc85V8Vo_BmPsXmqEzH2m-riYrjo2LifrlYH8vBo-38CTLe9KcWu4Nwnw",
        "WECOM_ARCHIVE_FOLDER_WEDOC_SHEET": "q979lj",
        "WECOM_ARCHIVE_FOLDER_WEDISK": "",
        "MEETING_APP_ID": "",
        "MEETING_SECRET": "",
        "MEETING_FIXED_ROOM_ID": "",
        "DEFENSE_REVIEWERS": ""
      }
    }
  }
}
```

**本地验证（干跑）**：未配置真实凭证时，`adapters.ts` 自动返回 MOCK 数据，可端到端验证流程而不消耗真实 API。
- `npm run build` 编译
- `node test/smoke.mjs` 真实拉起 Server（stdio）跑完整 SOP：T1 抓新人 → T2 建会 → T3 拉评委+通知 → T4 归集 → T5 归档（企微文档兜底）→ T6 月报。
- 已验证结果（2026-07-29）：六步全部跑通，2 名 mock 新人对应 2 场会议（`room_booked:false` 符合 v1 不绑固定室），通知触达新人，归档/月报返回 mock 链接。
- 新人看板智能表格已实建（企微机器人 MCP 建表，授权过期后由创建者重授权恢复）：docid=`dcibmZH1Gm22woDm_9KS7ywrfHu-Y1KhmE930tI4o7a3NuG79PtoR1ht_Wm4pryvBXns2wtuuaGrhRVtrxuxvh4A`，10 列 = 员工ID/姓名/部门/中心/组/导师/入职日期/答辩类型/答辩状态/备注；docid 已写入 `config.ts` 默认值与列映射 `ROOKIE_BOARD_COLUMNS`。
- **看板访问/编辑入口（2026-08-03 验证）**：该表另有一个「分享链接形态」docid=`s3_ALgAH3ghAGgCNaEjl35sfT2aiVbME_a`，完整可编辑链接=`https://doc.weixin.qq.com/smartsheet/s3_ALgAH3ghAGgCNaEjl35sfT2aiVbME_a?scode=AJEAIQdfAAopIWSAQqALgAH3ghAGg&tab=q979lj&viewId=vukaF8`（浏览器登录企微即可直接编辑，尤其「组织方式」列需人工手填）。与 MCP 长 ID `dcibmZH1Gm…` 指向同一张表（14 列 / 2 记录 / record_id 完全一致），给用户浏览器编辑入口请用此分享链接，MCP 操作请用长 ID。
- **归档表已实建（2026-07-29）**：企微智能表格「新人答辩归档看板」，docid=`dchw_aKbc-jZehYyn8hKi5FuCfuq8nzxc85V8Vo_BmPsXmqEzH2m-riYrjo2LifrlYH8vBo-38CTLe9KcWu4Nwnw`，sheet_id=`q979lj`，10 列 = 月份/新人姓名/员工ID/会议号/会议链接/录制链接/智能纪要/归档文档链接/归档时间/归档状态；已接入 `config.ts`（`archiveFolderWedoc`/`archiveFolderWedocSheet`）+ `mcp.json` 环境变量。T5 改为向该表追加归档行（比裸文件夹实用，可一键筛选）；代码保留 MOCK 兜底，生产实现 `archiveDefenseToSheet` 待接入（等价已连 MCP：`smartsheet_add_records`）。
- **真实台账已确认（2026-07-29 读取）**：共 2 条——① eileenyyang(杨小羽)，导师 `v_yitcai(蔡依彤)、komakiyang(杨蕊嘉)`（双导师顿号分隔✅），状态**待安排**；② komakiyang(杨蕊嘉)，状态**已答辩**（不参与本期）。`contact_search`（腾讯会议）已验证可解析三人 open_id，真实建会+邀请前置打通。

---

## 8. 落地路线（分阶段）

- **P0（1 周）**：搭 `mcp-server-rookie-defense` 骨架 + T1/T2/T3 跑通「建会+通知」，用测试新人验证批量建会（v1 普通会议，不绑定固定室）
- **P1（1 周）**：T4/T5 跑通「归集+归档（企微文档兜底）」，产出月报
- **P2（待权限，可选）**：微盘写权限获批后切 `wedisk`，关闭文档兜底
- **P3**：接入每月 1 日 recurring automation，全闭环上线
- **可选增强（后续）**：如需体验一致性，补 `create_defense_meetings` 的 `room_id` 直连 REST 集成，绑定固定会议室

---

> 附：可运行骨架代码见同目录 `mcp-rookie-defense/`（含 6 工具完整 Zod schema 与 REST 集成点 TODO）。
