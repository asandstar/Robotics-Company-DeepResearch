# Sunday Robotics 深度研究报告 v1.0 审计

审计日期：2026-07-18  
审计对象：`sunday_robotics_research_report.html`  
目标版本：v1.1 Evidence Audit and Technical Deep Dive  
审计方式：完整读取当前 HTML；解析 DOM 锚点、正文引用、图片、CSS 与 JavaScript；对 17 个外部来源 URL 做可达性/元数据抽查。本轮未修改 HTML、未移动文件、未创建前端实现。

## 1. 结论摘要

v1.0 已经具备清晰的研究叙事、较好的来源类型意识，以及优于普通产品介绍的证据边界讨论；但它还不是可复核的 evidence audit。最重要的问题不是页面视觉，而是 claim、来源、评测单位与限定条件没有形成一一对应的机器可读链路。

### P0：v1.1 发布前必须处理

- 将所有强 claim 改为与来源类型相称的措辞。公司自评必须写成“Sunday 报告/声称/展示”，不能写成独立验证事实。
- 固定关键数字的分母与定义：99.1% = 778/785；2:13 只覆盖 778 次成功折叠，且包含自主重试与恢复；68 是一次长程任务中报告的总灵巧交互数，不是 68 次评测。
- 首次出现 `zero robot data` 时解释为“没有机器人遥操作轨迹”，而不是没有任何人类示范、机器人 rollout、标定或其他机器人相关数据。
- 将 `zero adaptation cost` 限定为 ACT-2 页面定义的 `zero per home`：无目标家庭/衣物专属数据、专家示范或后训练；地图、标定、安装、人工重置、监控和故障处置是否为零仍未知。
- 为 Claim Evidence Matrix 的每张卡增加直接引用；修复“百人规模”“自主充电”“2024 创立”等目前无可审计正文引用或来源不足的 claim。
- 把 ISO 13482 与 UL 3300 来源从 `P/Paper` 改为 `S/Standard` 或 `Standards body`。标准相关性不能写成 Sunday 已认证。
- 把 demo 证据从“至少成功一次”与“统计可靠性”严格分开；人为打断、儿童接触、双杯、袜子、自主充电均需登记原始视频、时间戳、剪辑、尝试数、介入与重置规则。

### P1：应在 v1.1 同期处理

- 给 17 条参考资料补作者/机构、发布日期、访问日期、版本/venue 和正文引用覆盖；给 Business Insider 条目增加 id 或移除未使用来源。
- 减少执行摘要、ACT、Claim Matrix、泛化/安全章节之间的重复，以 claim registry 为唯一事实层。
- 拆分源码与发布产物，建立 `assets/css/js/data` 和图片资产目录；继续生成离线 `dist` 单文件。
- 修复键盘可达性、lightbox 焦点管理、搜索标签、主题状态、`prefers-reduced-motion`、打印状态污染与长 section 的分页风险。
- 为图片增加尺寸、懒加载/解码策略和格式优化；当前约 5 MB 单文件会增加首屏解析、base64 解码和内存峰值。

### P2：可在技术深挖阶段持续改善

- 把室内风格和品牌意图降级为明确标注的视觉分析，不与技术证据争夺篇幅。
- 给论文结论增加表格/章节定位，区分 arXiv、会议与期刊版本。
- 给全部图表、公式、表格增加可打印说明、单位和文本替代。

## 2. 页面结构审计

### 2.1 Section 清单

当前共有 23 个 `section`（1 个 hero + 22 个正文 section），目录覆盖全部 22 个正文 section，未链接 hero 属合理设计选择。

