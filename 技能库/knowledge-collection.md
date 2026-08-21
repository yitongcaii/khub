---
tags: [skill]
source: C:\Users\v_yitcai\.workbuddy\skills\knowledge-collection
updated: 2026-08-06
---
# knowledge-collection

> 源路径：`C:\Users\v_yitcai\.workbuddy\skills\knowledge-collection` ｜ 类型：用户级 skill ｜ 更新：2026-08-06
> 简介：知识采集自动化 skill。当用户需要按指定主题（员工大会 / Offsite / 破冰 / 颁奖 / Open Day / 下午茶研讨）自动搜索外部资讯、判定一手与二手来源、做五维评估、与历史去重并沉淀为卡片墙 HTML 到 Obsidian「知识采集库」时使用。This skill should be used when the user asks to auto-collect, fetch-and-deposit, or build a knowledge card wall for activity/HR topics, or when the 6h knowledge-collection automation fires.

# 知识采集沉淀 Skill

## 目的
将「按主题自动搜索 → 判定一手/二手 → 五维评估 → 历史去重 → 渲染卡片墙 → 落库」固化为可复用流程，避免每次重写。

## 何时用
- 用户说「采集 XX 主题」「自动搜 XX 知识」「建 XX 知识卡」「沉淀 XX 到知识库」。
- 触发「知识采集自动化（6h 轮询）」automation 时，本 skill 作为流程依据。

## 主题池（固定，轮询）
员工大会、Offsite、破冰、颁奖、Open Day、下午茶研讨。轮询进度存 `C:\Users\v_yitcai\WorkBuddy\20260728154244\knowledge-collection\last-topic.txt`（不存在则从第一个开始）。

## 流水线（每次必跑）
1. **取主题**：读 last-topic.txt 得到本次主题，准备更新为下一主题。
2. **多源搜索（按关系档定向）**：WebSearch 按主题 + 受众关系档做多角搜索。关系档见下文「受众关系分层」。每个主题应**覆盖多关系档**，尤其优先找 ②/③ 的一手源（HR 高管工作坊 SOP、管理层 Offsite 官方议程、高管教练方法论、公司内部上下级沟通培训）；覆盖公司内部源（KM / 乐享 / HR 公众号 / 内部复盘）、主办方官方回顾、工具官网文档、行业媒体、经验帖。
3. **一手/二手判定**：逐条打标。
4. **六维评估**：相关度 / 权威性（一手加权）/ 时效性 / 去重度 / 可落地质量 / **关系适配度**，各 1-5 分，任一 < 3 淘汰；一手放宽阈值，但**关系适配度对跨档内容从严**（平级向内容用于上下级场景直接判低分）。
5. **历史去重**：读 index.json，比对 URL 完全相同 / 标题归一化相似 / 摘要重合，判为重复则删信息少或权威低的一版，留更全更权威（一手优先）版；记录新增 N、去重删 M。
6. **渲染卡片墙 HTML**：视觉与字段见 `references/cardwall-spec.md`。文件名用 ASCII。
7. **落库**：更新 index.json（标题 / 归一化 key / URL / 一手二手 / 摘要）；Obsidian「知识采集库」追加人读索引 md。
8. **硬约束**：绝不发送任何企微消息（todo / webhook / 测试），除非用户当次对话明确授权。
9. **打印摘要**：本次主题、新增 N、去重删 M、产出文件路径。
完成后把 last-topic.txt 更新为下一主题。

## 一手 / 二手判定（摘要，完整见 references/cardwall-spec.md）
- **一手**：公司内部官方（KM / 乐享 / HR 公众号 / 内部复盘）、主办方原文、当事人逐字稿（演讲 PPT / 员工故事 / 高管公开信）、工具官方文档。
- **二手**：媒体解读、服务商营销软文（zbj 等）、知乎 / 小红书 / 头条经验帖、培训转载。
- **规则**：一手优先；二手仅一手缺失时保留，且必须醒目标注「二手·供参考」。

## 受众关系分层（必做，硬维度）
活动类知识必须按**受众关系 / 权力距离**分层，绝不混用。三层：
- **① 平级 / 朋友向（peer）**：轻松、可暴露、可搞笑。如两真一假、互画肖像、最尴尬经历。
- **② 领导↔员工（上下级，supervisor）**：尊重、不隐私暴露、建信任不越界。如轻量自我介绍+专业背景、共创式提问、目标对齐小活动。
- **③ 领导↔领导（高管间，exec）**：商务化、以专业 / 共同目标切入、避免幼稚游戏。如行业洞察互换、战略议题共识、职业里程碑履历盲盒。

规则：
- 每张卡**必填**「适用关系」字段（可多档，如 peer+supervisor）。
- 搜索时定向找各档一手源，②/③ 优先 HR 高管工作坊、管理层 Offsite 官方 SOP、高管教练方法论。
- 若采集内容仅适配 ① 但主题涉及 ②/③，必须醒目标注「适用关系：①平级/朋友 · 慎用于上下级」。

## 去重索引（存放）
- **主索引（真值）**：`C:\Users\v_yitcai\WorkBuddy\20260728154244\knowledge-collection\index.json`（结构化，程序比对快）。
- **人读镜像**：Obsidian「知识采集库」索引 md，每次渲染后同步更新。
- index.json 每条结构：`{ "title": "", "normKey": "", "url": "", "sourceType": "primary|secondary", "relation": "peer|supervisor|exec（可多档）", "summary": "" }`。
- 比对逻辑：URL 完全相同 → 删；标题归一化（去标点/空格/大小写）相似 → 删；摘要包含或 Jaccard > 阈值 → 删。冲突时保留一手且信息更全的一版。

## 卡片墙规范
视觉变量、字段清单、页脚硬约束见 `references/cardwall-spec.md`。核心：紫青渐变底、白卡圆角 18px、柔和投影、顶部 4px 强调色、胶囊分类标签；每张卡含 emoji + 标题 + 分类 chip + **适用关系 chip（三色，必填）** + 价值描述 + 一手/二手醒目标签 + 来源 URL + 「怎么做」折叠 + 适用备注；页脚 `📌 本页由 yitong 沉淀整理 · 文化活动知识库`。

## 注意事项
- 严格守 GitHub Pages 页脚硬约束与 SSH 推送（若需上线）。
- 一手源稀缺时，二手可补但必须醒目标注，不冒充一手。
- 落库到独立 Obsidian vault「知识采集库」，不混入「活动」vault。


---
- 回到：[[技能索引]]
