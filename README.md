# Robotics Company Deep Research

一个面向机器人研究人员的长期技术尽调项目。项目选择值得持续关注的机器人公司，系统研究其技术路线、能力证据、部署闭环与尚未解决的问题，而不是复述公司营销材料。

[研究门户](https://asandstar.github.io/Robotics-Company-DeepResearch/) · [GitHub 仓库](https://github.com/asandstar/Robotics-Company-DeepResearch)

## Research scope

每家公司原则上沿同一组问题展开：

- 公司认为行业真正的技术瓶颈是什么；
- 创始团队、研究谱系与历史能力如何影响路线选择；
- robot embodiment 如何约束数据、模型和部署；
- 数据引擎如何采集、清洗、标注、筛选和回流；
- foundation model、VLA、WAM 或 policy architecture 如何设计；
- pre-training 与 post-training 分别解决什么；
- deployment loop 如何把真实运行转化为下一轮改进；
- failure recovery、generalization 与 safety 如何评估；
- infrastructure 是否支持可重复的大规模实验；
- benchmark 与 evaluation 是否具有明确协议、样本量和边界；
- 商业部署究竟是 demo、pilot、内部评测还是客户生产运行；
- 每个重要 claim 与 evidence 是否匹配；
- 哪些关键研究问题仍未解决。

## Companies

### [Sunday Robotics](./Sunday.html)

**状态：Published — 已有完整研究报告**

主题：家庭机器人、Memory Glove、human demonstration、ACT、Diffusion Policy、deformable manipulation、force control、recovery、generalization、home robot safety。

### [Dyna Robotics](./Dyna.html?v=ui2-20260819)

**状态：Published — 已有完整研究报告**

主题：human-video scaling、World-Action Models、cross-embodiment、task-specific post-training、million-hour infrastructure、commercial deployment 与 evidence auditing。

当前可审计材料：

- [Dyna research audit](./docs/reports/2026-08-18_dyna_research_audit_v1.md)
- [Research timeline](./docs/dyna/dyna_research_timeline_v1.md)
- [Research questions](./docs/dyna/dyna_research_questions_v1.md)
- [Dyna-2 scientific audit](./docs/dyna/dyna_2_scientific_audit_v1.md)
- [Dyna-2 infrastructure deep dive](./docs/dyna/dyna_2_infrastructure_deep_dive_v1.md)

## Research methodology

研究路径统一为：

`Problem Definition → Embodiment → Data → Model → Infrastructure → Evaluation → Deployment → Failure → Safety → Evidence Boundary → Open Research Questions`

重要 claim 应尽量记录以下字段：

- source、source type 与 publication date；
- evidence strength 与可支持的结论范围；
- evaluation scope 与 protocol；
- adaptation cost 与 robot-data requirement；
- human intervention status；
- third-party verification；
- unknowns 与推荐写法。

结构化登记表用于保存可机器检查的证据链，叙事报告则解释证据为什么重要、能够说明什么以及不能说明什么。

## Evidence policy

项目明确区分五类证据：

1. **Official company claim**：公司官网、研究页、新闻稿或创始人公开材料。它能证明公司作出过该声明，不自动证明声明已被独立复现。
2. **Peer-reviewed / research result**：论文、正式技术报告或原始研究项目页。记录实验协议、数据范围、基线和局限；未经过同行评审的公司研究页不会被写成 peer-reviewed 结果。
3. **Standards / authoritative source**：标准机构、官方技术文档、公司登记或其他权威原始记录。
4. **Third-party evidence**：高质量英文媒体、投资机构原始公告、客户可归属证词或第三方现场观察。投资人文章与公司案例仍可能存在利益关系，需单独标注。
5. **Research inference**：基于公开材料作出的分析判断，必须与原始事实分开，并说明不确定性。

优先使用英文一手资料、原始论文、官方研究页面、标准机构和高质量英文第三方来源。不使用中文网站作为研究证据。数字未经独立验证时采用“公司报告”“官方披露”“客户案例称”等限定语。

## Repository structure

```text
index.html                         多公司研究门户
Sunday.html                        Sunday Robotics 完整报告（稳定 URL）
data/                              source、claim 与 demo/deployment registries
docs/dyna/                         Dyna 专题研究底稿
docs/reports/                      带日期的审计与版本报告
.github/workflows/deploy-pages.yml GitHub Pages 发布工作流
```

GitHub Pages 发布研究门户、Sunday 报告与 [Dyna 交互式技术尽调报告](./Dyna.html?v=ui2-20260819)。结构化 Dyna registries 与审计底稿继续保留为页面的 source of truth。

## Update policy

- 研究以明确的截止日期形成版本快照；Dyna v1 截止于 **2026-08-18**。
- 新证据优先追加到 source registry，再更新 claim、demo/deployment audit 与叙事报告。
- 重大数字变化保留旧版本和变更原因，不静默改写历史判断。
- 链接、日期、外键、优先级和第三方验证状态在发布前执行检查。
- 公司材料更新不等于证据等级自动提升；只有新增协议、原始数据、独立观察或复现才能改变证据强度。

## Companies roadmap

1. 对 Sunday 与 Dyna 建立可比的 deployment、generalization、safety 和 adaptation-cost 字段。
2. 逐步加入其他具有明确技术路线与可审计公开材料的机器人公司。
3. 建立跨公司的 benchmark、claim strength 和证据缺口比较视图。