| 顺序 | id | 标题 | 主要功能 | 主要重复/重叠 |
|---:|---|---|---|---|
| 0 | `top` | Sunday Robotics 深度研究报告 | Hero、版本、研究对象、核心判断 | 与 `summary`、`claims` 重复总体评级 |
| 1 | `summary` | 执行摘要 | 路线、硬件、团队、最强结果、缺口 | 与 `system`、`act`、`claims`、`safety` 重复 |
| 2 | `evidence` | 证据规则 | O/P/T/I 来源规则与评测边界 | 与 `claims` 的 evidence/scope/adaptation 结构重复 |
| 3 | `macro` | “Macro Data Refinement” 与家居视觉语言 | 文化梗、室内风格、品牌解读 | 技术关联弱；与 hero 品牌判断轻度重复 |
| 4 | `company` | 公司档案 | 创始人、融资、估值、beta、瓶颈判断 | 与 `lineage`、`claims` 组织信号重复 |
| 5 | `lineage` | 创始人的研究谱系 | ALOHA/ACT/DP/UMI 研究连续性 | 与 `diffusion`、`act` 重复论文/时间线 |
| 6 | `system` | 从人类经验到机器人技能 | 数据—训练—部署闭环与技术问题 | 与 `summary`、`glove`、`developer` 重复流程 |
| 7 | `embodiment` | Memo 的机器人本体选择 | 轮式本体、速度与风险 | 与 `safety` 重复 passive stability、50%、compliance |
| 8 | `glove` | Memory Glove 究竟采集什么 | 相机、关节、力信号与未知项 | 与 `system`、`developer` 重复数据采集 |
| 9 | `developer` | Memory Developer 与 smooth fluid choreography | 示范员角色与数据质量 | 与 `system` 的 QA/覆盖/闭环重复 |
| 10 | `transparent` | 透明物体为何难以感知 | 玻璃感知、双杯 demo、实验建议 | 与 `force` 的玻璃抓取风险重复 |
| 11 | `deformable` | 袜子和衣物为何真的很难 | 柔性物体状态、遮挡、摩擦、质量 | 与 `force`、`act` 的衣物内容重复 |
| 12 | `force` | 力控难点究竟在哪里 | 摩擦约束、玻璃/衣物接触控制 | 与 `transparent`、`deformable`、`glove` 重复 |
| 13 | `recovery` | 被打断后重新计划为什么困难 | 恢复流程、记忆因果识别 | 与 `act` 的 emergent behavior、`questions` 重复 |
| 14 | `generalization` | 对新场景泛化到底意味着什么 | 泛化维度与 Sunday 证据 | 与 `evidence`、`act`、`claims` 重复 scope/adaptation |
| 15 | `safety` | 家庭机器人安全需要几层防线 | 多层安全、公开证据与标准参照 | 与 `embodiment`、`claims` 重复 |
| 16 | `diffusion` | Diffusion Policy 深度拆解 | 方法原理、术语、边界 | 与 `lineage`、`glossary` 重复 |
| 17 | `act` | 从能力展示走向可靠性评测 | ACT-1/2 数字与 Solve | 与 `summary`、`generalization`、`claims` 高度重复 |
| 18 | `claims` | 关键 Claim 与证据边界 | 5 张可筛选 claim 卡 | 与正文事实重复但未形成规范化引用 |
| 19 | `researcher` | 研究人员关注一家机器人公司时看什么 | 通用尽调清单和指标 | 与 `evidence`、`questions` 部分重复 |
| 20 | `questions` | 可以从 Sunday 提炼出的研究问题 | benchmark/实验问题池 | 与各技术章节末尾研究建议重复 |
| 21 | `glossary` | 术语表 | 可搜索术语定义 | 与 `diffusion`、`generalization` 定义重复 |
| 22 | `references` | 参考资料 | 17 条来源与版本说明 | 应成为 source registry 的渲染结果 |

### 2.2 目录、内部引用与链接一致性

- 22 个目录链接全部指向存在且唯一的 section id；未发现重复 id。
- 31 个正文 `.cite` 链接全部指向存在的 `ref-*` id；未发现断裂锚点。
- 参考列表有 17 条：7 条公司官方、6 条研究论文、2 条标准/标准机构、2 条第三方。
- 16 条有 `ref-*` id；Business Insider 条目没有 id，正文引用数为 0。
- 正文实际引用 16 条有 id 的来源；Business Insider 是唯一未被正文锚点实际引用的参考条目。
- 引用标签 `[O1]…[P8]、[T2]` 与参考列表的 CSS 自动序号是两套体系。读者看到的列表序号 1–17 不能直接解释 O/P/T 编号；`T2` 也没有可见的 `T1` 映射。
- 参考链接均使用 HTTPS、外链均设置 `target="_blank" rel="noreferrer"`，格式基本一致；Business Insider 的可见文本不是 URL，属于轻微格式不一致。
- 2026-07-18 外链检查：15 个来源由抓取器正常打开；YouTube 与 Business Insider 在抓取器中不可读，但 URL 格式有效，不能据此判断浏览器端失效。后者可能受付费墙限制。

