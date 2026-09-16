---
title: 代码库蒸馏 · training-management 培训运营平台（网站借鉴参考）
tags: [技术参考, 代码库蒸馏, 全栈架构, Express, MongoDB, 前端组织, AI集成]
source: 腾讯内部分享包 training-management-share.tar.gz（龙嘉婷分享，仅架构借鉴，禁公网复用内部接口）
created: 2026-09-16
---

# 代码库蒸馏 · training-management（培训运营平台）

> **用途**：y 以后做网站（尤其 training-pkg 培训网站）时，AI 可直接检索本笔记借鉴架构与实现模式。
> **性质**：腾讯内部项目，含内网接口/key，**只学架构思路，禁止公网部署或复用内部 API**（`*.woa.com`/`*.oa.com`、Venus key、iwiki token 等）。
> **来源包**：`D:\博闻多识一堂课\training-management-share.tar.gz` → 已解压至工作区 `training-pkg-src/training-management-share/`
> **读懂前提**：本包是「A 类自搭后端」（Express + MongoDB + 原生前端），不是轻量表格方案。

---

## 0. 项目速览（一眼架构）

| 项 | 值 |
|---|---|
| 技术栈 | Node + Express + Mongoose(MongoDB) + 原生 HTML/JS（**零前端构建**） |
| 后端 | 单文件 `server.js`（3409 行，含全部路由+Schema） |
| 前端 | `public/`：原生 JS 多 tab SPA，每 tab 一个 js 文件，无框架无打包 |
| 端口 | 8080，`process.env.PORT` 可配 |
| 鉴权 | **不做登录**，靠 OA 网关注入 `x-staff-id` / `x-staff-name` 请求头识别身份 |
| AI | 接内部 Venus(DeepSeek) + 混元，做相似度查重 / 简介优化 / 知识萃取 / 反馈分析 |
| 数据库 | MongoDB（`.env` 里 `MONGO_URI`）；挂了自动降级到内存数组 |
| 文件 | 头像/海报存本地磁盘 `uploads/`，头像缓存 `uploads/avatars-cache` |

**目录结构**
```
server.js              # 全栈后端：路由 + 7个 Mongoose Schema + AI封装 + 降级
public/
  index.html           # 单页，含侧边栏 tab + 各功能区 DOM + 源码保护脚本
  app.js               # 全局 state + init + 身份识别 + tab 导航 + 权限
  style.css            # 全部样式
  js/
    utils.js           # 公共工具：toast / escapeHtml / 分类配色
    tab-submit.js      # 提交主题（含 AI 查重、简介优化）
    tab-list.js        # 分享列表（搜索/批量/导入导出）
    tab-calendar.js    # 日历排期 + 导出 PNG
    tab-feedback.js    # 课后反馈问卷
    tab-knowledge.js   # STAR 知识萃取 + AI 美化
    tab-admin.js       # 后台（配置/看板/沉淀/券码）
    ai-assistant.js    # 浮动 AI 助手「小Q同学」
    poster-template.js / poster-generate.js  # 海报生成
.env.example           # 配置样例（MONGO_URI / VENUS_API_KEY / HUNYUAN_API_KEY 等）
package.json           # 依赖：express/mongoose/multer/xlsx/dotenv
```

---

## 1. 整体架构透镜（决策用）

**他为什么选自建 MongoDB 而不是飞书/企微表当库**（承接之前对话）：
1. **内网合规**：员工反馈/头像/组织数据不能出 OA 网关，SaaS 表格直接 pass。
2. **活儿是"算"不是"存"**：AI 查重、文案生成、券码核销、反馈聚类——无论数据放哪都得写后端，顺手用 MongoDB 存，少一层故障点。
3. **嵌套半结构化数据**：一条反馈挂多份打分、STAR 四段萃取不固定 → 文档库直接套，表格要拆表。
4. **个人成本最低**：他是写 Express 全栈的，mongoose 直接存 JS 对象，比研究飞书/企微开放平台 token 快。

**你借不借（给 y 的决策透镜）**：
- 轻运维、功能偏"报名/课程/打卡/反馈看板" → **走 C 类（飞书/企微表当库）**，AI 逻辑用轻量云函数补，最省力。
- 要"AI 自动萃取 + 激励券码核销 + 内部合规不出网" → 才值得照他这套 A 类来。
- ⚠️ 即便走 C 类，AI 计算逻辑仍要个地方跑（EdgeOne 云函数 / Supabase 函数），表格只替你存不替你算。

