---
title: ZZ-新人答辩自动化交接文档（副本）
tags: [交接, 索引, 自动化, meta]
type: 自动化采集
date: 2026-08-10
---

> ⚠️ **本文件是 workspace 主文档的完整副本（非纯指针）**。主文档修改后须在**同一回合**同步更新本副本，避免内容过期。
> 主文档：[`新人答辩自动化_交接文档.md`](file:///c%3A/Users/v_yitcai/WorkBuddy/20260728111214/%E6%96%B0%E4%BA%BA%E7%AD%94%E8%BE%A9%E8%87%AA%E5%8A%A8%E5%8C%96_%E4%BA%A4%E6%8E%A5%E6%96%87%E6%A1%A3.md) ｜ [在 Obsidian 打开](obsidian://open?path=c%3A%5CUsers%5Cv_yitcai%5CWorkBuddy%5C20260728111214%5C%E6%96%B0%E4%BA%BA%E7%AD%94%E8%BE%A9%E8%87%AA%E5%8A%A8%E5%8C%96_%E4%BA%A4%E6%8E%A5%E6%96%87%E6%A1%A3.md)

---
title: 新人答辩自动化 · 交接文档
tags: [协作手册, 方法, 自动化, 活动/方法]
source: WorkBuddy 工作区交接文档（2026-08-10 生成）
关联笔记: [[新人答辩MCP应用设计方案]] [[协作使用说明书]] [[英语角项目总览]]
---

# 新人答辩自动化 MCP 应用 — 交接文档

> **写给谁**：完全没上下文的新会话 / 新设备 / 接手的人。读完这份就能接手，不用翻历史。
> **最后更新**：2026-08-10 ｜ **项目状态**：设计完成 + 首例跑通 + gate 脚本与跨设备部署包就绪；**recurring 定时任务尚未创建**。
> **一句话定位**：把"新人转正答辩"从人工盯看板、手动拉群建会，变成 WorkBuddy 编排的半自动流程（拉群 + 发提醒话术 + 可选建会 + 看板回填），所有外发动作**必须过人工审批闸**。

---

## 0. 三个名词先对齐（避免新会话迷惑）

| 名词 | 是什么 | 别混淆 |
|------|--------|--------|
| **MCP 应用** | 本项目本体：WorkBuddy 编排 + 多个 MCP Server + 一个 skill 串起来的"应用" | **不是 skill**。skill 只是被调用的零件 |
| **rookie-defense server** | 专用领域 MCP Server（`mcp-rookie-defense/`，TS+zod），封装看板读写 + 答辩逻辑 | 本机 env 为空 = **MOCK 状态**，7/30 首例实际没靠它真跑 |
| **wecom_gate.py** | **审批闸脚本**：所有企微外发（发消息/建会/建群）的封装层，DRY_RUN 默认开 + 生成待审批清单 | 真实企微凭证**不在这个文件里** |

---

## 1. 我们在做什么

### 业务目标
新人入职转正前要完成"发展答辩"（15 分钟陈述 + 5 分钟 QA，共 20 分钟），由**导师**作为第一责任人组织，HR（y）兜底。原本靠人工盯看板、手动拉群发话术、手动建会，容易漏。本项目把它产品化。

### 架构（三层，互不重复）
1. **WorkBuddy（编排大脑 / MCP Client）**：在会话里调各 MCP、跑审批闸、DRY_RUN。
2. **数据 / 触达面**：
   - 企微机器人 MCP（看板读写 smartsheet）
   - **客服号通道**（wecom-api-group skill）：建群 / 发群消息 / 改成员 / RTX↔userid。纯单向发送，**无任何拉取消息能力**。
3. **答辩会场**：腾讯会议 MCP（建会 / 回填会议号链接）。常规场景**不默认建会**（特例见下）。

### 触发架构（三层）
- **每周一授权 pre-flight**（独立 recurring，尚未建）：探活企微/会议/客服号授权，失效提前告警（平台 token 无精确过期天数，只能探活兜底）。
- **每月 1 日主任务**（recurring，尚未建）：扫描看板，挑"本月应答辩"（看板 `答辩状态=待安排`）新人，按**部门+中心+小组**聚合拉群 + 发话术 + 可选回填「组织方式」+ 月报。
- **幂等防重 = 动作级 guard**（非定期任务）：每次执行（自动/手动/重试）、每个动作（建群/回填）前先查状态，已做则跳过。

### 群维度聚合规则（拍板结论）
- 每月 1 日，把"本月应答辩"新人按 **部门+中心+小组** 聚合成**一个群**（不再一人一群）。
- 群命名 `新人发展答辩沟通群-<中心>-<组>`；拉群前剔除**竞业 / 干部**（这两类不述职）。
- 同一小组多名导师去重合并后一并拉入。

### 流程规范（关键拍板）
- **常规不自动建会**：会议由导师自建、自行组织；自动化只负责拉群 + 发提醒 +（可选）回填。看板「会议号/会议链接/录制链接」常规改导师/HR 手填。
- **特例（转正前 5 天跟进分支）**：若导师回复"未安排"并经 y 转述，Agent 立即用腾讯会议 MCP 建"次日"会议 + 自动回填看板 + 发生建会通知（@导师 + @所有人）。
- **导师第一责任人**：负责邀请中心负责人+新人、全程录屏，答辩后录屏回传 y（v_yitcai）；会议不用邀 HR 进会。
- **答辩流程**：15 分钟陈述 + 5 分钟 QA。
- **PPT 模板**：`https://doc.weixin.qq.com/slide/p3_AR8AfgYaAOYCNgPdVfWjaSCmARYrp?scode=AJEAIQdfAAouSz0i1JAaUAsQZIADc`

---

## 2. 完成了什么

| # | 交付物 | 说明 | 位置 |
|---|--------|------|------|
| 1 | 应用设计文档 | 设计正本 v1.1，含架构/触发/话术/硬约束 | `新人答辩MCP应用设计方案.md` |
| 2 | 领导汇报版 | 向上汇报 docx | `新人答辩自动化方案（领导汇报）.docx` |
| 3 | 用户使用说明书 | md + docx | `用户使用说明书.md/.docx` |
| 4 | 领域 MCP Server | `mcp-server-rookie-defense`（TS+zod，已编译） | `mcp-rookie-defense/` |
| 5 | 新人看板 | 企微智能表格，14 列，云端唯一数据源 | docid 见 §6 |
| 6 | 首例全链路跑通 | 2026-07-30 蔡依彤 08-06 答辩：建会→回填→建群→群发@导师 | 真实跑通 |
| 7 | **gate 脚本** | `wecom_gate.py`：DRY_RUN 默认开 + 逐条审批闸 + 看板唯一性常量 | `wecom_gate.py` |
| 8 | 拉群/跟进话术 | 已推送测试群验证（返回 ok） | 固化进设计文档 §话术 |
| 9 | **跨设备部署包** | 一键 `setup.ps1` + 源码 + 凭证 + 模板 | `rookie-defense-deploy/` + `.zip` |
| 10 | gate+方案打包 | gate 脚本 + 设计文档 zip | `新人答辩_gate与方案.zip` |

---

## 3. 之前卡在哪（历史 blocker）

1. **企微消息/通讯录权限被企业禁用**：`wecom-cli` 报"当前企业暂不支持授权机器人「消息/通讯录」使用权限" → 聊天式双向触达不可行。
2. **回调接收路被堵死**：y 非企微管理员，无法配「接收消息」回调 URL → 群内按钮/文字回复程序化读取不到，只能 y 手动转述。
3. **群内回复我方不可见**：7/30 后 y 在群艾特导师问状态，但机器人读不到回复。
4. **rookie-defense server 没进全局 mcp.json**：全局只注册了企微机器人/lexiang/腾讯会议；server 自带 mcp.json 把 `cwd` 写死绝对路径 → 跨设备直接崩。
5. **server 本机 env 全空 = MOCK**：7/30 首例实际靠「企微机器人 MCP + 腾讯会议 MCP + 客服号 skill」跑通，不依赖 rookie-defense 真凭证。
6. **recurring 定时任务没建**（截至 2026-08-03 仍在）：所谓"只管更新看板就自动跑"当前不成立，仍需手动/AI 触发。
7. **跨设备迁移无标准包**：最初没有"发另一个设备自动跑"的方案。

---

## 4. 解决方案是什么（对应上表）

1. **权限被禁 → 走客服号单向通道**：用 `wecom-api-group` skill（纯发送），放弃双向聊天式触达。
2. **回调堵死 → y 转述 + 表单替代**：① 网页表单 POST 公网 ② y 手填看板「组织方式」列 ③ 找 IT 开回调（未走）。
3. **群回复不可见 → 动作前重读看板**：不依赖群内对话状态，每次外发前 `smartsheet_get_records` 实时拉最新值。
4. **server 未注册/路径写死 → 跨设备部署包**：`setup.ps1` 合并全局 mcp.json（含 rookie-defense 段，改绝对路径）+ 落位凭证。
5. **MOCK 问题 → 明确主路径**：编排直接调企微机器人 MCP + 腾讯会议 MCP + 客服号 skill 即可，rookie-defense 是可选增强。
6. **recurring 没建 → 双触发兜底**：每月 1 日自动跑 + y 在会话主动说"跑新人答辩"手动触发。
7. **无迁移包 → 已生成 `rookie-defense-deploy.zip`**：装 Node → 跑 setup.ps1 → 重启 WorkBuddy。

---

## 5. 🔴 踩过的坑，不要再踩（最高优先级）

> 这一节是血泪史。**新会话接手时，逐条对照，违反任意一条都可能发错消息 / 炸群 / 建重复群。**

### 5.1 外发安全（凌驾一切）
- **企微外发必须过审批闸**：任何 `send_message` / `schedule_meeting` 真实调用前，必须经 y 审批。落地 = ① `DRY_RUN` 默认开（封装层只打印不真发）② 生成 `pending_wecom_<日期>.md` 待审批清单（含群id/@对象/全文/建会参数）③ y 回"执行"才真发，支持逐条"砍/改/延"。
- **禁止擅自在企微测试**：不允许"先测一发看看"式未获逐条确认的发送。automation 跑出来也只 propose 不 execute。昨天两次测试群发送属当时授权特例，不作后续先例。
- **每句话术须逐条确认全文**：群聊发出的**每一句话术**（含 @导师/@新人/@所有人、提醒/跟进/建会通知/未安排/未完成等任意分支）都须 y 确认全文无误才发，逐条单独确认、绝不批量。
- **动作前必须重读看板**：看板无自动推送，每次 `smartsheet_get_records` 为实时拉取但上下文旧值会过时；任何 propose/外发/建会/拉群前必须重新 get_records，严禁依赖历史快照。

### 5.2 看板唯一性（硬约束）
- 全项目**只读写那一个看板**（docid/sheet_id 硬编码常量），**禁止新建看板、禁止写其他看板**。y 随时自查，Agent 不抢改；仅 y 明确指令才动，且只动这唯一看板。

### 5.3 企微 API 实测坑
- **93008 `not in room`**：发消息的客服号必须身处该群，否则直接 93008。新建群会自动拉客服号进群；存量群若不在需重建。
- **rich_text 正确格式**（实测）：
  - text 节点 `{"type":"text","text":{"content":"..."}}`（text 是**含 content 的对象，非字符串**，否则 40058）；
  - @人节点 `{"type":"mentioned","mentioned":{"userlist":["RTX名"]}}`（用 **RTX 名非 userid**）；
  - 链接只能纯文本 URL 写在 text.content，**禁止 link block**（会致 @ 失效）。

### 5.4 平台 / 权限坑
- **自动录制靠 `auto_record_type=cloud` 仅"请求"**，真录取决于企业后台是否对该应用开云录制。
- **评委为主持人**：用个人 token 建会时 `hosts` 直接为本人，无需 host transfer（旧假设"建会者=机器人"不成立）。
- **授权形态**：企微机器人=URL内apikey、腾讯会议=X-Tencent-Meeting-Token header、客服号=corpid/secret 文件；均为静态凭证，过期需人重授权。

### 5.5 架构认知坑
- **这是 MCP 应用，不是 skill**；触发是**定时（每月1日 + 转正前5天）**，不是"关键词一提就跑"——关键词触发会误炸群。
- **英语角 Loop（独立项目）只在后台提醒 y**（会话内输出待办/文案），**禁止调用客服号、禁止往新人答辩群发消息**。

### 5.6 代码 / 交付坑
- **`wecom_gate.py` 发给别人要脱敏**：含硬编码 `v_yitcai` 用户名路径（sys.path / PENDING_DIR）+ 你的看板 docid。真实企微凭证不在本文件（靠 `~/.wecom-api/secrets/credentials.json`），对方拿不到你发消息的能力，但能看到你的私有路径/看板 ID。
- **跨设备迁移三件套**：看板无需迁（云端）；代码（`mcp-rookie-defense/` 整目录 → npm install + build）+ 凭证（global mcp.json apikey/token + 客服号 credentials.json）+ 配置（全局 mcp.json 加 rookie-defense 段改绝对路径）。新设备需装 Node。
- **rookie-defense server 本机 env 空=MOCK**：即使不填真凭证也能起，但只返回假数据；要用真 server 须补 WECOM_CORP_ID/SECRET、MEETING_APP_ID/SECRET、看板 DOCID。

---

## 6. 关键常量与文件索引

### 6.1 看板（唯一数据源，硬编码常量）
- **分享短链**（浏览器编辑）：`s3_ALgAH3ghAGgCNaEjl35sfT2aiVbME_a?scode=AJEAIQdfAAopIWSAQqALgAH3ghAGg&tab=q979lj&viewId=vukaF8`
- **MCP 长 ID**：`dcibmZH1Gm22woDm_9KS7ywrfHu-Y1KhmE930tI4o7a3NuG79PtoR1ht_Wm4pryvBXns2wtuuaGrhRVtrxuxvh4A`
- **sheet_id**：`q979lj`
- 14 列：员工ID/姓名/部门/中心/组/导师/入职日期/答辩状态/备注 + 会议日期/会议号/会议链接/录制链接 + 组织方式(文本列)。**「导师」为文本列**，多名用 `、` 分隔，代码拆分逐一邀请。**「答辩状态」是拉群筛选依据**（值=「待安排」即本月应答辩）。

### 6.2 企微通道与凭证（⚠️ 不外泄明文）
- 拉群/发消息走**客服号通道**（corpid=`wxab249edd27d57738`，service_id=`fw67a985aa21073307`），凭证 `~/.wecom-api/secrets/credentials.json`。
- 企微机器人 apikey + 腾讯会议 token：存于全局 `~/.workbuddy/mcp.json`（云端静态凭证，跨设备原样复制）。
- **交接文档里不写明文 secret**；新会话需要时用 `wecom_gate.py` 走审批闸，凭证由 y 本机文件提供。

### 6.3 工作区文件清单（本机 `c:\Users\v_yitcai\WorkBuddy\20260728111214\`）
| 文件 | 用途 |
|------|------|
| `wecom_gate.py` | 审批闸脚本（外发封装层） |
| `新人答辩MCP应用设计方案.md` | 设计正本 |
| `新人答辩自动化方案（领导汇报）.docx` | 汇报版 |
| `用户使用说明书.md/.docx` | 使用说明 |
| `mcp-rookie-defense/` | 领域 MCP Server 源码 |
| `rookie-defense-deploy/` + `.zip` | 跨设备部署包 |
| `新人答辩_gate与方案.zip` | gate 脚本 + 设计文档打包 |
| `新人答辩看板_20260803.html/.md` | 看板快照 |
| `wecom_callback_server.py` / `wecom_aes_crypto.py` / `test_button_card.py` / `board_sync.py` / `read_automations.py` / `README_回调基建.md` | 回调基建探索（回调路已判定堵死，仅供参考） |

---

## 7. 当前待办 / 未闭环

| 项 | 状态 | 谁 |
|----|------|----|
| 创建每月1日 recurring 主任务 | ❌ 未建 | 待 y 拍板后建 |
| 创建每周一授权 pre-flight | ❌ 未建 | 待 y 拍板后建 |
| rookie-defense server 真凭证填充 | ❌ 本机 MOCK | 视是否要真用 server |
| 英语角 Loop 改造为 propose-only | ⚠️ 已拍板未落地 | 待做 |
| 转正前5天跟进分支接入 | ⚠️ 话术已定，未接自动化 | 待做 |
| 首次跨设备 DRY_RUN 验证 | ❌ 未做 | 换设备时 |

---

## 8. 给新会话的接手 Checklist

1. 读 §0–§1 对齐名词与架构；读 §5 硬约束（外发审批 / 看板唯一性 / 重读看板 / 禁擅测）。
2. 任何企微外发 → 走 `wecom_gate.py` 的 DRY_RUN + pending 清单，**等 y 回"执行"**。
3. 任何动作前 → `smartsheet_get_records` 实时重读看板，按「答辩状态=待安排」筛本月应答辩。
4. 不新建看板、不写其他看板、不发测试消息、不靠群内回复判断状态。
5. 换设备 → 用 `rookie-defense-deploy.zip` 的 setup.ps1，先 DRY_RUN 再真发。

---

## 9. 周边并行项目（一句话索引，本交接不含细节）
- **抽奖 Web 应用**：Vue3+Vite / Express+SQLite，Railway+GitHub Pages 部署（主力项目）。
- **英语角运营 Loop**：独立看板 + 每日 10:00 巡检，已拍板"只在后台提醒 y，禁发客服号/禁发答辩群"。
- **活动策划**：H1 业务连续性文化周 + H2 云技服质量稳定性文化活动；男性为主技术团队外地团建方案池；AI 黑客松。
- **知识管理**：Obsidian vault（`C:\Users\v_yitcai\Documents\Obsidian\活动`）+ ima 知识库。
- 详细跨项目习惯见 `~/.workbuddy/MEMORY.md`（极简命令驱动 / 成品给 HTML / 每交付沉淀 Obsidian 等）。
