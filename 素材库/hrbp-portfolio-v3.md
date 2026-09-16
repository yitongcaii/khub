# HRBP 求职作品集 v3（创意编辑式 / Editorial Dossier）

> 线上：https://yitongcaii.github.io/activities-html/portfolio/hrbp-portfolio-v3.html
> 源文件：D:\AI\workbuddy\2026-08-25-18-13-30\hrbp-portfolio-v3.html
> 前序：v1（hrbp-portfolio.html，Bento 卡片墙）、v2（hrbp-portfolio-v2.html，v1+视觉止血+联系入口）均保留不动

## 设计概念
**Editorial Dossier / 工作法手册**——像一本高端咨询 / 创意机构案例集，而非 SaaS 模板。
配色：墨黑 `#17150F` + 暖纸白 `#F4F1E8` + 朱红 `#BE3A2B`（唯一强调色）。
字体：标题 / 刊名 / 大数字用衬线（Songti SC / Georgia 栈），正文黑体（PingFang / 雅黑栈）。
零圆角、零阴影、零渐变、零玻璃拟态。只用细线(1px)、留白、字号对比、朱红。

## 信息架构（重排，非原顺序）
身份(Masthead) → 01 思考方式(大字宣言) → 02 数据成果(细线数据带) → 03 代表项目(4 种差异化版式) → 04 方法论·AI(两栏索引) → 05 能力沉淀(清单) → 06 作品(编辑式链接) → 07 关于我

## 视觉节奏（7 种以上差异化版式，相邻不重复）
- Masthead 不对称刊头（左大刊名 / 右元数据）
- Large statement 满宽大字 + 关键词行
- Stat band 细线分隔超大衬线数字（非卡片）
- P01 Process System 节点流程图（核心反色）
- P02 Step line 四步细线编号
- P03 Case Study 不对称分栏（左超大 40+ / 右动作）
- P04 Before / After 反色对比块
- Index list 两栏编号索引
- Tool list 细线清单
- Work links 下划线生长链接

## 意外但合理的细节
1. 刊号 No.HR-01 + 章节 01–07 红编号贯穿（隐形导航 + 记忆点）
2. After 块反色黑底，打破全页纸色节奏
3. "做成系统"签名句在 01 与 07 首尾呼应
4. 数据数字 / 标题保留 contenteditable + localStorage（`hrbp3_` 隔离），但视觉隐形（无占位符、聚焦才显细红框）

## 2 轮视觉优化
- 轮1：确认无 AI 模板指纹（无 hero+3 卡、无等宽 card grid、无蓝紫渐变、无圆角阴影、无 timeline / 雷达 / 进度条）
- 轮2：删冗余 `nth-child` CSS；移动端 flow 节点 `width:100%` + arrow 旋转；窄屏 `ptag` 取消 `margin-left:auto` 避免挤压

commit `43d82c2`。