### 2.3 JavaScript 一致性

页面只有一个内联 script。所有直接使用的元素和集合均存在：`progress`、`topBtn`、`printBtn`、`themeBtn`、目录、filter buttons、evidence items、glossary input/items、lightbox、close button、zoomable images。

功能与 DOM 的对应关系：

| 功能 | DOM/状态 | 审计结论 |
|---|---|---|
| 阅读进度/回到顶部 | `#progress`、`#topBtn` | 一致；滚动监听为 passive |
| 主题切换 | `#themeBtn`、`data-theme`、localStorage | 可用；缺 `aria-pressed` 和系统主题首选项 |
| 打印 | `#printBtn`、`window.print()` | 可用；打印结果受当前筛选/搜索/主题状态影响 |
| 目录高亮 | `IntersectionObserver` + `main section[id]` | 一致；无兼容性降级，构造器不存在时会中断后续脚本 |
| Claim 筛选 | `.filter-btn`、`.evidence-item[data-evidence]` | 一致；打印前未自动清除 hidden 状态 |
| 术语搜索 | `#glossarySearch`、`.glossary-item` | 一致；input 只有 placeholder，无显式 label |
| 图片 lightbox | `.zoomable`、dialog、Escape | 鼠标可用；图片不可键盘触发，缺焦点圈定、焦点返回、背景 inert |

## 3. Claim 审计

完整 claim 台账见 [`data/claim_registry_v1.csv`](../../data/claim_registry_v1.csv)。本轮登记 51 条重要可核查 claim，字段覆盖 claim/source/type/strength/scope/adaptation/intervention/unknowns/revision/priority。

### 3.1 证据类型必须保持四层分离

1. **公司官方声明**：O1–O7 只能证明 Sunday 公开作出该声明或展示该案例。即使样本量大，也仍是公司执行、选择和评分的内部证据。
2. **论文结果**：P1–P6 的结论必须限定到论文任务、数据与协议。论文方法背景不能自动证明 ACT-1/2 采用同一模型结构或达到同一性质。
3. **第三方信息**：T1/T2 需记录作者、日期、采访/赞助关系和可访问性。外部报道不等于独立技术测试。
4. **报告推断**：品牌意图、完整闭环是核心壁垒、安全架构更优、公司已转向部署等都属于分析判断，应使用“显示、可能、与……一致”措辞。

### 3.2 正文中最重要的未引用或弱引用 claim

| 优先级 | Claim | 问题 | 建议 |
|---|---|---|---|
| P0 | Sunday 已进入百人规模 | Claim card 无直接引用；O6/T1 的当前条目不足以核对口径和时点 | 补可访问、带日期来源；否则删除数字 |
| P0 | 2024 创立 | 只在指标卡出现，当前第三方条目无正文锚点 | 用公司注册/权威档案核对或弱化 |
| P0 | 自主充电能力 | 安全表中出现但没有引用 | 定位原始视频和时间戳；否则改为未知 |
| P0 | 安全设计明显优于仅靠模型约束 | 是报告比较判断，不存在对照实验 | 改为“采用分层安全思路” |
| P0 | 少量内部后训练数据可提升未见环境性能 | “少量”没有样本数或消融 | 降级为公司叙事或补实验 |
| P0 | 手套双摄像头的具体功能分工 | 当前是合理推断，不是已披露接口定义 | 加“可能/推测”并列未知参数 |
| P1 | 机器人端高质量系统通常需要的传感器清单 | 是领域性建议但没有综述/教材来源 | 标成工程建议或补权威来源 |
| P2 | Demo house 的品牌目的 | 意图归因无直接采访证据 | 改为视觉效果分析 |

## 4. 数字审计

### 4.1 指定关键数字