---

## 2. 可借鉴模式（7 大类 · 共 15 条）

每条格式：**模式 / 借鉴点 / 代码证据（文件:行）/ 坑·注意**

### A. 架构骨架
**A1 零构建全栈** — 单文件 `server.js` + `public/` 原生 JS，`node server.js` 即跑，不用 webpack/vite。
- 证据：`server.js:1-51`（express 初始化 + `express.static(public)`）
- 坑：单文件 3409 行难维护；>2000 行应拆 `routes/` 模块。小原型可学，生产要拆。

**A2 配置外置 .env** — `require('dotenv').config()`；所有敏感项走 env，代码里只留 `process.env.X || '默认值'`。
- 证据：`server.js:1,12-13`；`.env.example`（MONGO_URI / VENUS_API_KEY / HUNYUAN_API_KEY）
- 借鉴：密钥/连接串绝不进代码；`.env` 进 `.gitignore`。

### B. 容错降级
**B1 数据库降级容错** — `dbConnected` 开关 + 内存数组兜底，Mongo 挂了服务不崩。
- 证据：`server.js:1032` `let dbConnected=false` + `memoryTopics/memoryGiftCodes/...` 数组（L1033-1049）；连接失败时路由切内存。
- 借鉴：依赖挂了服务不死，给运维留窗口。
- ⚠️ 坑：内存模式**重启丢数据、多人不同步**——仅应急，绝不能当生产存储。

**B2 外部 AI 双保险** — 优先调 AI 语义接口，catch 后回退本地算法，体验不中断。
- 证据：`server.js:675-718`（check-similarity：先 Venus DeepSeek，失败 `console.warn` 后走 `calculateSimilarity` 本地 Jaccard bigram）
- 借鉴：**任何外部依赖都要有本地兜底**（AI/支付/短信皆适用）。

### C. AI 集成
**C1 统一 AI 调用抽象** — `callAIModel(provider, key, url, prompt, model)`，venus/hunyuan 可切换。
- 证据：全项目统一调 `callAIModel('venus', VENUS_API_KEY, VENUS_API_URL, prompt, 'deepseek-v3.2')`（如 L694）
- 借鉴：多模型可切换、易替换、易做 A/B。

**C2 AI 输出强约束** — prompt 规定严格 JSON 格式 + 本地解析容错。
- 证据：`server.js:684-703`（要求 `{"results":[{"title","similarity"}]}`，只留 ≥20 的项）
- 借鉴：让 AI 输出可信、可机读；前端务必 try/catch JSON.parse。

### D. 数据建模（Mongoose Schema 范式）
**D1 状态机枚举** — 券码 `status: enum['unused','assigned','sent']`，唯一码 + 重复报错 11000。
- 证据：`server.js:90-105`（giftCodeSchema）；导入重复码走 Mongo 唯一索引报错。
- 借鉴：用 DB 原生能力表达业务状态，少写 if。

**D2 嵌套半结构** — 一条反馈挂多份打分 `topicScores:[{topicId,content,speaker,overall}]`；萃取内容不固定。
- 证据：`server.js:108-130`（feedbackSchema 内嵌 feedbackTopicScoreSchema）
- 借鉴：文档库天然适配嵌套；关系库得拆关联表。

**D3 高频查询建索引** — `dateKey`/`topicId` 标 `index:true`。
- 证据：`server.js:116-117`
- 借鉴：列表/筛选高频字段必须建索引，否则全表扫。

**D4 通用 KV 配置表** — `configSchema {key:unique, value:Mixed}` 存全站开关/模板/管理员列表。
- 证据：`server.js:82-87` + `memoryConfigs`（L1037-1046）存默认模板
- 借鉴：一个表管所有配置，免建多表；改配置热更新免发版。

### E. 数据访问层
**E1 统一存储切换** — `getAllTopicsData()` 按 `dbConnected` 返回 Mongo 或内存，业务代码不感知。
- 证据：`server.js:1083-1095`（Topic/Feedback 同理）
- 借鉴：**业务层不感知存储实现**，换库（Mongo→飞书表）零改业务。

