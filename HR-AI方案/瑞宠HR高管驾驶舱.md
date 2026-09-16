# 瑞宠 HR 高管驾驶舱（单页 HTML）

> 宠物生物制药 **HR 全场景高管驾驶舱**：整合 组织 / 经营 / 人效 / 绩效 / 目标 / 周报 六大模块，Bento 卡片墙风格，纯 SVG 手绘图表、自包含无外网依赖。行业视角：研发密集型宠物药企（高毛利 61% / 重研发 17%+ / 国产替代 / 覆盖 2050 家宠物医院）。

- 本地 HTML：`D:\AI\workbuddy\2026-09-10-10-04-24\hr-bitable-template\hr_exec_dashboard.html`
- 数据来源：瑞宠生物制药 HR 全场景多维表格（Base `Cy80bmoDHaxiyqs9PP8cByKbnXf`，虚拟脱敏、口径自洽）
- 模块：① 组织架构全景（部门满编率红绿灯）② 业务经营驾驶舱（月度趋势/产品线/渠道管线）③ 人效分析（营收部门人均营收 vs 支持部门人均核心产出）④ 绩效管理（强制分布环图/C级预警）⑤ 业务目标 & 周报（健康度红绿灯）
- 生成脚本：`hr-bitable-template/generate_html.py`（读 `hr_dashboard_data.json` 内联）；数据导出：`hr-bitable-template/export_biz_data.py`
- 说明：飞书仪表盘原生仅数字/柱/饼/折线/文本，此 HTML 补足组织树/红绿灯/卡片墙等高管视觉（方案乙）