| 数字/词组 | 当前来源 | 当前定义 | 可能歧义 | 进一步核查 | 优先级 |
|---|---|---|---|---|---|
| 99.1% | O5 ACT-2 Preview | 785 次公司称自主折衣尝试中 778 次成功；页面给 ±0.3% standard error | 不是独立评测；trial independence、家庭聚类、人工重置/接管不清楚 | 发布 trial manifest、失败视频、家庭/衣物实例数、SE 计算、预注册协议 | P0 |
| 785 次 | O5 | 9 类衣物的 autonomous attempts | attempt 起止、超时、硬件故障、重试是否新 attempt 不清楚 | 固定 reset/timeout/abort 规则和每条运行日志 | P0 |
| 2:13 | O5 | 778 次**成功**折叠的中位时间；取衣开始到加入衣堆；含自主重试/恢复 | 当前摘要卡容易被读成全部尝试或端到端洗衣时间；失败耗时未计 | 同报均值 2:19、失败耗时、超时和人工重置时间 | P0 |
| 68 次交互 | O4 ACT-1 | Table-to-Dishwasher 中公司报告的 68 次总 dexterous interactions | 容易误读为 68 次试验；interaction 的分段规则未知 | 明确是否单次连续 rollout、交互事件 taxonomy 和原始事件日志 | P0 |
| 33 种交互 | O4 | 33 unique dexterous interactions | “unique”的去重规则和类别表未公开 | 发布 33 类清单、映射与计数方法 | P0 |
| 21 个物体 | O4 | 21 different objects | 实例/类别口径、是否含垃圾/机器部件未知 | 发布对象清单和实例 ID | P0 |
| 50% 人类自然速度 | O1 | 多数任务当前按人类自然 pace 的 50% 训练和运行 | 人类基线、任务集合、移动/手臂口径、速度还是时长缩放不清楚 | 给每任务绝对速度、完成时间、人类样本和测量方法 | P0 |
| zero robot data | O4 | 原文更精确为没有一条 robot teleoperation trajectory；仍使用人类手套示范 | 可能被误读为无机器人 rollout、无标定、无合成数据、无机器人观测 | 列训练数据 taxonomy，并说明哪些数据被排除/保留 | P0 |
| zero adaptation cost | O5 | zero per home：无目标家庭/衣物数据、专家示范或 post-training；同 checkpoint/config | 地图、标定、安装、人工重置、监控、故障处置和运维是否计入未知 | 用统一成本表报告分钟、人次、数据量、算力、地图、标定与运维 | P0 |

### 4.2 其他应保留定义的数字

- `778`：成功折叠数，是 99.1% 的分子，也是质量/速度统计的分母。
- `4.72/5`：仅在 778 次成功折叠上计算；不是全部 785 次的无条件质量。
- `98.3%`、`73.8%`：同样只以 778 个成功折叠为条件分母。
- `9 类衣物`：页面列出 shorts、厚/薄长袖、polos、sleeveless tops、T-shirts、pants、leggings、blouses。当前 HTML 的类别成功率列表漏写薄长袖、polos 和 sleeveless tops 三类，却称 9 类。
- 类别百分比必须同行显示样本量。尤其 sleeveless tops 为 `n=7`，blouses 为 `n=19`，不可只比较百分比。
- `90% Skill Transform 成功率`：O4 未公开成功定义、分母、审核方式或误差；只能写成公司报告。
- `>130 ft`：属于 Table-to-Dishwasher 路径声明；需要说明测量口径和是否单次 rollout。
- `$165M`、`$1.15B`：分别为公司融资和估值口径；估值需注明 pre/post-money 并交叉验证。
- `80%–90%`、`约 10 分钟`：ACT 论文特定 6 个真实任务的汇总，不应外推到 Memo。
- `46.9%`：Diffusion Policy 论文聚合结果，必须写清比较基线、指标和最终 venue 版本。

## 5. 来源审计

完整来源台账见 [`data/source_registry_v1.csv`](../../data/source_registry_v1.csv)。

### 5.1 覆盖与使用情况