### F. 前端组织
**F1 零打包 SPA** — 全局 state（`topics/configs/currentUser`）+ `initTabs()` + 每 tab 一个 js，script 顺序加载。
- 证据：`app.js:19-45`（state + DOMContentLoaded init）+ `index.html:1011-1024`（script 顺序）
- 借鉴：免构建、改完即时看、部署极简。
- ⚠️ 坑：全局函数污染、无类型检查；大项目建议上框架。

**F2 权限双层** — 前端 `applyPermissions` 显隐 admin tab + 后端 `staffId`/admin 校验。
- 证据：`app.js:116-138`（前端显隐）+ 后端路由读 `req.staffId`（L57）验 admin
- 借鉴：**前端只管体验，后端才是真相**；前端显隐可被绕过，敏感操作后端必验。

**F3 渲染必 escape** — 所有用户输入渲染前 `escapeHtml`。
- 证据：`utils.js:31-36`（`escapeHtml` 用 textContent 转义）
- 借鉴：渲染用户内容必须 escape，防 XSS 注入。**这是硬规则，不是可选项**。

### G. 交互与批量
**G1 公共工具库** — `showToast` / `escapeHtml` / 分类配色映射抽进 `utils.js` 全局复用。
- 证据：`utils.js:3-43`
- 借鉴：通用函数集中，避免每页重写。

**G2 XLSX 批量导入导出** — `multer` 接文件 + `xlsx` 解析 + 模板下载。
- 证据：`server.js:386/474/990`（import-preview/import-confirm/import）+ `937`（template 下载）+ `879`（export）
- 借鉴：管理后台标配导入导出；先下模板→预览→确认，防脏数据进库。

**G3 防丢加固** — 草稿 `localStorage` 自动存、头像 letter avatar 兜底、html2canvas 导出 PNG。
- 证据：`app.js:24-26`（DRAFT_KEY + initDraftAutoSave）、`app.js:100-114`（renderLetterAvatar 首字母渐变兜底）、`index.html:12`（html2canvas）
- 借鉴：长表单防误关丢数据；头像加载失败有兜底；排期/海报可导出图片离线用。

### H. 安全 tricks
**H1 CSP 精细化** — 中间件按域名放行内网头像，限制 `connect-src` 内网。
- 证据：`server.js:28-39`（Content-Security-Policy 头）
- 借鉴：内网 CSP 思路防 XSS/外联泄漏；公网按自己域名收紧。

**H2 源码保护（弱）** — 禁 F12 / 右键 / Ctrl+U + base64 文本注入混淆文案。
- 证据：`index.html:13-22`（禁开发者工具）、`app.js:1-17`（base64 注入标题/占位符防改）
- 借鉴思路：内网防手滑够用。
- ⚠️ 坑：**公网形同虚设**，真安全靠后端鉴权，别依赖"禁 F12"。

---

## 3. 直接可抄的代码片段（精选）

### ① 内存降级骨架（B1/E1 配套）
```js
// server.js
let dbConnected = false;
const memoryTopics = [];      // Mongo 不可用时的兜底数组
const memoryFeedbacks = [];

async function getAllTopicsData() {
  if (dbConnected) return Topic.find().lean();
  return [...memoryTopics];    // 业务层无感知切换
}
```

### ② AI 双保险（B2/C2）
```js
// server.js:656-718 简化
app.post('/api/topics/check-similarity', async (req, res) => {
  // 优先 AI 语义查重
  if (VENUS_API_KEY) {
    try {
      const aiResult = await callAIModel('venus', VENUS_API_KEY, VENUS_API_URL, prompt, 'deepseek-v3.2');
      if (aiResult?.results?.length) return res.json(filtered);
    } catch (e) {
      console.warn('Venus failed, fallback to local:', e.message);
    }
  }
  // 降级：本地 Jaccard bigram
  const sim = existingTitles.filter(t => calculateSimilarity(title, t.title) > 0.3)
    .map(t => ({ title: t, similarity: Math.round(calculateSimilarity(title,t.title)*100) }));
  res.json(sim.slice(0, 5));
});
```

### ③ 统一数据访问（E1）
```js
async function getAdminList() {
  try {
    if (dbConnected) {
      const config = await Config.findOne({ key: 'admins' });
      return config ? config.value : [];
    }
    return memoryConfigs.get('admins') || [];
  } catch (e) { return []; }
}
```

