---
title: ZZ-定时小复盘自动化交接文档（副本）
tags: [交接, 索引, 自动化, meta]
type: 自动化采集
date: 2026-08-07
---

> ⚠️ **本文件是 workspace 主文档的完整副本（非纯指针）**。主文档修改后须在**同一回合**同步更新本副本，避免内容过期。
> 主文档：[`定时小复盘自动化-交接文档.md`](file:///C%3A/Users/v_yitcai/WorkBuddy/20260728154244/%E4%BA%A4%E6%8E%A5%E6%96%87%E6%A1%A3/%E5%AE%9A%E6%97%B6%E5%B0%8F%E5%A4%8D%E7%9B%98%E8%87%AA%E5%8A%A8%E5%8C%96-%E4%BA%A4%E6%8E%A5%E6%96%87%E6%A1%A3.md) ｜ [在 Obsidian 打开](obsidian://open?path=C%3A%5CUsers%5Cv_yitcai%5CWorkBuddy%5C20260728154244%5C%E4%BA%A4%E6%8E%A5%E6%96%87%E6%A1%A3%5C%E5%AE%9A%E6%97%B6%E5%B0%8F%E5%A4%8D%E7%9B%98%E8%87%AA%E5%8A%A8%E5%8C%96-%E4%BA%A4%E6%8E%A5%E6%96%87%E6%A1%A3.md)

---
title: 定时小复盘自动化 · 交接文档
tags: [协作手册, 方法, 自动化, 活动/方法]
source: WorkBuddy 工作区交接文档（2026-08-05 生成）
关联笔记: [[协作使用说明书]] [[新人答辩MCP应用设计方案]] [[工作规划复盘]]
---

# 🪧 交接文档 · 定时小复盘自动化

> 写给**完全没上下文的新会话**看。一句话：每 2 小时提醒 y 做一次 ≤2 分钟微复盘，落进 Obsidian，并推企微提醒。

---

## 🎯 我们在做什么

一个「趁热打铁」的高频复盘机制。y 约定了**每 2 小时一次微复盘**（源于 2026-07-29 人生导师式对话）。机制很简单：

- **步骤1**：往 Obsidian 笔记 `C:\Users\v_yitcai\Documents\Obsidian\活动\工作规划复盘.md` 的 `## 记录` 节末尾，追加一条骨架记录（callout + 任务清单样式，三项留空：①做了什么 ②卡点 ③下一步），等 y 口述后由 AI 整理。
- **步骤2**：用企微机器人给 y 建一条待办/提醒，作为「跨设备闹钟」——企微日程被企业拦截、且原生不支持「每2小时」间隔，故用待办替代。

> 触发：工作日每 2 小时（rrule `FREQ=HOURLY;INTERVAL=2;BYDAY=MO,TU,WE,TH,FR`），自动化 id=`automation`，脚本 `wecom_todo_create.js`。

---

## ✅ 已完成

- ✅ **已稳定** 步骤1：Obsidian 追加骨架记录已跑通，2026-07-29 起累计 40+ 条，仅追加不覆盖。
- ✅ **已稳定** 早期（7/29–8/3 上午）步骤2 企微待办创建成功（errcode 0），todo_id 可查。
- ✅ **已交付** 新增跨设备兜底脚本 `wecom_msg_push.js`（走南瓜 webhook，未启用）。

---

## 🚧 卡在哪

两个卡点（其实是同一个根因）：

- 🔴 **阻塞** 企微「待办」API 权限过期：自 **2026-08-03 09:35** 起，每次建待办都报 `errcode 850003 authorization expired`。机器人的「待办」使用权限需创建者重新授权（链接见下）。
- 🔴 **连带** 跨设备提醒断链——待办是唯一的手机桥梁，桥一断，y 在手机上收不到复盘提醒。
- 🔴 **连带** 记忆噪声：自动化每 2h 把同样的失败追加进 `memory.md`，20+ 条重复失败淹没了「需重新授权」这一条有用信号。

---

## 🛠 解决方案

已设计、待拍板启用：

- 🔧 **方案B 换通道到南瓜 webhook**：`wecom_msg_push.js` 走群机器人 webhook（key `ea87c4eb-23b2-4f05-a44f-272b2327ca4b`，2026-07-31 实测有效），是**另一套权限**，不受待办过期影响，消息直接进群→手机实时可见。已写好脚本，待切换。
- 🔧 **方案A 记忆熔断**：在 `memory.md` 顶部加 `## 已知阻塞` 区块，每次运行先读它；遇活跃阻塞就跳过待办调用、只记一行 `skipped(已知阻塞)`，清掉重复失败噪声。
- 🟡 **方案C** 若 y 仍想要真·待办弹窗：点下方链接重新授权即可恢复，保留 todo 形态。

---

## ⚠️ 踩过的坑 · 别再踩

新会话请直接照做：

1. ❌ **别** 反复重试已过期的待办 API 并刷重复失败日志——先读 `memory.md` 顶部的「已知阻塞」，遇 `850003` 直接跳过建待办。
2. ❌ **别** 把「待办」和「群机器人 webhook」混为一谈：**待办 API ≠ webhook 推送**，两者权限独立。待办过期 ≠ webhook 失效，南瓜 webhook 仍可用。
3. ❌ **别** 改 Obsidian 笔记时覆盖已有记录——只往 `## 记录` 节**末尾追加**，用唯一锚点定位再写（之前有多重匹配坑）。
4. ❌ **别** 用 `wecom-cli` 的 `--json` 经 PowerShell 传参——花括号会被二次编码报 `key must be a string`。正确做法见 `wecom_todo_create.js`：用 Node `spawnSync` 直接传 argv 数组。
5. ✅ **要** 跨设备提醒优先用南瓜 webhook（markdown 推送），它不依赖待办权限、手机必到。
6. ✅ **要** 把「阻塞/注意事项」写进**自动化自己的** `memory.md`（它每轮会读），而不是只写工作区 `.workbuddy/memory/`——后者自动化不会加载。

---

## 📦 关键资产 / 路径

- Obsidian 复盘笔记：`…\Obsidian\活动\工作规划复盘.md`
- 待办脚本（过期通道）：`wecom_todo_create.js`
- webhook 推送脚本（推荐通道）：`wecom_msg_push.js`
- 执行历史：`.codebuddy\automations\automation\memory.md`
- 南瓜 webhook：`https://qyapi.weixin.qq.com/cgi-bin/webhook/send?key=ea87c4eb-23b2-4f05-a44f-272b2327ca4b`

---

## 🔑 待办权限重新授权（方案C用）

> 机器人「待办」使用权限已过期，创建者点此授权：
> 👉 https://work.weixin.qq.com/ai/aiHelper/authorizationList?from=chat&forceInnerBrowser=1&aibotid=33777000851280625&str_aibotid=aibpgZRPCykH7n97Hz91eaPkbkYpCw2Dic3&type=5&ww_vw=640&ww_vh=640&for_native=true&bar_style_type=3&pc_bar_bg=f6f6f7%2C202021&hide_more_btn=true
> 非创建者请联系创建者去企业微信「工作台-智能机器人」授权。

---

## 📌 当前待 y 拍板

- 🟡 是否把自动化提醒通道从「待办 API」切到「南瓜 webhook」（推荐：切，robust 不依赖授权）。
- 🟡 是否启用「记忆熔断」（推荐：启用，清噪声）。
- 两者都确认后，改自动化配置：步骤2 改调 `wecom_msg_push.js`；`memory.md` 顶部加阻塞区块。改完即生效。

---

> 交接文档生成于 2026-08-05 · 面向零上下文新会话 · 状态：步骤1 稳定，步骤2 待办通道过期、webhook 通道已备好待启用