| source_id | 类型 | 正文引用次数 | 审计要点 |
|---|---:|---:|---|
| O1 | 公司官网 | 4 | 动态页面，无稳定发布日期；安全表述是公司声明 |
| O2 | 公司技术页 | 3 | 动态页面；缺传感器/QA 的可复核规格 |
| O3 | 公司页 | 1 | 仅摘要引用，lineage 应直接引用 |
| O4 | ACT-1 官方文章 | 5 | 日期可核；属于公司研究文章，不是论文 |
| O5 | ACT-2 官方文章 | 6 | 数字最完整，但仍是内部执行/评分 |
| O6 | B 轮公告 | 1 | 应补交易口径和第三方交叉验证 |
| O7 | Beta 页面 | 1 | 原则性安全/隐私声明，不是认证证据 |
| P1–P8 | 论文/标准 | 各 1–2 | HTML 全部缺作者与日期；P7/P8 标签错误 |
| T2 | 第三方视频 | 1 | 缺日期、时间戳、转录和赞助关系 |
| T1 | Business Insider | 0 | 无 id、无作者/精确日期、可能付费墙；未被正文直接引用 |

### 5.2 重复与标签

- 未发现完全重复 URL。
- O1/O2/O7 内容部分重叠，但分别承担产品、技术、beta 角色，不属于应删除的重复来源；需要 claim-level locator，避免同一安全声明多页互相“交叉验证”。同一机构的多个页面不是独立证据。
- P7 `ISO 13482:2014` 是标准；P8 是标准机构的服务/概览页。二者当前被标成 Paper 是实质性错误。
- Diffusion Policy 正文称“同行评审”“RSS 与 IJRR”，参考却只链接 arXiv。若以同行评审状态增强证据，应链接最终 venue 页面/DOI。
- 柔性物体综述也只链接 arXiv；除非补最终 venue，不应默认称同行评审。

### 5.3 参考资料元数据缺口

当前 17 条均未在 HTML 中完整显示作者和发布日期。建议统一字段：`source_id`、类型、完整标题、作者/机构、发布日期、版本/venue、URL、访问日期、archive URL、正文引用位置、支持的 claim_id、利益关系/独立性备注。

## 6. Demo Audit 模板

模板见 [`data/demo_audit_template_v1.csv`](../../data/demo_audit_template_v1.csv)，已为以下 8 个 demo 建立部分填充记录：

- D01 Table-to-Dishwasher
- D02 双玻璃杯搬运
- D03 袜子配对和卷袜
- D04 ACT-2 折衣
- D05 人为打断恢复
- D06 儿童接触
- D07 自主充电
- D08 抽屉和洗碗机操作

v1.1 的 demo 审核单位应是“一个可定位的原始视频或一次 trial”，而不是一个营销任务名称。若同一任务有多个镜头、住宅或尝试，应从 D01 派生 `D01-T001…` 逐条登记。

最低验收规则：

1. `source + timestamp` 必须能定位原始片段。
2. `attempt_count` 与 `success_rate` 不得从成功集锦反推。
3. `autonomy_claim` 必须同时登记远程协助、安全接管、人工重置与预摆。
4. `editing_or_cuts` 不得留空后写“连续自主”。倍速不等于剪辑，但必须记录。
5. `reset_rule` 必须定义 timeout、abort、硬件故障、物品跌落、人工触碰和重试是否计为新 attempt。
6. 儿童接触属于安全案例，不应由普通 demo 画面承担安全认证结论。

## 7. 工程审计

### 7.1 当前单文件构成

- 文件大小：`5,006,483 bytes`（约 4.77 MiB；`du -h` 显示 4.8M）。
- 行数：956；行数低估了复杂度，因为 base64 图片各占超长单行。
- 图片：7 张内容图 + 1 个 lightbox 空 `img`。7 张均以 data URI 嵌入；其中 1 PNG、5 WebP、1 JPEG。DOM 图片 data URI 字符约 4,793,648；hero 背景还在 CSS 中内嵌并在移动媒体规则中重复声明。
- 最大两张：Macro Data Refinement PNG data URI 约 3.10M 字符；手套近景 JPEG 约 1.29M 字符，合计占绝大多数体积。
- CSS：单个内联 `<style>`，约 302 行，包含 tokens、组件、响应式、dark theme 与 print。
- JavaScript：单个内联 `<script>`，约 58 行，直接初始化全部交互。
- 数据：claim、来源、术语、指标和图表数值全部硬编码在 HTML；没有 schema 或单一事实源。