### ④ 防 XSS + Toast（F3/G1）
```js
// utils.js
function escapeHtml(str) {
  if (!str) return '';
  const div = document.createElement('div');
  div.textContent = str;          // 用浏览器 API 转义，比正则稳
  return div.innerHTML;
}
function showToast(msg, type = 'info', duration = 2500) {
  const toast = document.getElementById('toast');
  toast.textContent = msg;
  toast.className = `toast ${type} show`;
  setTimeout(() => { toast.className = 'toast'; }, duration);
}
```

### ⑤ 状态机 Schema（D1）
```js
const giftCodeSchema = new mongoose.Schema({
  code: { type: String, required: true, unique: true },
  status: { type: String, enum: ['unused', 'assigned', 'sent'], default: 'unused' },
  assignedTo: { type: String, default: '' },
  assignedAt: { type: Date, default: null },
  sentAt: { type: Date, default: null },
});
```

### ⑥ XLSX 模板下载（G2）
```js
// 思路：服务端用 xlsx 构造空模板（含表头）→ 写 buffer → 设
// Content-Disposition: attachment 触发浏览器下载；导入则 multer 接文件后 xlsx.parse
app.get('/api/topics/template', async (req, res) => {
  const ws = XLSX.utils.json_to_sheet([{ title:'', speaker:'', shareDate:'' }]);
  const wb = XLSX.utils.book_new(); XLSX.utils.book_append_sheet(wb, ws, 'template');
  const buf = XLSX.write(wb, { type:'buffer', bookType:'xlsx' });
  res.setHeader('Content-Disposition', 'attachment; filename=topics-template.xlsx');
  res.send(buf);
});
```

---

## 4. 做 training-pkg · 对号入座

| 你要的功能 | 借鉴哪条 | 推荐路线 |
|---|---|---|
| 报名 / 提交主题 | A2 env + F1 零打包 SPA | C 类：前端零构建 + 飞书/企微表当库 |
| 课程 / 日历排期 | F1 + G2 导入导出 | 前端 + 表格存排期，日历渲染 |
| 课后反馈问卷 | D2 嵌套打分 + F3 escape + G3 草稿 | 表格存反馈，前端渲染，必 escape |
| 数据看板 / 统计 | G2 + 后端 stats 聚合路由 | 表格读聚合，或 Supabase 视图 |
| AI 简介优化 / 查重 | C1/C2 + B2 双保险 | 轻量云函数跑 AI，本地算法兜底 |
| 激励券码发放 | D1 状态机 + G2 批量导入 | C 类用表格列模拟状态机（unused/assigned/sent） |
| 知识萃取（STAR） | C1 + D2 嵌套 | 云函数跑 AI，表格存萃取结果 |
| **多人实时同步** | —— 他**没做**（纯 REST，需刷新） | 要秒级实时需 WebSocket/SSE，或直接用飞书表近实时 |

> 关键提醒：参考项目**没有实时推送**（无 WebSocket/SSE），A 改了 B 要手动刷新。你若要"多人同时看到更新"，要么走飞书表（近实时），要么自建加 WebSocket。

---

## 5. 安全与合规提醒（必读）

1. **内部接口/key 禁公网**：Venus/混元 key、`*.woa.com`/`*.oa.com` 地址、iwiki token 都是腾讯内网，**只学架构，绝不公网复用或提交**。
2. **禁 F12 是装饰不是安全**（H2）：公网项目靠后端鉴权，前端混淆/禁开发者工具形同虚设。
3. **内存降级不能当生产**（B1）：Mongo 挂了降级到内存，重启丢数据、多人不同步，仅应急。
4. **OA 网关鉴权思路可借鉴**（F2 来源）：公网项目要做真登录（企微/OAUTH），不能靠请求头信任身份。
5. **渲染用户内容必 escape**（F3）：所有 `innerHTML` 拼接用户输入前 `escapeHtml`，防 XSS。

---

## 附：本包与「你之前想的 C 类方案」对照
| | 参考项目（A 类） | 你倾向的 C 类 |
|---|---|---|
| 存储 | MongoDB（自运维） | 飞书/企微表（平台兜底） |
| 后端 | Express 全栈自己写 | 零数据库运维，AI 逻辑用云函数 |
| 实时 | 无（需刷新） | 表格近实时 |
| 适合 | 内网合规 + 重 AI 逻辑 | 轻运维 + 报名/课程/反馈看板 |
