---
title: ZZ-自动化交接文档（副本）
tags: [知识采集, 自动化, 交接, meta]
type: 自动化采集
date: 2026-08-07
---

> ⚠️ **本文件是 workspace 主文档的完整副本（非纯指针）**。主文档修改后须在**同一回合**同步更新本副本，避免内容过期。
> 主文档：[`知识采集自动化-交接文档.md`](file:///C%3A/Users/v_yitcai/WorkBuddy/20260728154244/%E4%BA%A4%E6%8E%A5%E6%96%87%E6%A1%A3/%E7%9F%A5%E8%AF%86%E9%87%87%E9%9B%86%E8%87%AA%E5%8A%A8%E5%8C%96-%E4%BA%A4%E6%8E%A5%E6%96%87%E6%A1%A3.md) ｜ [在 Obsidian 打开](obsidian://open?path=C%3A%5CUsers%5Cv_yitcai%5CWorkBuddy%5C20260728154244%5C%E4%BA%A4%E6%8E%A5%E6%96%87%E6%A1%A3%5C%E7%9F%A5%E8%AF%86%E9%87%87%E9%9B%86%E8%87%AA%E5%8A%A8%E5%8C%96-%E4%BA%A4%E6%8E%A5%E6%96%87%E6%A1%A3.md)

# 知识采集自动化 · 交接文档（新会话必读）

> 本文档写给**完全没上下文的新会话**。读完后你应能无缝接手这条自动化，不再重踩已经踩过的坑。
> 最后更新：2026-08-10 14:40 ｜ 维护人：y（蔡依彤）/ 小柚 ｜ 本版纠正「乐享硬阻断」误判（实测可达，三端恢复）+ 乐享重复清理（建「归档」folder，待清洗素材现每主题 1 份英文名版）
> 配套源：自动化记忆 `.workbuddy/automations/automation-1785984826299/memory.md`、`knowledge-collection/skill`（skill `knowledge-collection`）

---

## 一、这个项目在做什么（What）

一条**每 6 小时轮询**的自动化（自动化 ID `1785984826299`，schedule `FREQ=HOURLY;INTERVAL=6`，model `hy3`，`push_to_wecom_bot=false`）。

流程：在 6 个文化活动主题里**自动搜索外部资讯 → 判定一手/二手 → 六维评估（含关系适配度）→ 与历史去重 → 渲染卡片墙 HTML（关系分层）→ 更新 `index.json` → 三端同步**。

- **6 个主题轮询池**（指针 `knowledge-collection/last-topic.txt` 循环）：员工大会 / Offsite / 破冰 / 颁奖 / Open Day / 下午茶研讨。
- **关系分层（核心维度）**：①平级/朋友向（蓝，已全剔除）/ ②上下级（橙）/ ③高管间（紫）。
- **三端交付**（3 端可用：GitHub Pages / 乐享 / Obsidian）：
  1. **GitHub Pages** —— 精确样式的卡片墙 HTML（线上可看，✅ 主通道）。
  2. **乐享团队文件夹「待清洗素材」** —— ✅ **2026-08-10 14:22 实测可达**：v_yitcai 为 folder owner，`whoami`/`describe_entry`/`apply_upload`+`PUT`+`commit_upload` 全 `code:0`（无 403）。早前「硬阻断」为误判已推翻，自动化第10步本就是推送逻辑（从未改不推），三端恢复。注意：乐享 MCP 无删除 API，重复清理只能 move 进归档 folder。**2026-08-10 14:40 已建「归档」folder（`da1a6240…`），将 6 个中文名旧版 + 2 个重复英文名版（openday 早版 `968fd179`、下午茶中文名 v4 `218adb6b`）共 8 个 move 进归档；待清洗素材现仅留 6 主题英文名版（staff-meeting/offsite/openday/award/afternoontea/icebreaker）+ 1 份「一手源补采计划」，每主题 1 份，自动化以后只更新这 1 份。**
  3. **Obsidian「知识采集库」vault** —— 每主题一篇 markdown 汇总 + 索引，只嵌链接、不存 HTML 文件（✅ 正常）。

---

## 二、已完成（当前状态 · 2026-08-07）

| 交付物 | 状态 | 说明 |
|---|---|---|
| 6 主题卡片墙 HTML | ✅ 已生成并上线 | GitHub Pages 全部 200 |
| `index.json` 主库 | ✅ ≈226 条 | relation 全为 ②③，**0 条 peer**；按主题墙分布见下 |
| 乐享团队文件夹 | ✅ 2026-08-10 14:22 实测可达 | v_yitcai 为 folder owner，whoami/describe/apply_upload 全通，无 403；早前硬阻断为误判已推翻。14:40 已建「归档」子 folder 清理旧版重复（待清洗素材现每主题 1 份英文名版 + 1 份补采计划） |
| Obsidian 知识采集库 | ✅ 6 篇主题汇总 + 3 篇人工 SOP + 索引 | md-only，含 GitHub Pages 链接，零 HTML 副本 |
| 自动化 prompt Step10b | ✅ 锁死文件上传流程 | 不再走原生块 |

**各主题卡片墙规模**（2026-08-10 实测 HTML 卡片数）：员工大会 45 / 破冰 44 / 下午茶研讨 47 / 颁奖 42 / Offsite 39 / **Open Day 10**（九轮清洗后：sec2 8 客户/品牌/国企开放日 · sec3 2 品牌媒体开放日）。Open Day 经七轮(-22 家庭日) + 八轮(-21 投资者/财经媒体开放日) + 九轮(-4 残余资本市场开放日) 从 57→10。

---

## 三、之前卡在哪 + 解决方案（Stuck → Solved）

### 坑 1：乐享上传后"变成 markdown"
- **现象**：用 `entry_import_content(content_type=html)` 把卡片墙导入乐享，块编辑器把 CSS 卡片/渐变**全部剥离成纯文本**，用户看到的就是一堆"markdown"。
- **解决**：改用 `file_apply_upload → HTTP PUT（本地字节）→ file_commit_upload`，把**本地 HTML 文件本体**作为 `file` 条目上传。样式保留在 GitHub Pages 链接里，乐享内只存可下载的文件。
- **结论（用户 2026-08-07 14:13 确认）**：文件条目上传【是正确做法】。用户之前以为"全是 markdown"是混淆了归档里那批 `entry_import_content` 导成的旧 page——那批才是错的。

### 坑 2：文件上传 COS 403
- **现象**：`file_commit_upload` 前 PUT 报 403。
- **根因**：`file_apply_upload` 的 `size` 参数与实际字节数对不上（曾硬编码 24883，实际 19188）。
- **解决**：脚本里**动态读 `os.path.getsize()` 精确字节**，绝不硬编码。

### 坑 3：COS 偶发 403（瞬时故障）
- **现象**：后续批量上传一律 403。
- **根因**：预签名 STS 凭证瞬时失效，非系统性故障。
- **解决**：换新凭证重试即可；**已上传的文件不受影响**，不用回滚。

### 坑 4：团队空间删不掉旧/错条目
- **现象**：`entry_move_entry` / 删除 调用返回 403。
- **根因**：团队知识库 API 对该账号无移动/删除权限。
- **解决**：**放弃在团队空间做移动/删除**，等其自然消失（归档文件夹最终自行消失）。教训：团队空间只做"新增文件上传"，别碰结构性操作。

### 坑 5：原生块重建实验翻车
- **现象**：为"在乐享内渲染漂亮卡片"试 `block_create_block_descendant` 原生块，标题/正文存成空块。
- **根因**：`para()` 误写成 `{"text":{"elements":...}}`，正确应为 `{"elements":...}`。
- **结论**：原生块重做**不划算**，已废弃，保持文件上传 + GitHub Pages 看样式。

### 坑 6：用户误判"上传错了"
- **现象**：用户说"乐享所有新文件都是 markdown"。
- **真相**：混淆了归档里 `entry_import_content` 导成的旧 page（纯文本）vs 主文件夹里正确的 file 条目。
- **解决**：引导用户去主文件夹核对 file 条目 → 确认正确 → **校正了自动化记忆里"需原生块重做"的错误记录**。
- **教训**：再遇到"怎么是 markdown"，先核查是 file 条目还是旧 page，别急着改流程。

### 坑 7：乐享 connector 面板显示 disconnected 是**滞后误报**（2026-08-07 15:04 实测）
- **现象**：自动化某轮执行时，context 连接器状态面板显示 `lexiang disconnected`，按旧规则"disconnected 就跳过乐享"，结果颁奖 19 卡只进了 GitHub Pages + Obsidian，**漏推乐享**（团队文件夹里仍是旧版 12 卡）。
- **真相**：用户追问"同步乐享了吗" → 实测 `lexiang.whoami` 返回 `v_yitcai` 成功，**乐享 MCP 实际连通**。面板 disconnected 是状态滞后，非真断开。
- **解决 / 固化**：自动化 prompt 已改为「第 10 步 whoami 探活，**以 whoami 实调为准**，通则推送，真失败才告警跳过，绝不因面板 disconnected 跳过」。补推颁奖 19 卡到乐享（覆盖 version 4→5）后三端对齐。
- **教训**：**永远用 whoami 实调判乐享连通，不要信面板状态灯**。

### 坑 8：更新已有乐享文件时 apply_upload 扩展名校验
- **现象**：`file_apply_upload(更新场景)` 报 `50021017 新版本文件扩展名应与原文件一致`。
- **根因**：原条目 name 不带 `.html` 后缀，系统从 name 推断扩展名，与新传 `extension=html` 冲突。
- **解决**：`name` 必须带 `.html` 后缀（如 `颁奖典礼卡片墙.html`），与 `extension=html` 对齐，校验才过。

### 坑 9：乐享 MCP **没有删除条目/文件夹的 API**（清理只能走"归集归档"）
- **现象**：早期在乐享个人库误建了一个旧 folder（`98bfda9e...` 「📚 文化活动知识采集(自动化沉淀)」，含 3 个早期错误 page），想删干净，但工具列表里**找不到 entry/folder 删除接口**。
- **实测可用删除能力**：`block_delete_block`（仅删块）、`draft_delete_markdown_draft`（仅删草稿）、`entry_move_entry`（移动条目）。**无** `entry_delete_entry` / `folder_delete` 之类。
- **解决（归集法，可逆安全）**：在个人库根新建归档 folder `🗑 旧自动化沉淀(已废弃-归集待清)`（`a0e16641...`），用 `entry_move_entry(entry_id=98bfda9e..., parent_id=a0e16641...)` 把旧 folder 整体移进去。个人库根恢复干净，团队文件夹正确交付不受影响；旧数据保留可逆、不丢。
- **教训**：乐享清理别指望 API 真删除，**旧无用条目归集进归档 folder 即可**；若需彻底消失只能在乐享网页 UI 手动删（或等其自动归档）。

---

### 坑 10：去重只做 URL 比对，漏掉同源主题重复（2026-08-10 发现并修正）
- **现象**：Offsite 六轮 enrich 新增 5 张卡，执行脚本只做了「URL 完全相同」精确比对（与 index.json 全量 + 主题 HTML 现有卡均无 URL 碰撞即判新增）。事后用户质疑核查，发现其中两张——iceindia「企业年度 Offsite 全流程最佳实践」与 easyhotelrfp「企业 Offsite 端到端规划指南（含预算基准）」——属**同源主题不同站**，内容骨架几乎一致（目的→选址→议程→后勤→ROI→时间轴），且都与既有 11/12/14/19 等「offsite 规划方法」卡撞同一主题壳。另有 2 张（分布式远程团队 / 高影响力四策略）与既有卡邻近但角度差异化，可保留。
- **根因**：prompt 去重要求是「URL 完全相同 / 标题归一化相似 / 摘要重合」三档，但脚本**只实现了第一档 URL 比对**，后两档语义去重从未落地。
- **修正（2026-08-10）**：删 33(iceindia) 整卡，将其差异化点（年会式 offsite 的 outcomes+KPI 定义、选址形态匹配、体验活动留白）并入 34(easyhotelrfp) 的「怎么做」段与 note；Offsite HTML 40→39 卡（r3 26→25）；index.json 删 iceindia 条目（255→254）；Obsidian 汇总笔记 + 00-索引同步；门户 Offsite 计数 40→39、total 247→246。保留的 2 张补「🔍 差异化」定位注避免读者误判重复。
- **教训 / 固化规则**：去重**必须**实现三档，尤其「标题归一化相似 + 摘要重合」语义比对，不能只比 URL。新增卡入 HTML 前，先列出同主题既有卡标题做语义比对，撞同一主题壳即合并或放弃；跨站同主题卡优先保留信息更全 / 有硬数据（预算基准、时间轴）的一张。

---

## 四、踩过的坑 · 不要再踩（Pitfalls Checklist）

- ❌ **绝不用 `entry_import_content(html)` 往乐享导卡片墙**——会被拍成纯文本。只走文件上传三连。
- ❌ **绝不硬编码上传 `size`**——动态读字节，否则 403。
- ❌ **乐享 MCP 无删除条目/文件夹 API**（坑 9 已记）——重复清理只能 `entry_move_entry` 进归档 folder；团队空间 move 属结构改动，先问 y 确认（实测 v_yitcai 为 folder owner 有写权限，但谨慎）。
- ❌ **别再试乐享原生块重做卡片墙**——已证伪，浪费时间。
- ❌ **绝不能主动发企微/企微机器人消息**——最高优先级硬约束，凌驾一切；除非 y 当前对话明说"发吧/可以发"。
- ❌ **绝不把 ①平级/朋友向 内容放进任何交付物**——relation 含 peer 一律剔除（含双/三档卡）。
- ❌ **Obsidian 别复制 HTML 文件进 vault**——只写 md + 嵌 GitHub Pages 链接。
- ❌ **GitHub Pages 文件名必须 ASCII、根目录放 `.nojekyll`、页脚必含** `📌 本页由 yitong 沉淀整理 · 文化活动知识库`、`SSH` 推送。
- ❌ **去重不能只比 URL**——必须做「标题归一化相似 + 摘要重合」语义比对，否则同源主题不同站的重复卡会漏网（见坑 10）。
- ❌ **绝不把「家属开放日 / 家庭日 / 亲子 / 家企共融」类内容放进交付物**——用户 2026-08-10 明确「不要家庭日相关内容」，凡家属开放日/家庭开放日/员工家属参与类活动一律剔除（Open Day 七轮清洗已清 22 张，颁奖 2 张）。
- ❌ **Open Day 绝不收「投资者/IR/财经/资本市场开放日」**——命中 投资者/股东/券商/证监局/资本市场/IR/市值/财报/路演/董秘/上市 即跳过；Open Day 域只装客户/媒体/品牌/公众/国企开放日（八轮+九轮清洗已清 25 张上市公司 IR/监管开放日）。

---

## 五、硬约束速查（MUST-KNOW RULES）

1. **企微禁发**：未授权绝不发任何企微消息（含待办/群 webhook/模板卡片/测试）。外部动作（发消息、公开操作）谨慎；内部动作（读、整理、学）大胆。
2. **关系分层硬过滤**：凡 `relation` 含 `peer`（①平级/朋友向）一律剔除，只保留 ②上下级 + ③高管间。用户原话："你给我找的所有活动都不要有平级/朋友向的"。
2b. **家庭日/家属开放日硬排除**：凡 家属开放日/家庭日/家庭开放日/亲子/家企共融/员工家属参与类活动 一律剔除（用户 2026-08-10「不要家庭日相关内容」；Open Day 七轮 -22、颁奖 -2 已清）。
2c. **Open Day 域过滤（投资者/IR）**：Open Day 主题只收 客户/媒体/品牌/公众/国企开放日；命中 投资者/股东/券商/证监局/资本市场/IR/市值/财报/路演/董秘/上市 即跳过（上市公司 IR/证券监管/财经媒体类开放日属资本公关域，非企业文化活动向；八轮 -21 + 九轮 -4 已清 25 张）。
3. **Obsidian 落库规则**：只写 markdown 笔记 + 嵌入 GitHub Pages 链接；vault 内零 HTML 副本；独立 vault「知识采集库」，不混进「活动」vault。
4. **乐享上传规则**：`file_apply_upload → PUT → file_commit_upload` 上传本地 HTML 文件本体；目标团队文件夹 `5106d5b2...`；`size` 精确匹配；已存在则更新不新建（更新时 `name` 须带 `.html` 后缀，见坑 8）；**connector 状态面板显示 disconnected 常为滞后误报，必须以 `lexiang.whoami` 实调为准——whoami 通则推送，真失败才告警跳过，绝不因面板状态跳过乐享**（见坑 7）。
5. **GitHub Pages 规则**：ASCII 文件名；根 `.nojekyll`；页脚固定字符串；SSH 推送（yitongcaii + ed25519 key）；`git add <具体文件>` 不用 `-A`。

---

## 六、关键 ID / 配置速查（Quick Reference）

| 项 | 值 |
|---|---|
| 自动化 ID | `1785984826299` |
| 乐享 MCP endpoint | `https://mcp.lexiang-app.com/mcp?company_from=csig` |
| 乐享 token | `lxmcp_1b82fcd9c11ff51ea657ee591e793c39825fb1748510b241ab29443a1106b708` |
| 乐享目标团队文件夹 | `5106d5b2decc442780c1cae5014c6fb6`（名「待清洗素材」，space `cd14780757604f59bffb6eb9cf3ba032`） |
| 乐享个人库旧文件夹（已废弃·归集） | `98bfda9e...` → 已 move 进 `🗑 旧自动化沉淀(已废弃-归集待清)`（`a0e16641...`），非删除（MCP 无删条目 API） |
| GitHub Pages 仓 | `yitongcaii/workbuddy-handoff`，子目录 `knowledge-collection/` |
| 卡片墙 URL 模板 | `https://yitongcaii.github.io/workbuddy-handoff/knowledge-collection/<主题>/<主题>.html` |
| 同步脚本 | workspace `sync_knowledge_github.py` |
| Obsidian 知识采集库 vault | `C:\Users\v_yitcai\Documents\Obsidian\知识采集库\` |
| 主题轮询指针 | `knowledge-collection/last-topic.txt` |
| 主库 | `knowledge-collection/index.json`（96 条，relation 全②③） |

---

## 七、待办 / 开放问题（Open Items）

- ✅ **乐享同步机制已固化（2026-08-07 15:04）**：自动化 prompt 第 10 步改为 `whoami` 探活（以实调为准，面板 disconnected 为误报，通则推送）；第 7 步补回 `sync_knowledge_github.py` 调用。配套 `automation-1785984826299/memory.md` 已记乐享实际连通 + 扩展名踩坑（坑 7 / 坑 8）。
- ✅ **Obsidian 已按「素材/<主题>/」拆子文件夹**（2026-08-07 完成）：6 篇 `<主题>-知识卡汇总.md` 移入 `素材/<slug>/`（slug 同 GitHub Pages：staff-meeting / offsite / icebreaker / award / openday / afternoontea）；`00-知识采集索引.md` 各主题段加 `📄 主题汇总笔记：[[素材/<slug>/<主题>-知识卡汇总]]` 导航链接；`破冰` 笔记内两条交叉引用同步改路径。根目录仅留 3 篇人工 SOP + 索引 + ZZ 指针。
- ✅ **自动化 prompt Step7/10c 落库路径已改（2026-08-07 15:02）**：汇总笔记现固定写入 `素材/<slug>/<主题>-知识卡汇总.md`（slug 映射：员工大会=staff-meeting、Offsite=offsite、破冰=icebreaker、颁奖=award、Open Day=openday、下午茶研讨=afternoontea），已存在则**覆盖更新**、绝不在根目录重建；索引分区约束「**绝不重复追加**『📄 主题汇总笔记』导航链接行、不重建索引」；Step 10c 同步校验 `素材/<slug>/` 路径。下次 6h 轮询将直接落到子文件夹，与索引 `[[素材/...]]` 导航对齐，不再产生根目录重复文件。
- ⏳ **破冰重生后自动化日志缺失**：破冰曾因全 peer 被删，后于 2026-08-07 06:49 重采（14 卡，②③），但那次轮询未写入 `memory.md`，需补记或忽略。
- ✅ **个人库旧文件夹已清理（2026-08-07 15:18）**：`98bfda9e…`（「📚 文化活动知识采集（自动化沉淀）」含 3 个旧 page：颁奖12卡/OpenDay13卡/员工大会3篇）已用 `entry_move_entry` 归集进个人库根新建的 `🗑 旧自动化沉淀(已废弃-归集待清)`（entry `a0e16641…`）。**注意**：乐享 MCP 无删除条目/文件夹 API（仅 move / 删块 / 删草稿），故走「归集归档」而非真删，可逆、不影响团队文件夹 `5106d5b2` 正确交付。个人库根现已干净。
- ⏳ **一手源稀缺**：颁奖/Offsite 类公开一手源极少（96 条仅 10 一手），持续以权威机构（SHRM / O.C.Tanner / HBR）补充二手。

---

- ⏳ **index.json 缺 topic 字段（2026-08-10 发现）**：全量 254 条 `topic` 字段**全部为 null**（只存 title/normKey/url/sourceType/relation/summary），去重无法按「同主题内比对标题相似」执行，只能靠全局 URL——这正是坑 10 漏掉同源重复的根因之一。需一次性回填：遍历 6 主题 HTML 提取各卡 url→slug 映射，给 index.json 每条按 url 归 `topic`（staff-meeting/offsite/icebreaker/award/openday/afternoontea），匹配不到标 `unknown`。回填后未来语义去重才能在主题内生效。脚本思路：`读各 <slug>/<slug>.html 抽 href 建 url2topic → 读 index.json 逐条填 topic → dump`；回填后需重新 `sync_knowledge_github.py` push。

---

- ⛔ **乐享同步硬阻断（2026-08-10 确认）**：目标空间 `hrbp文化活动灵感发电机⚡️`(cd147807) 已归属团队「安全HRBP文化知识库」(team 28fab978)，v_yitcai 非成员 → `describe_entry`(folder `5106d5b2`)/`apply_upload` 全返回 403 / 50021001。非瞬时故障，是权限/团队归属变化。**恢复条件**：① 把 v_yitcai 加回「安全HRBP文化知识库」团队；或 ② 指定一个 v_yitcai 可访问的乐享空间作新 sink 重配 `parent_entry_id`。当前三端交付仅 GitHub Pages + Obsidian 两路。
- ✅ **Open Day 九轮清洗完成（2026-08-10）**：家庭日(七轮 -22) + 投资者/财经媒体开放日(八轮 -21) + 残余资本市场开放日(九轮 -4) 全清，57→10 卡（sec2 8 / sec3 2）；颁奖同步清 2 张家属卡（44→42）。四端一致 + GitHub 已 push。
- ⏳ **index.json 标题漂移 + 孤儿条目（预存，建议下次收口）**：① 个别卡 HTML 标题与 index.json 标题文本略有差异（如缺「（观摩会/开放活动）」后缀），index 实含 ≈227 条、数量一致仅文本不严格相等；② 孤儿条目 `千马会高端闭门会·董秘×私募合伙人` **已删（2026-08-10）**：从 afternoontea.html 卡 + index.json 条目双删，下午茶 48→47 卡、index 226 条；此前「未进任何主题墙」为误判——卡实际在 afternoontea sec3，仅 index.topic=null 致孤儿标记。

## 八、给新会话的启动清单（How to Resume）

1. 先读本文档 + `.workbuddy/automations/automation-1785984826299/memory.md`（最新轮询记录）。
2. 跑之前先 `cat knowledge-collection/last-topic.txt` 看轮询指针落在哪个主题。
3. 上传乐享前先 `lexiang.whoami` 实调连通性——**注意 context 面板显示 disconnected 常为滞后误报，以 whoami 实调为准，通则推送，真失败才跳过**（绝不要因为面板灯就跳过乐享，详见坑 7）。
4. 任何乐享写入**只走文件上传三连**，size 动态读；团队空间不做移动/删除。
5. 产出后三端同步：GitHub（`sync_knowledge_github.py` SSH push）→ Obsidian（md + 链接）→ 乐享（file 条目）。
6. **绝不发企微消息**，除非 y 当场授权。
7. 改完任何流程，回头更新本文档第七节 + 自动化 memory.md。
