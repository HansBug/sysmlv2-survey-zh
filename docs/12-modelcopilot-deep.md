# 12 ModelCopilot / WSE Laboratory 深度档案

## 本章简介

本章是 [03-北航专项 §2.7](03-beihang-investigation.md#27-wse-laboratory--modelcopilot-平台深读) 的全面展开版，目标只有一个：**把"ModelCopilot 这群人到底已经做到什么程度、定位是什么、后续会做什么"盘清楚**。涵盖：

- 实验室基本面 + 团队画像 + GitHub 组织时间线
- 8 个公开仓库**逐一深读**（含每个仓库的代码组织、关键数据、与论文的对应关系、是否值得跳转）
- 核心论文 arXiv 2602.21641（PSUM-SysMLv2）解剖：作者、7 案例细节、stereotype 完整集、未来工作承诺
- 三篇配套论文 + 一篇**同名同题但与本团队无关**的英国 Loughborough 研究路线图（重要澄清）
- ModelCopilot 平台自报技术规格（KerML 95.6% / SysML v2 87.9%）
- 微信公众号 ModelCopilot 真实状况（"几乎不存在"的对外渠道）
- NSFC / 项目资助的可见性 gap
- 与 OMG Pilot / 华望 M-Design / Cameo / Sensmetry / FBK / Almeida UFO / Loughborough 的战略定位对比
- 6 个月 / 1 年 / 3 年前瞻轨迹

> 核心三问的速读答案见 §1 决策卡。

## 1 决策卡（直面三个核心问题）

### Q1 — "ModelCopilot 这群人到底已经把事情做到什么程度了？"

> **中后期 alpha 实验室原型 + 单点突破已发表论文 / 已开源实证 artifact，但平台代码至今未公开**。

| 维度 | 已做到 | 未做到 |
|---|---|---|
| 平台 | KerML 95.6%（239/250 元素）+ SysML v2 87.9%（452/514 元素）；裸 IP demo `http://116.204.36.247` 在线 | **平台源代码至今未开源**（`WSE-ModelCopilot` 仓库实际不存在，仅是网站文案占位） |
| 标准 | **全球首个**把 OMG PSUM 落地到 SysML v2 的 profile + 7 工业域案例 + arXiv 2602.21641 论文（2026-02）已开源 | PSUM × ModelCopilot 平台的集成尚未释放 |
| 量子线 | 完整三连击：SLR (76 papers) + 实证元分析 (78 studies) + 工具型 IsingBench（4 求解器 + 3 经典 SE 数据集）；含 arXiv 2506.16878 + arXiv 2510.27113 | IsingBench 论文未投稿 |
| ADS 线 | LiveTCM-demo 是 4★ 的最热仓；LiveTCM 主仓沉淀了 **165+ LLM 实验日志**（每个 30–175 KB） | LiveTCM 实验论文未发表 |
| 综述线 | LLM4MDE artifact (254 papers, 2021–2025) 已上 GitHub | 综述论文未上 arXiv |
| 公众号 | ModelCopilot 公众号在 modelcopilot.org footer 留二维码 | 搜狗 + Bing + 知乎 + 百度均**无可检索文章**——基本上是"试运行 / 未启动"状态 |

**整体处在"成果集中爆发、论文撰写阶段、对外通道还没打开"的临界期**。

### Q2 — "定位是什么？"

> **学术参考实现 + OMG 标准支撑 + AI4MBSE 元平台**——三件事并行：

1. **OMG 标准的 BUAA 参考实现**：与 [Pilot Implementation](04-parsing-ide-infrastructure.md#1-omg-官方参考实现pilot-implementation) 平行，专注于 PSUM、Uncertainty、AI Co-pilot 这三个 Pilot 不覆盖的方向。
2. **不确定性 × 量子 × LLM 三条独立 SE 研究方向的统一汇聚平台**——三个方向各有独立的数据 / 工具仓库（PSUM-SysMLv2、IsingBench、LLM4MDE），最终都收敛到 ModelCopilot 这一"活模型"愿景。
3. **不与商业工具（Cameo / 华望 M-Design / Sensmetry SysIDE）正面竞争**：扮演他们的"上游标准实验台"和"AI 增强元能力供应方"——这是岳涛 / 张曼这类资深 OMG 标准贡献者最自然的卡位。

**重要澄清**：与英国 [Loughborough 大学 *MBSE Co-Pilot: A Research Roadmap*][^mbse-copilot-loughborough-12]（INCOSE *Systems Engineering* 2026, DOI 10.1002/sys.70011）**同名同题但完全无关**——后者是 vision-only 路线图论文，没有平台没有代码；BUAA WSE-Lab 走的是 vision + 平台 + profile + 案例 + 多个工具的"做实型"路径。

### Q3 — "后续比较会干什么？"

按可能性从高到低：

1. **2026 H2**：ModelCopilot 平台 + WSE-ModelCopilot 仓库正式开源（与 PSUM 集成版同步），最迟在 MODELS 2026 / OMG TC 季的 demo 时段。
2. **2026 H2 → 2027 H1**：4 篇配套论文（平台架构 / PSUM 集成 / LiveTCM 完整版 / IsingBench Tool Track）密集投稿。
3. **2027**：在 OMG 渠道推动 PSUM 子规范的中国主导版本；启动公众号 + 中文综述以打通国内学术与工程界的认知。
4. **2027–2029**：把 "AI4MBSE" 作为统一议程，把 PSUM 不确定性 + Quantum-SE benchmark + LLM4MDE 元能力收敛进 ModelCopilot；与航空航天部委 / 商飞 / 华望等形成项目级合作的概率显著高于平均。
5. **不太会做的事**：商业化 SaaS、与 Cameo 正面竞争、以工具厂商身份进入企业 MBSE 市场——团队结构与 PI 履历不支持这一路径。

## 2 实验室档案

### 2.1 基本面

| 维度 | 事实 |
|---|---|
| 全称 | **WSE Laboratory**（Software Engineering, Elevated；网站标题"WSELab"） |
| 中文 | 北京航空航天大学计算机学院 软件工程实验室（暂无正式中文名） |
| PI | **岳涛 Tao Yue**（教授 / 博导，BUAA 主页 G917，曾任 Simula Chief Research Scientist）[^yue-buaa] |
| Co-PI | **张曼 Man Zhang**（副教授 / 硕导，BUAA 主页 G616，2018 Oslo / Simula PhD，挪威 Kristiania 博后）[^zhang-buaa] |
| 通讯 | yuetao@buaa.edu.cn / manzhang@buaa.edu.cn |
| 地址 | 北京市海淀区学院路 37 号 100191 |
| 团队规模 | 2 教师 + 2 PhD + 11 MEng + 3 BEng + 1 visiting = **19 人** |
| 五大研究方向（自报） | Uncertainty-aware SE、Quantum SE、Model-based SE、Intelligent Software Testing、Optimization in SE |
| 三个旗舰平台 | **ModelCopilot**（MBSE 内核）、**LiveTCM**（自动驾驶自适应测试）、**IsingBench**（量子优化测试） |

### 2.2 GitHub 组织时间线

| 仓库 | 创建时间 | 最近 push | 大小 | Star | 类型 |
|---|---|---|---|---|---|
| WSE-Lab.github.io | 2024-10-17 | 2026-03-19 | 333 MB（含 demo 视频） | 0 | 站点源码 |
| LiveTCM | 2025-03-25 | 2025-03-25 | 135 MB（含 165+ 实验日志） | 0 | LLM 实验日志档案 |
| liveTCM-demo | 2025-03-26 | 2025-06-19 | 343 MB（含 89 MB demo.mp4） | **4** | 自动驾驶测试工具 |
| QuantumOpt4SE | 2025-06-14 | 2026-04-12 | 1.1 MB | 0 | SLR 数据集 |
| QuantumOpt4SE-EmpiricalStudies | 2025-10-09 | 2025-10-29 | 41 KB | 0 | 实证元分析 |
| PSUM-SysMLv2 | 2026-02-24 | 2026-02-27 | 385 KB | **1** | 论文配套 profile + 案例 |
| IsingBench | 2026-03-12 | 2026-04-13 | 83 MB（含 89 MB demo.mp4） | 0 | 量子优化基准 |
| LLM4MDE | 2026-04-01 | 2026-04-01 | 269 KB | 0 | SLR 数据集 |
| ~~WSE-ModelCopilot~~ | **不存在**（HTTP 404，gh api 三次确认） | — | — | — | 仅在 [model-copilot.html][^modelcopilot-platform] 上保留"open source in progress"占位 |

**关键观察**：

- 组织 2024-09-04 创建至今约 20 个月，**只有 8 个仓库**，集中在 2025 H1 与 **2026 Q1 两个高峰**；从 2026-02 起开始把"成果"系统化推上 GitHub（PSUM-SysMLv2、IsingBench、LLM4MDE 三个核心 artifact 都在 2026 Q1 集中放出），可以判断他们正在进入"集中收获、对外可见"的阶段。
- Star/Fork 数极低（org 总和 < 10），社区运营尚未启动。
- 4 个 star 的 liveTCM-demo 是当前最热仓——这意味着自动驾驶测试方向已经有零星外部关注。

## 3 ModelCopilot 平台技术规格

来自 [model-copilot.html][^modelcopilot-platform] 抓取的关键自报数据（**截至 2026-05**）：

| 指标 | 数据 |
|---|---|
| KerML 具象语法元素 | **239 / 250 = 95.6%** |
| SysML v2 具象语法元素 | **452 / 514 = 87.9%** |
| Live demo | <http://116.204.36.247>（公网 IPv4，未走域名） |
| 源代码 | "Github (open source in progress)"——至 2026-05 仍未释放 |
| 平台口号 | "Where Models Craft Meaning" / "Making models 'come alive'" |
| 三大能力 | Model Handling / Application / Assessment |
| 四大设计理念 | Sustained Relevance / Seamless Integration / Cross-Domain Synergy / Embracing Uncertainty |

值得注意的几个工程化未完成信号：

1. 95.6% / 87.9% 这种自报数字目前**没有第三方核验**，模板里还残留着 "Quantum-Classical Hybrid 92.8%" 这种来自原始模板的展示数据，说明站点工程化未完成。
2. demo 用裸 IP 暴露而非走 modelcopilot.org 子域名——ICP / 备案管控之下的常见妥协。
3. 站点页脚 © 2025，page header © 文案为 "WSE Laboratory"，**没有任何商业化 / SaaS pricing 痕迹**——纯学术路线。
4. 首页 HTML 里有大段被注释掉的 News Banner / Publications 卡片——说明站点是从一个商业模板改造而来，论文 / 新闻区目前是"占位—待填"状态。

## 4 GitHub 8 仓库逐项深读

### 4.1 PSUM-SysMLv2 — OMG PSUM 在 SysML v2 上的首个开源 profile

[github.com/WSE-Lab/PSUM-SysMLv2][^psum-repo]，385 KB，1★，2026-02 创建，**README 仅 11 行**（极简风格，权重压在论文上）。

仓库结构：

| 目录 | 内容 | 体量 |
|---|---|---|
| `profile/PSUM-SysMLv2.profile.uml` | 配套论文的 MOF/XMI Profile 定义 | 18 KB |
| `profile/KerML_only_xmi.uml` | 取自 OMG SysML v2 Pilot Implementation | 759 KB |
| `profile/SysML_only_xmi.uml` | 同上 | 875 KB |
| `case-studies/` | **7 个完整案例**（每个含 `origin/` 原模型 + 扩展后 `.sysml`） | — |

**7 个案例**（与 arXiv 2602.21641 呼应，从仓库目录直接抓取）：

| # | 案例（仓库目录名） | 域 | 扩展后 .sysml 体量 |
|---|---|---|---|
| 1 | Adaptive Cruise Control system (ACC) | 车辆自适应巡航 CPS | 19.8 KB |
| 2 | Arrowhead Framework system (AF) | 工业 IoT / SOA 框架（来自挪威 Arrowhead 项目）| 24.7 KB |
| 3 | Drone System (DS) | 无人机控制（含多状态机 + 不确定迁移）| 31.7 KB |
| 4 | Interaction Sequencing system (IS) | 发布-订阅交互系统 | 12 KB |
| 5 | Mining Frigate system (MF) | 矿山舰 / 海事工程 | **83 KB**（最大模型） |
| 6 | Vehicle Fuel Economy Analysis system (VFEA) | 燃油经济性分析 | 9.7 KB |
| 7 | Vehicle Model | OMG SysML v2 Spec Annex A SimpleVehicleModel 扩展 | 82.9 KB |

**PSUM 实际落地的 stereotype 完整集**（直接从 profile XMI 提取，与论文 Table 1 对应）：

| Package | Stereotype |
|---|---|
| **Belief** | `«BeliefStatement»` · `«IndeterminacySource»` · `«IndeterminacySpecification»` |
| **Uncertainty** | `«Uncertainty»` · `«UncertaintyTopic»` · `«Effect»` · `Risk`（KerML 子类） |
| **Measurement** | `Accuracy` · `Sensitivity` · `MeasurementError` · `Precision` · `Degree`（KerML 子类） |

实际 `.sysml` 用法示例（来自 Drone System 案例）：

```sysml
«Uncertainty<ocr, epi, subj>» accept SigSwitchOn then standBy {
    «IndeterminacySpecification» ref ::> droneControlUnit.droneControlUnitOperational;
    measurement { m_degree = "TODO"; }
}
```

`<ocr, epi, subj>` 三元组分别对应 occurrence / epistemic / subjective——把 PSUM 的本体维度直接编码进 SysML v2 transition 关键字上方，是相当工整的工程实现。

**跳转判断**：**SysML v2 不确定性方向必看**。是**全球第一份**把 OMG PSUM 落地到 SysML v2 的开源 profile，引用价值高。

### 4.2 LLM4MDE — 254 篇 LLM × MDE 的 SLR

[github.com/WSE-Lab/LLM4MDE][^llm4mde-repo]，269 KB，0★，2026-04 创建。

| 维度 | 数据（实测 CSV 抓取） |
|---|---|
| 初筛 | **2,066** 篇（去重后） |
| 入选 | **254** 篇（仓库写 228，CSV 实际 254 行） |
| 时间分布 | 2021×1 · 2022×5 · 2023×25 · **2024×84 · 2025×113** |
| 数据维度 | 27 列：8 个 RQ × 多子维度（年份、venue、文章类型、MDE 任务、SE 活动、建模语言、应用域、LLM 家族、Prompt 工程、增强技术、集成、交互模式、有效性指标、时间/资源/货币 cost、baseline、源码链接、prompt 链接） |
| Top venues | MODELS Companion(12) / ER(9) / MODELS(9) / RE Workshops(7) / SoSyM(6) |
| Top 文章类型 | Proposed method(153) / Benchmarking(56) / Vision(15) / Tool demo(4) |
| Top 建模语言 | UML(66) / DSL(56) / BPMN(34) / SysML v1(8) / Ecore(6) |
| Top SE 活动 | SE Models & Methods(81) / Requirements(30) / Construction(10) / **Testing(8)** |

**论文未投稿 / 未上 arXiv**——此为已开源的 artifact，配套 manuscript 应在 2026 H2 投出（参 §10.1 预测）。

**跳转判断**：**做 LLM × MBSE / 形式化工程的人必看**——228+ 篇直接拿来当 Related Work 引文池；不必读全文，核心数字 254/2066 + 27 列 RQ 已浓缩进上表。

### 4.3 QuantumOpt4SE — 76 篇量子优化 × SE 的 SLR（已上 arXiv）

[github.com/WSE-Lab/QuantumOpt4SE][^qopt-repo]，1.1 MB，0★，2025-06 创建，2026-04-12 最后更新。

| 维度 | 数据 |
|---|---|
| 作者 | Man Zhang, Yuechen Li, **Tao Yue**, **Kai-Yuan Cai 蔡开元** |
| 初筛 → 入选 | 2,083 → **76**（2026-04-12 仓库更新到 76） |
| 论文 | arXiv 2506.16878 *Quantum Optimization for Software Engineering: A Survey*（2025-06-20）[^arxiv-2506] |
| 年份 | 2015–2025，2022–2024 三年 46 篇是主力 |
| Top venues | Cluster Computing / Applied Soft Computing / Sensors 各 3，TOSEM / TSE / QCE / QP4SE 各 2 |
| Top SE 活动 | SE operations(37) / Testing(16) / Quality(9) / Security(5) |
| 量子机制 | Circuit-based(25) / Physics-based(19) / N/A(28) |

**蔡开元** 是 BUAA 自动化与可靠性学科领军人物之一——把他列入作者意味着 WSE-Lab 已与本地化 BUAA 资源做了绑定（不只是岳涛 + 张曼两人体系）。

**跳转判断**：**做量子软件工程或量子启发优化的人必看**——这是该方向最系统的中国学者主导综述。

### 4.4 QuantumOpt4SE-EmpiricalStudies — 78 实证子集元分析

[github.com/WSE-Lab/QuantumOpt4SE-EmpiricalStudies][^qopt-emp-repo]，41 KB，0★，2025-10 创建。

`extracted_data.csv` 含 **78 行 × 25 列**实证维度：`#Repeats / #Shots / Quantum noise / #Qubits / 终止条件 / 问题复杂度 / 有效性指标 / 量子专属指标 / 案例开源? / Baseline 类型 / 工具开源?`。

论文：arXiv 2510.27113 *Empirical Studies on Quantum Optimization for SE: A Systematic Analysis*（2025-10-31）[^arxiv-2510]。

**结论**：现有量子 SE 实证报告"重复次数 / shots / 噪声处理普遍缺失，缺统一指标"——典型的"为社区立 reporting guideline"立场论文。

**跳转判断**：仅当深做 QSE 实证研究 / reporting guideline 时引用；否则掠过。

### 4.5 IsingBench — 测试套件优化的统一 Ising benchmark

[github.com/WSE-Lab/IsingBench][^isingbench-repo]，83 MB（含 89 MB demo.mp4），0★，2026-03 创建。

README 是几个仓库里最详细的（约 7.5 KB）。**核心抽象**：把 SE 的两个 NP-hard 经典问题（**TCS 测试用例选择 / TCM 测试套件最小化**）统一编码成 Ising Hamiltonian：

```
E(s) = -∑hᵢsᵢ - ½∑Jᵢⱼsᵢsⱼ
```

| 子模块 | 内容 |
|---|---|
| Problems (`ising_bench/problems/`) | `WAOr` 比例加权 / `WAOd` 偏离加权 / `WAOr-Budget` 带预算 |
| Solvers (`ising_bench/solvers/`) | **CIM**（Coherent Ising Machine GAPP 模拟）/ **BruteForce** / **GA**（遗传） / **SA**（模拟退火） + 各自 base 抽象 |
| Benchmarks | `paintcontrol`（**90 cases**） / `gsdtsr`（**5,555 cases**） / `iofrol`（**1,941 cases**）——这三套是 **CI 回归测试领域的经典数据集**（Spieker 等 2017） |
| Baselines | 内置 `BootQA` / `EIDQ` / `Div-QAOA` / `IGDec-QAOA` / `RandomSearch` 的预跑结果（每 case 一个 JSON，方便复现对比） |
| 工程化 | YAML 驱动 (`example1.yaml` / `example2.yaml` / `example3.yaml`) + CLI (`ising_bench.py`) + register_problem / register_solver 装饰器扩展 |
| Demo | 视频 89 MB 在仓库；在线 demo 嵌入 `modelcopilot.org/QSE` |

**这是目前公开仓库里最完整、可外部复用的工程产物**，对国内还在做"量子启发式 + SE"的小团队是即开即用的对照基准。

**跳转判断**：**量子启发优化 + 测试方向必看**。Tool Track 论文应在 ASE 2026 / ISSTA 2026 投出（参 §10.1）。

### 4.6 liveTCM-demo — 自动驾驶自适应模型驱动测试 demo

[github.com/WSE-Lab/liveTCM-demo][^livetcm-demo-repo]，343 MB（含 89 MB demo.mp4），**4★（最热仓）**，2025-03 创建。

完整可跑的 demo：

| 组件 | 内容 |
|---|---|
| 前端 | Vue + build 脚本 + index.html + src + style |
| 后端 | `carla_server/` Python（CARLA 0.9.15 + DeepCollision MORL baseline + Interfuser 端到端模型） |
| 业务逻辑 | `carla_logic.py` 共 52 KB |
| CARLA 场景示例 | 见 `figures/Town07_WetCloudyMorning.gif` 等典型 CARLA Town/天气组合 |

**8 大功能**（README 直接列出）：

1. **Model Explorer**——树形 model element 浏览器
2. **Test Setup**——sequential 测试步骤设置视图
3. **TestCaseSpecification**——自然语言 → 模型元素的 runtime 转换
4. **Auto-Completion** for imported APIs
5. **Alternative Flows**——oracle / specific / global 三类验证流
6. **Dynamic Generation**——可扩展引擎自动 generate / complete 测试规约
7. **Record & Replay** 测试场景
8. **Switch Mode + Pause/Stop** 执行控制

来源是 [Shi 等 MoDELS 2021][^shi-2021-models-12] *Restricted Natural Language and Model-based Adaptive Test Generation for Autonomous Driving*——相当于**把 6 年前的 RTCM 思路 + DeepCollision MORL + 现代 LLM 整合进了一个统一的 ADS 自适应测试框架**。

**跳转判断**：**自动驾驶 / ADS 测试方向必看**——含 CARLA 集成的端到端 demo，可作 baseline。

### 4.7 LiveTCM — 165+ LLM 实验日志档案（**重要纠正**：不是空仓）

[github.com/WSE-Lab/LiveTCM][^livetcm-repo]，135 MB，0★，2025-03 创建。

**第一轮调研误判为"主仓占位"**——实际仓库结构：

```
LiveTCM/
├── README.md                # 8 大功能说明（与 liveTCM-demo 重复）
├── consoleLogs/             # 实验日志档案
│   ├── example_abort.log    # 失败示例
│   ├── example_success.log  # 成功示例
│   └── experiment/          # 165+ 个 LLM 实验日志（每个 30–175 KB）
└── figures/                 # 多个 .gif 演示截图（AutoCompletion / dynamic / oracleflow / TestCaseSpecification 等）
```

**核心价值**：`consoleLogs/experiment/` 沉淀了 **165+ 个完整的"LLM 驱动自适应测试规约生成"实验日志**——这是 2025-03 一次性提交的系统性实验数据集，说明他们已经做完了一轮端到端实证。论文应在 2026 H2 投 ICSE / FSE / ISSTA 2027（参 §10.1）。

**跳转判断**：仅当复现 / 引用 LiveTCM 实验数据时跳转；否则配合 liveTCM-demo 一起读即可。

### 4.8 WSE-Lab.github.io — 三页静态站

[github.com/WSE-Lab/WSE-Lab.github.io][^wse-site-repo]，333 MB（含 demo 视频），0★。

| 页面 | 内容 |
|---|---|
| `index.html` | 实验室总览 |
| `model-copilot.html` | 平台子页（KerML 95.6% + SysML v2 87.9% 自报） |
| `QSE.html` | IsingBench 子页 |
| `ADS.html` | LiveTCM 子页 |

外加 19 张师生头像、4 段 demo 视频（IsingBench 89 MB / 主页 demo 105 MB / liveTCM 97 MB）。

**重要观察**：首页 HTML 内有大段被注释掉的 **News Banner / Publications** 卡片——说明站点是从一个商业模板改造而来，论文 / 新闻区目前是"占位—待填"状态。**没有真实 news 流，意味着信息更新仍倚靠 GitHub 与论文，公众号几乎无内容**。

### 4.9 WSE-ModelCopilot — **不存在**

通过 `gh api` 三次确认（`WSE-ModelCopilot` / `ModelCopilot` / `modelcopilot` 三种命名都返回 **HTTP 404**），**ModelCopilot 平台代码至今没有公开**，仅在 `model-copilot.html` 上保留 "Github (open source in progress)" 的占位文案。

第一轮调研误判为"准备开源"——实际是"网站文案占位，仓库未创建"。**这是评估实验室"代码开放度"时必须明确的事实**：他们至今尚未把核心平台开源出来。

## 5 核心论文 arXiv 2602.21641 解剖

[arXiv:2602.21641][^arxiv-2602-12] *Uncertainty Modeling for SysML v2*，2026-02-25 由张曼 / 李云阳 / 岳涛（通讯）共同提交。

| 维度 | 事实 |
|---|---|
| 作者 | Man Zhang¹ · Yunyang Li¹ · **Tao Yue\*¹**（通讯，但一作让给张曼，是有意提升张曼显示度的安排） |
| 提交日期 | 2026-02-25（v1） |
| GitHub artifact | <https://github.com/WSE-Lab/PSUM-SysMLv2>（论文 footnote 直接给出） |
| 平台 footnote | <https://www.modelcopilot.org/model-copilot.html> |
| Future work 关键句 | *"we plan to integrate PSUM-SysMLv2 into our ModelCopilot platform and develop additional automated solutions to assist engineers in identifying, constructing, and evolving uncertainties within models."* |

**这是 ModelCopilot 平台的官方"未来工作背书"**——已经把 PSUM 集成进 ModelCopilot 列入了 roadmap，但仓库与平台尚未释放（参 §1 Q1 进度评估）。

PSUM-SysMLv2 stereotype 完整集见 §4.1 表格（与 profile XMI 完全一致）。

7 个案例验证（domain / size 见 §4.1 表格）。**Acknowledgement 与具体 NSFC 编号在 arXiv html 渲染中未暴露**——这是一个**待补 gap**（需直接下载 PDF 翻第 1–2 页脚注）。

## 6 配套论文 + 同名误识

### 6.1 WSE-Lab 自家近两年 SysML × QSE × LLM 论文

| 论文 | 年 | venue | arXiv |
|---|---|---|---|
| Uncertainty Modeling for SysML v2 | 2026 | preprint | [2602.21641](https://arxiv.org/abs/2602.21641) |
| Empirical Studies on Quantum Optimization for SE: A Systematic Analysis | 2025-10 | preprint | [2510.27113](https://arxiv.org/abs/2510.27113) |
| Quantum Optimization for SE: A Survey | 2025-06 | preprint | [2506.16878](https://arxiv.org/abs/2506.16878) |
| Quantum Software Engineering: Roadmap and Challenges Ahead | 2025 | TOSEM | [2404.06825](https://arxiv.org/abs/2404.06825) |
| Seeding and Mocking in White-Box Fuzzing Enterprise RPC APIs | 2024 | ASE 2024 Industry | — |
| Safety Behaviour Abstraction and Model Evolution in Autonomous Driving | 2024 | SoSyM (to appear) | — |
| Tool report: EvoMaster | 2024 | EMSE | — |
| Advanced White-Box Heuristics for Search-Based Fuzzing of REST APIs | 2024 | TOSEM | — |
| EpiTESTER (Lu/Ali/Yue) | 2024 | TSE | — |
| Pretrain, prompt, and transfer (Xu/Yue/Ali/Arratibel) | 2024 | TSE | — |

**LLM4MDE 与 LiveTCM 的论文均未在公开渠道命中**——artifact 已开源但 manuscript 未上 arXiv，可能在投稿匿名状态。

### 6.2 同名误识澄清（**重要**）

英文搜索 "MBSE Co-Pilot Research Roadmap" 会命中：

[**INCOSE *Systems Engineering* 29(1):20–33 (2026)** *MBSE Co-Pilot: A Research Roadmap*][^mbse-copilot-loughborough-12]

- DOI 10.1002/sys.70011
- 作者：Wenheng Zhang, Callum Cockburn, Michael Henshaw, Peter Douglas, Paul Palmer, Joshua Olivier-Myall, Siyuan Ji
- 机构：英国 **Loughborough University**
- 内容：用 NLP / ML / CV 等 AI 技术增强 MBSE 模型构建、管理与理解的**研究路线图**

**与 BUAA WSE-Lab 的 ModelCopilot 没有任何作者 / 引用 / 合作关系**——只是同一 "Co-Pilot" 隐喻。这是英语圈和中国团队在 2024-2026 间**碰巧同名同题**的两条独立研究线。

| 项 | BUAA ModelCopilot | Loughborough MBSE Co-Pilot |
|---|---|---|
| 类型 | 平台 + profile + 工具 + 案例 + 论文 | vision-only roadmap 论文 |
| 平台代码 | 未开源（仅有自报指标） | 无 |
| 论文 | arXiv 2602.21641（已发） | INCOSE Systems Engineering 2026 |
| 团队 | 19 人 | 7 人 |
| 落点 | OMG PSUM 标准 + AI4MBSE 三方向收敛 | AI × MBSE 路线图陈述 |

写综述时**务必分开引述**，不要混为一谈。

## 7 微信公众号 ModelCopilot — 一个"几乎不存在"的对外渠道

| 检索路径 | 结果 |
|---|---|
| 搜狗微信文章搜索 `query=ModelCopilot` | 命中的全部是泛 Copilot（GitHub Copilot / 容犀 Copilot / 出门问问 CoPilot）等无关公众号 |
| 搜狗微信文章 `query=ModelCopilot 岳涛` | **0 命中**（"呀！没有找到相关的微信公众号文章"） |
| 搜狗公众号目录 `type=1 query=ModelCopilot` | "暂无与'ModelCopilot'相关的官方认证订阅号" |
| Bing `site:mp.weixin.qq.com ModelCopilot` | 0 有效命中 |
| 百度 / 知乎 中文站点 "ModelCopilot 公众号" | 0 命中 |

**结论**：截至 2026-05-05，ModelCopilot 公众号**无法通过任何公开搜索引擎独立验证有可读内容**。该公众号要么是未认证私号 / 已注销 / 设置不可被搜狗索引，也可能是仅扫码可关注的"试运行号"。对外信息辐射主要还是通过 modelcopilot.org 与 GitHub。**微信生态对这个团队而言目前不是一个有效渠道**。

> **第一轮调研留下的"≥ 1 篇推文"提法**已无法在 2026-05 时点的搜狗 / Bing 搜索复现——可能是搜狗索引在过去几个月内变化或者文章已被删除。这是档案最大的 **blind spot**，建议直接通过实验室邮箱或在 BUAA 内部询问其原始账号 / 二维码（modelcopilot.org footer 提供 `wechat_qr_en.png`）。

## 8 NSFC / 北航资助 — 公开可见性 gap

| 检索 | 结果 |
|---|---|
| NSFC 大数据知识管理服务门户（kd.nsfc.cn）"岳涛" | 门户对未登录态返回空页；具体项目号需登录 |
| 北航主页 / scse.buaa.edu.cn 教师页 | 仅披露联系方式，未公开基金号 |
| LetPub NSFC 数据库（2026 年索引） | 未通过 web search 暴露具体编号 |

**Gap**：本次外部检索**没有命中任何具体 NSFC 项目编号 / 国家重点研发计划编号**与 Yue / Zhang 一一对应。鉴于岳涛属于 BUAA 计算机学院教授 + 博导级别（170+ 论文），有 NSFC 面上 / 重点项目几乎是必然的，但需要直接登录 nsfc.cn 个人服务系统或翻 BUAA 科研院年报才能补全。

可确认的**Simula 时期项目**（基金体现于论文致谢）：

- **U-Test** EU 项目 — 不确定性 CPS 测试（2015–2018）
- **Zen-Configurator (No. 240024)** — 挪威研究理事会
- **AIT4CR** — Cancer Registry of Norway 测试基础设施
- **Co-tester / IND-LIFT / Tugendwald / IM4QN** — 挪威工业资助
- **Simula × Cisco Norway 9 年战略合作**

## 9 战略定位对比

与同业的卡位关系（详 §1 Q2）：

| 工具 / 项目 | 类型 | 核心标的 | 与 ModelCopilot 关系 |
|---|---|---|---|
| **OMG SysML v2 Pilot Implementation** | 标准参考实现（开源） | 语法 + 语义 reference | ModelCopilot 95.6% / 87.9% 是基于 Pilot 的并行实现；目标不是替代 Pilot，而是补 PSUM / Uncertainty / AI 协同那一层 |
| **杭州华望 M-Design v2** | 商业（中国） | 工程级建模、求解、对接 | 工程化方向更接近 Cameo；ModelCopilot 走"模型即活物"AI 协作路线，不在同一象限 |
| **Cameo / 3DEXPERIENCE No Magic** | 商业（达索 / 国际） | 全功能企业 MBSE | ModelCopilot 是学术演示 + AI 元能力，无意硬碰 Cameo |
| **Sensmetry SysIDE** | 闭源商业（立陶宛 + 北欧） | 开发者体验 + 形式化 | 直接竞争形式化辅助方向，但 SysIDE 不做不确定性 |
| **FBK SAWS²** | 形式验证学术 | 时序 / 模型检查 | 与 ModelCopilot 的 PSUM 路线**互补**：前者形式化验证，后者本体级语义建模 |
| **Almeida UFO / OntoUML** | 学术（巴西） | 本体 / 语义诘问 | 偏概念分析，ModelCopilot 偏工程化平台 |
| **Loughborough MBSE Co-Pilot**（Wenheng Zhang 2026） | 学术 vision paper | INCOSE *Systems Engineering* AI roadmap | **同名同题、不同团队**——理念互参，未来可能合作或竞争 |

**ModelCopilot 的独特卡位**——三句话：

1. **"PSUM × SysML v2 × AI 协作"** 三元组目前**全球独家**——OMG PSUM 2024 才正式发布，把它作为一等公民编码进 SysML v2 profile 并配 7 个工业域案例，2026-02 是首发。
2. **不与 Cameo / 华望工具链正面碰撞**：定位是"AI 协作元能力 + 不确定性建模 + Quantum-SE benchmark" 的**学术研究平台 + 标准制定支撑**，不做企业级 IDE 替代。
3. **借 OMG 标准入口实现影响力放大**：岳涛在 OMG SysML v2 / PSUM 都是核心贡献者，ModelCopilot 实际上扮演 **OMG 标准的 BUAA 参考实现**角色，与 Pilot Implementation 形成"双参考"格局。

## 10 前瞻轨迹预测

### 10.1 未来 6 个月（2026-05 → 2026-11）

| 信号 | 预测 |
|---|---|
| PSUM-SysMLv2 论文 footnote "plan to integrate" + 平台 87.9% 完成度 | **PSUM × ModelCopilot 集成版会在 2026 H2 上线**（最可能于 MODELS 2026 / OMG TC 2026-Sep 期间发布 demo），同时 GitHub `WSE-ModelCopilot` 仓库正式创建并开放 |
| LLM4MDE artifact 2026-04 才推 GitHub | **LLM4MDE survey 论文会在 2026 H2 投 SoSyM / TOSEM**（Yue/Zhang 在 SoSyM 是编委，是自然首选） |
| LiveTCM 主仓 165+ 实验日志已就绪、demo repo 有 4★ | **LiveTCM 实验论文 2026 H2 投 ICSE / FSE / ISSTA 2027**（描述完整 LLM-driven adaptive testing 的实证） |
| 2026-04 IsingBench README 写完整、仓库 push | **IsingBench 论文很可能投 ASE 2026 / ISSTA 2026 Tool Track**，对标 EvoMaster 工具型论文路径 |

### 10.2 未来 1 年（→ 2027 中）

- 围绕 ModelCopilot 形成**第一代论文集**：1 篇平台架构 (TOSEM/TSE)、1 篇 PSUM 集成 (SoSyM/MODELS)、1 篇 LiveTCM 集成 (ICSE/FSE)、1 篇 IsingBench 集成 (ASE/ISSTA)，共 4 篇是合理上限。
- OMG 渠道：**很有可能拿到 OMG SysML v2 PSUM 工作组的官方 demo 时段**（岳涛是该工作组的中国学术界代表）。
- 中文圈：**公众号会被启用**，配合一篇国内顶级期刊（《软件学报》或《计算机研究与发展》）的中文综述出场——这是国内学者打 SCI 序列的标准动作。
- 团队规模 19 → 25（每年新增 5–6 个 MEng 是 BUAA 计算机学院常规节奏）。

### 10.3 未来 3 年（→ 2029）

| 路径 | 可能性 | 备注 |
|---|---|---|
| 与 OMG Pilot Implementation **并驾成为参考实现** | 高 | 既得利益方面 OMG 也希望多个参考实现 |
| 转化成商业工具直接对标 Cameo | 低 | 团队结构纯学术，无商业化迹象 |
| 与杭州华望 / 中国商飞 / 航天 N 院形成**国产 MBSE 工具链共建** | 中 | BUAA 与航空航天部委深度绑定，"国产替代"政策下被点名的概率高于平均 |
| 把 PSUM × Quantum-SE × LLM4MDE 三条线**收敛成"AI4MBSE"统一议程** | 高 | 这正是 modelcopilot.org 自我描述的"models come alive"愿景，且 MBSE Co-Pilot 同名论文（Loughborough）已在 INCOSE 提供国际叙事支持 |
| 输出**OMG 国际标准的中国主导子规范**（如 PSUM next version、Uncertainty Profile for SysML v2） | 中-高 | 岳涛已是 OMG SysML v2 核心贡献者；这是中国学者在 OMG 历史上的稀缺路径 |

## 11 与北航其它 MBSE 团队的关系

详见 [03-北航专项 §2.7.2 团队成员 + §3](03-beihang-investigation.md#27-wse-laboratory--modelcopilot-平台深读)。要点：

- **WSE-Lab（岳涛 + 张曼）vs 葛宁团队（北航软件学院）**：两条独立的 LLM × MBSE 路线，皆在北航。葛宁团队主走"形式化 + LLM × Lustre / SysML 行为建模"（Internetware 2025 + FASE 2026 + FSE 2025 SemServGen / DReM），WSE-Lab 主走"OMG 标准 + PSUM + 不确定性"。
- **WSE-Lab vs 鲁金直团队（北航航空学院 + 北理工）**：鲁金直走的是 KARMA + GB/T 45803 国家标准路径，与 OMG SysML v2 平行；WSE-Lab 走 OMG 国际标准路径。两条线在北航内部"并行而不交叉"。
- **WSE-Lab vs 吴际（北航计算机学院）**：吴际是 OMG PSUM 北航代表，与岳涛是直接合作（论文如 ICECCS 2024 BERT 测试架构生成）。WSE-Lab 与吴际团队**在 OMG 标准侧形成北航的 PSUM/SysML v2 联合代表**。

## 12 合作建议

如果你计划与 WSE-Lab 合作 / 引用 / 跟踪：

1. **想做 SysML v2 不确定性建模研究**：直接基于 [PSUM-SysMLv2 仓库](https://github.com/WSE-Lab/PSUM-SysMLv2)的 7 案例做扩展，引用 arXiv 2602.21641 + OMG PSUM 1.0 标准。可去信 manzhang@buaa.edu.cn / yuetao@buaa.edu.cn。
2. **想做 LLM × MBSE 综述 / 实证**：基于 [LLM4MDE 仓库](https://github.com/WSE-Lab/LLM4MDE) 的 254 篇做分析；该 artifact 暂未配套论文，是良好的 Related Work 起点。
3. **想做量子启发优化 + 测试**：直接 fork [IsingBench 仓库](https://github.com/WSE-Lab/IsingBench)，4 求解器 + 3 经典 SE 数据集 + 5 baseline 已就绪，是国内最完整的 SE × Ising 工具栈。
4. **想做自动驾驶 ADS 测试**：参考 [liveTCM-demo](https://github.com/WSE-Lab/liveTCM-demo) + LiveTCM 主仓 165+ LLM 实验日志做 baseline。
5. **想跟踪 OMG SysML v2 标准动态**：关注岳涛主页[^yue-tao-12] + OMG PSUM Wiki[^omg-psum-wiki-12]，最新动态会先反映在标准会议而非公众号。
6. **想做 ModelCopilot 平台用户**：等 2026 H2 平台 + WSE-ModelCopilot 仓库正式开放（参 §10.1 预测）；目前裸 IP demo `http://116.204.36.247` 偶尔可访问。

## 13 信息源（全 URL 实证）

### 仓库与平台

- WSE-Lab GitHub 组织：<https://github.com/WSE-Lab>
- PSUM-SysMLv2：<https://github.com/WSE-Lab/PSUM-SysMLv2>
- LLM4MDE：<https://github.com/WSE-Lab/LLM4MDE>
- QuantumOpt4SE：<https://github.com/WSE-Lab/QuantumOpt4SE>
- QuantumOpt4SE-EmpiricalStudies：<https://github.com/WSE-Lab/QuantumOpt4SE-EmpiricalStudies>
- IsingBench：<https://github.com/WSE-Lab/IsingBench>
- liveTCM-demo：<https://github.com/WSE-Lab/liveTCM-demo>
- LiveTCM：<https://github.com/WSE-Lab/LiveTCM>
- WSE-Lab.github.io：<https://github.com/WSE-Lab/WSE-Lab.github.io>
- WSE-ModelCopilot：**HTTP 404（不存在）**
- 实验室主站：<https://www.modelcopilot.org/>
- ModelCopilot 平台页：<https://www.modelcopilot.org/model-copilot.html>
- IsingBench 子页：<https://www.modelcopilot.org/QSE.html>
- LiveTCM 子页：<https://www.modelcopilot.org/ADS.html>
- ModelCopilot live demo：<http://116.204.36.247>（裸 IP）

### 论文

- arXiv 2602.21641 *Uncertainty Modeling for SysML v2*（Zhang/Li/Yue, 2026-02-25）：<https://arxiv.org/abs/2602.21641>
- arXiv 2506.16878 *Quantum Optimization for SE: A Survey*（Zhang/Li/Yue/Cai, 2025-06-20）：<https://arxiv.org/abs/2506.16878>
- arXiv 2510.27113 *Empirical Studies on Quantum Optimization for SE*（Zhang/Li/Yue/Cai, 2025-10-31）：<https://arxiv.org/abs/2510.27113>
- arXiv 2404.06825 *Quantum Software Engineering: Roadmap and Challenges Ahead*（Murillo et al, incl. Yue）：<https://arxiv.org/abs/2404.06825>
- INCOSE *Systems Engineering* 29(1):20-33 *MBSE Co-Pilot: A Research Roadmap*（**Loughborough Wenheng Zhang 等，与 BUAA 同名同题、与 ModelCopilot 无直接关系**）：<https://incose.onlinelibrary.wiley.com/doi/10.1002/sys.70011>

### 个人主页

- 岳涛 BUAA：<https://scse.buaa.edu.cn/info/1078/10993.htm>
- 岳涛 个人页：<https://yue-tao.github.io/>
- 张曼 BUAA：<https://scse.buaa.edu.cn/info/1079/10996.htm>
- 张曼 个人页：<https://man-zhang.github.io/>

### 公众号检索（均无命中，作为反向证据）

- 搜狗微信 `query=ModelCopilot`：<https://weixin.sogou.com/weixin?type=2&query=ModelCopilot>
- 搜狗公众号 `query=ModelCopilot`：<https://weixin.sogou.com/weixin?type=1&query=ModelCopilot>
- Bing `site:mp.weixin.qq.com ModelCopilot`：<https://www.bing.com/search?q=%22ModelCopilot%22+site%3Amp.weixin.qq.com>

## 参考文献

[^yue-buaa]: 岳涛 BUAA 中文主页. <https://scse.buaa.edu.cn/info/1078/10993.htm>

[^zhang-buaa]: 张曼 BUAA 中文主页. <https://scse.buaa.edu.cn/info/1079/10996.htm>

[^modelcopilot-platform]: ModelCopilot 平台子页（含 KerML 95.6% / SysML v2 87.9% 自报）. <https://www.modelcopilot.org/model-copilot.html>

[^psum-repo]: *WSE-Lab/PSUM-SysMLv2*. <https://github.com/WSE-Lab/PSUM-SysMLv2>

[^llm4mde-repo]: *WSE-Lab/LLM4MDE*. <https://github.com/WSE-Lab/LLM4MDE>

[^qopt-repo]: *WSE-Lab/QuantumOpt4SE*. <https://github.com/WSE-Lab/QuantumOpt4SE>

[^arxiv-2506]: Zhang, M., Li, Y., Yue, T., Cai, K.-Y. *Quantum Optimization for SE: A Survey*. arXiv:2506.16878 (2025-06). <https://arxiv.org/abs/2506.16878>

[^qopt-emp-repo]: *WSE-Lab/QuantumOpt4SE-EmpiricalStudies*. <https://github.com/WSE-Lab/QuantumOpt4SE-EmpiricalStudies>

[^arxiv-2510]: Zhang, M., Li, Y., Yue, T., Cai, K.-Y. *Empirical Studies on Quantum Optimization for SE: A Systematic Analysis*. arXiv:2510.27113 (2025-10-31). <https://arxiv.org/abs/2510.27113>

[^isingbench-repo]: *WSE-Lab/IsingBench*. <https://github.com/WSE-Lab/IsingBench>

[^livetcm-demo-repo]: *WSE-Lab/liveTCM-demo*. <https://github.com/WSE-Lab/liveTCM-demo>

[^livetcm-repo]: *WSE-Lab/LiveTCM*（含 165+ LLM 实验日志）. <https://github.com/WSE-Lab/LiveTCM>

[^wse-site-repo]: *WSE-Lab/WSE-Lab.github.io*. <https://github.com/WSE-Lab/WSE-Lab.github.io>

[^shi-2021-models-12]: Shi 等 *Restricted Natural Language and Model-based Adaptive Test Generation for Autonomous Driving*. MoDELS 2021. DOI 10.1109/MODELS50736.2021.00019.

[^arxiv-2602-12]: Zhang, M., Li, Y., Yue, T. *Uncertainty Modeling for SysML v2*. arXiv:2602.21641 (2026-02-25). <https://arxiv.org/abs/2602.21641>

[^mbse-copilot-loughborough-12]: Wenheng Zhang 等 *MBSE Co-Pilot: A Research Roadmap* (Loughborough University)，**与北航 ModelCopilot 无关**. INCOSE *Systems Engineering* 29(1):20-33 (2026), DOI 10.1002/sys.70011. <https://incose.onlinelibrary.wiley.com/doi/10.1002/sys.70011>

[^yue-tao-12]: Yue, Tao *Personal Homepage*. <https://yue-tao.github.io/>

[^omg-psum-wiki-12]: OMG PSUM Working Group Wiki. <https://www.omgwiki.org/uncertainty/doku.php?id=start>