### 7.2 性能风险

| 优先级 | 风险 | 影响 | 建议 |
|---|---|---|---|
| P1 | 全部图片 base64 内嵌并即时解析 | HTML 下载/读取完成前无法独立缓存；base64 体积膨胀；解码和 DOM 字符串内存峰值高 | 源码版改外部图片；dist 构建时才内联 |
| P1 | 最大 PNG 约 3.1M 字符 | 首屏之外资源占据大部分文件 | 重新编码 WebP/AVIF，并设置视觉质量预算 |
| P1 | 图片无 `width/height` | 解码前无法预留纵横比，存在布局跳动 | 构建时写入尺寸或 `aspect-ratio` |
| P1 | 无 `loading="lazy"`/`decoding="async"` | 非首屏图片也会尽早解码 | 对非 hero 图使用 lazy/async；hero 使用明确优先级 |
| P1 | CSS 中 hero data URI 重复 | 额外体积和维护分叉 | 用自定义属性或单资产引用；dist 去重 |
| P2 | 所有脚本一次性初始化 | 当前体量尚可，但任一早期异常会阻断后续功能 | 按 feature 初始化并做能力检测/空值保护 |

### 7.3 移动端风险

- `max-width:1120px` 后目录直接隐藏，没有替代目录/跳转菜单，长报告移动端失去导航。P1。
- 760px 下 hero 标题仍为 47px，极窄设备和大字号设置可能换行/溢出。P1。
- 表格通过横向滚动保底，基本可用；但没有滚动提示，打印时也没有表格专用布局。P2。
- 固定回顶按钮可能与移动浏览器底部 UI/安全区域重叠，未使用 `env(safe-area-inset-bottom)`。P2。

### 7.4 无障碍风险

已有优点：`lang=zh-CN`、语义 heading、图片 alt、目录 aria-label、按钮 aria-label、lightbox dialog/aria-modal、Escape 关闭。

待修复：

- P1：`.zoomable` 图片仅监听 click，不可通过键盘进入；应使用 button/link 包装或 `tabindex=0` + 键盘事件。
- P1：lightbox 没有初始焦点、焦点圈定、关闭后焦点返回和背景 inert。
- P1：术语搜索只有 placeholder，没有 `<label>`。
- P1：主题按钮不暴露 `aria-pressed`/当前主题；筛选按钮也不暴露选择状态。
- P1：没有显式 `:focus-visible`，浏览器默认焦点在复杂背景上的可见性未知。
- P1：smooth scroll、hover transform 与 transition 没有 `prefers-reduced-motion` 降级。
- P2：进度条是纯视觉状态；可保持装饰性，但不要让它承担唯一导航反馈。
- P2：颜色对比未做自动测量；dark theme、muted 文本、source tag 应跑 WCAG AA 检查。

### 7.5 打印风险

- P1：打印隐藏 filter/search 工具，却不清除已应用的 `.hidden`；用户在筛选或搜索后打印会得到不完整报告。
- P1：dark theme 的 CSS variables 没有在 `@media print` 重置，卡片/section 可能保留深色背景或低对比配色。
- P1：对整个 `.section` 使用 `break-inside: avoid`；长 section 无法放进单页，浏览器可能产生大空白或忽略规则。应只对卡片、图注、短表格行组避免断页。
- P1：hero 打印仍保留高 460px 和大背景图，浪费首张纸并增加墨量。
- P2：外链 URL 仅在参考列表可见；正文引用在纸面上依赖 O/P/T 编号体系，当前映射不直观。
- P2：表格和图形缺打印专用字号、重复表头、灰度对比与宽表降级。

## 8. 源码迁移方案（不在本轮实现）

建议把“可维护源码”和“可离线分发物”分开：

```text
src/
  sunday_robotics_research_report.html
assets/
  css/report.css
  js/report.js
  images/
    hero.webp
    macro-data-refinement.webp
    glove-overview.webp
    glove-closeup.webp
    glassware.webp
    act-1.webp
    act-2.webp
data/
  claim_registry_v1.csv
  source_registry_v1.csv
  demo_audit_template_v1.csv
  glossary.json
  report_metrics.json
scripts/
  validate-report.*
  build-single-file.*
dist/
  sunday_robotics_research_report.html
```

约束与构建策略：

1. `data/*` 是 claim/source/demo 的事实层；HTML 表格、Claim Matrix 和参考列表由同一数据生成，禁止三处手工复制数字。
2. `src` 使用外部 CSS/JS/图片，便于审阅、缓存和局部 diff。
3. 构建脚本生成 `dist` 单文件：压缩/转码图片、内联 CSS/JS/data URI、写入版本与内容哈希。
4. 保留根目录现有文件名作为发布兼容副本还是只指向 `dist`，在实施阶段明确；不应维护两份独立手写 HTML。
5. CI 校验：唯一 id、内部锚点、外链格式、source_id 外键、claim 必填字段、数字一致性、无未引用重要 claim、HTML 校验、无障碍 smoke test、打印截图、移动端截图、dist 可离线打开。
6. 预算建议：源码图片总量 < 2 MiB，关键首屏 < 500 KiB；dist 单文件设明确上限并记录每次变化。

## 9. v1.1 分阶段实施计划

### Phase 0 — 冻结证据口径（P0）

- 审阅并确认三个 registry 的 schema、枚举和 source_id。
- 对 51 条 claim 逐条决定 retain/rewrite/demote/remove。
- 先修 9 个指定数字及 `90%`、`4.72/5`、`46.9%` 的定义。
- 为所有 company self-report 加主语；为所有 inference 加推断措辞。
- 产出验收物：零裸写 `zero robot data`/`zero adaptation cost`；全部重要数字有分母、范围和来源。

### Phase 1 — 来源与 demo 取证（P0/P1）

- 补 17 条来源作者、日期、版本、archive 和 claim-level locator。
- 修复 T1/T2、P7/P8 标签；给 Claim Matrix 直接引用。
- 逐 demo 获取原始视频、时间戳和 trial 信息；ACT-2 建 785 条 manifest（若公开材料允许）。
- 产出验收物：每条 retained claim 至少一个 source_id；每个 demo 至少有 source/timestamp/editing/intervention/reset 状态，不确定项显式为 unknown。

### Phase 2 — Technical Deep Dive（P1）

- 围绕数据转换、力/触觉、柔性物体、恢复/记忆、安全控制和泛化成本建立技术问题表。
- 优先要求可证伪的信息：传感器频率、控制频率、数据 taxonomy、模型/配置哈希、trial protocol、失败分类、地图/标定成本、介入率。
- 把论文背景与 Sunday 实现分成两栏，避免方法论文替公司系统背书。

### Phase 3 — 内容重构（P1）

- `summary` 只保留 5–7 个结论及限定。
- `act` 作为数字和协议主章节；`claims` 从 registry 渲染简表，不重复长段。
- 合并 `transparent + force` 的玻璃部分，合并 `deformable + force` 的衣物部分；`system/glove/developer` 用一张事实—未知映射表串联。
- 将 `macro` 降为附录或短侧栏。

### Phase 4 — 工程迁移与质量门禁（P1/P2）

- 按第 8 节拆分源码，添加构建与验证脚本，生成 `dist` 单文件。
- 完成性能、移动端、键盘、screen-reader、reduced-motion、dark/light print QA。
- 产出可复现的构建命令、文件体积报告、桌面/移动/打印截图与离线 smoke test。

### Phase 5 — 发布前 evidence sign-off（P0）

- 由研究与工程分别签核：claim 外键、数字一致性、引用可达性、demo 边界、无障碍和打印。
- 在页面显著位置加入“数据截止日期、最后核验日期、公司自评/独立验证状态、变更日志”。
- v1.1 发布后把未解决 unknowns 转为下一轮研究 backlog，不在正文中用确定语气填补。

## 10. 本轮产物与完整性说明

- `docs/reports/2026-07-18_sunday_report_v1_audit.md`：本综合审计。
- `data/claim_registry_v1.csv`：51 条 claim 登记。
- `data/source_registry_v1.csv`：17 条来源登记。
- `data/demo_audit_template_v1.csv`：8 个 demo 审计记录。

本报告的“已核验”表示核对了当前 HTML 与可访问的公开页面，不表示完成独立机器人复现、公司内部日志审计或安全认证审查。
