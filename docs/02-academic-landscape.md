# 02 学术文献引导页

## 本章简介

本章是 SysML v2 / KerML 学术文献的**入口与导航页**。覆盖 2024–2026 三个集中爆发年的 60+ 篇高质量论文与政策文档，按 **A–I 九个维度**组织，每条附中文一句话要点 + 作者机构 + 代码链接（若有）。读者读完应能：

- 找到与自己研究方向匹配的入口论文（A 语义形式化 / B 桥接变换 / C LLM × v2 / D 行业案例 / E 工作坊 / F 变体方法学 / G 综述 / H 博士论文与技术报告 / I 区域研究）。
- 区分**有代码可复现**与**仅论文**的工作（详 §11）。
- 把握全球**6 个研究中心**的分布与各自专长（详 §12）。
- 找到当前**4–5 个明显缺口**作为立项方向（详 §13）。

> **覆盖说明**：本章只列已发表 / 已公开预印本的学术性资料，不含 GitHub 仓库（→ [11-real-world-corpora.md](11-real-world-corpora.md)）、商用产品文档（→ [README §商用厂商支持速览](../README.md#商用厂商支持速览)）、或工具开发（→ [04-](04-parsing-ide-infrastructure.md) / [10-](10-daltskin-deep-audit.md)）。

## 1 调研动机与本章使用方式

OMG 在 2025-06-30 完成 SysML v2 Final Adoption[^omg-final]，2026-03 以 `formal/2026-03-0x` 文档号正式出版。Beta1 / Beta2 / Final 三段时间区间正好对应 v2 学术研究"奠基—爆发—工业化"三波。本章按以下三种使用方式服务读者：

1. **找入口论文**：按维度 A–I 取 1–2 篇最有代表性的就够了，不必读全章。
2. **找研究空白**：直接跳 §13 趋势观察 + §14 缺口清单。
3. **建立 Related Work**：从 §2 到 §10 的引文+脚注组合，可直接拷贝到自家论文/立项报告。

## 2 维度 A：核心规范语义与形式化

### 2.1 KerML 4D 时空语义批评

NEMO/UFES 团队 Almeida、Ferreira Pires、Guizzardi 在 ER 2024 发表的 *An Analysis of the Semantic Foundation of KerML and SysML v2*[^almeida-2024-er] 是这一波最有引用势能的文章。基于 UFO（Unified Foundational Ontology）的 4D 时空语义，系统比对 KerML 的 Feature/Specialization/Subsetting 与本体论意义上"分类"概念，指出多处不对齐——特别是 KerML 没有显式区分 *kind* / *role* / *phase*。

同团队 2025 年扩展工作 *Towards an Ontology of Type-Level Phenomena for System Modeling*[^almeida-2025-type-level] 进一步用多层级（MLT）理论把 SysML v2 的"组件类型 / 连接类型 / 特化关系"重塑为良基（well-founded）的领域无关本体。这是把 KerML 推向"领域无关的可重用元元层"最有野心的尝试。

### 2.2 模型检查与时序逻辑

Molnár & Graics（FBK + BME）在 MODELS 2024 的 *Towards the Formal Verification of SysML v2 Models*[^molnar-2024-models] 是首批把 v2 状态机映射到 Gamma 框架做验证的论文。前端代码本身没合并到 ftsrg/gamma 主仓（详 [05-formal-verification.md §3.2](05-formal-verification.md#3-ftsrggamma--bme-状态机验证框架)），但指出了"v2 状态机 → UPPAAL/Theta/nuXmv"路径的可行性。

FBK 在 2025 年正式发布 SAWS² 工具[^fbk-saws2-2025]：基于 xSAP 后端做 SysML v2 安全分析 + 模型检测 + 故障树 / FMEA 表生成 + 组件级合约验证。**目前 FBK 工具页是封闭的，仅描述能力，不开源**。

德国 Forsch. Ingenieurwes. 期刊在 2025 年发表 *Enhancing Model-Based Development with Formalized Requirements*[^forsching-ltl-2025]，把 LTL 形式化需求与 SysML v2 状态机绑定——填补 v2 状态行为与时序逻辑属性之间的语义桥梁。

### 2.3 Lean / Coq / Isabelle / Imandra 形式化

`chantakan/verified-mbse`（Lean 4 实现）已在 [05 §1](05-formal-verification.md#1-chantakanverified-mbse--lean-4-形式化) 详评。

更值得注意的是 **Imandra Inc. 的工业级商业线**：2024–2025 系列博文 *Automated Reasoning for SysML v2 (Parts 1-3)*[^imandra-2024] 演示如何把 v2 模型自动翻译到 Imandra Modeling Language (IML)，证明交通灯三个属性（确定性、错误处理、安全切换）。**闭源商业 license**，但其工程化程度是当前 v2 形式化最成熟的一家。

### 2.4 OWL / RDF 语义层

NASA JPL + openCAESAR 团队的 *SysML v2 Ontology in OWL2-DL*[^opencaesar-onto-2024] 把 KerML/SysML v2 元模型自动转 OML / OWL2-DL 词汇表，输出 RDF 三元组，打通 SPARQL 查询与 Flexo MMS 知识库管理。代码在 <https://github.com/opencaesar>。

TUM 硕士论文 *Interpreting SysML Diagrams to OWL Ontologies for Engineering Knowledge Reuse*[^harder-2025-tum] 给出了 SysML 包→ontology、需求→class、依赖→object property 的系统化映射，是知识图谱驱动 MBSE 的方法学起点。

### 2.5 元模型驱动校验

*Ensuring Semantic Consistency in SysML v2 Models Through Metamodel-Driven Validation*[^metamodel-2025] 在 ResearchGate 2025 发表，提出一组工业流水线可用的约束集合。

> **维度 A 趋势**：核心语义研究从"批评 + 建议"（Almeida 2024）转向"工具化"（FBK SAWS²、Imandra、OWL/OML 落地），并向"多层建模 / 不确定性 / 时序逻辑扩展"分化。

## 3 维度 B：桥接与变换（v2 ↔ AADL/Modelica/Capella/AAS/Code）

### 3.1 v2 ↔ AADL

Litwin/Amundson/Verma/McDermott 在 SAE AeroTech 2024 的 *Transforming AADL Models Into SysML 2.0*[^litwin-2024] 给出 AADL → SysML v2 的转换规则与 case study；姊妹篇 *AADL modelling with SysML v2*[^aadl-sigada-2023] 在 ACM SIGAda Ada Letters 2023 发表。**两篇均无开源转换器代码**——可参考 [Systems-Modeling/SysML-v2-AADL-Release][^repo-aadl-release-2]（library，不是 transformer）。

更进一步的工作是 **HAMR + SysML v2**——KSU + Adventium 团队 Hatcliff、Belt、Robby 等在 FMICS 2025 的 *End-to-End Formal Methods Integrated Development with SysMLv2 Using HAMR*[^hatcliff-fmics-2025]。在 SysML v2 RTESC AADL library 前端 + GUMBO 合约 + HAMR 后端构建端到端形式化方法工具链；SMT 模型集成检查 + 自动测试。代码：<https://hamr.sireum.org/>、<https://sireum.org/hamr-sysmlv2/>。详见 [05 §4](05-formal-verification.md#4-sireum-hamr--高保障代码生成)。

Hardin、Slind 等在 DASC 2025 / HCSS 2025 (DARPA PROVERS) 的 *Automated SysML v2 System Model to Memory-Safe Language Code Generation for Avionics Applications*[^hardin-2025-dasc] 把整条链推到 Rust/Verus 自动代码生成，部署到 seL4 微内核。代码：[GaloisInc/HARDENS](https://github.com/GaloisInc/HARDENS)（[HARDENS 仓库实测见 11 §3.5](11-real-world-corpora.md#35-行业--oem--工业出版物)）。

### 3.2 v2 ↔ Modelica / FMI / Simulink

Springer LNCS 2024 *SysML v2 for Automated Co-simulation from Systems Architecture Models*[^zimmermann-2024-cosim] 提出基于 v2 API + FMI 的自动 co-sim 网络。INCOSE IS 2024 *Bidirectional SysML v2 ↔ Modelica Transformation*[^pepper-2024-incose] 同样未发布配套代码。

DarTwin 数字孪生 DSL 形式化方向：Haugen、Klikovits 等在 arXiv 2510.12478 的 *DarTwin made precise by SysML v2 — An Experiment*[^haugen-2025-dartwin]。

### 3.3 v2 ↔ Capella / Arcadia

Thales / Obeo 团队 Bonnet 等在 INCOSE IS 2024 *Integrating Arcadia and Capella with SysML v2*[^bonnet-arcadia-2024]。通过"Arcadia 概念库 + KerML 特化"路径，让 SysML v2 直接承载 Operational/System/Logical/Physical 四层 Arcadia 视点；并演示 SysON 扩展。

### 3.4 v2 ↔ Asset Administration Shell（工业 4.0）

JKU Linz 团队的 *Towards Interoperable Digital Twins: Integrating SysML into AAS with Higher-Order Transformations*[^jku-iiia-2024] 用 ATL/HOT 高阶模型变换把 SysML（v1→v2 路径）模型注入 AAS submodel 模板，制造业 RFID 案例验证。

Computers in Industry 2025 *From engineering models to digital twins: Generating AAS from SysML v2 models*[^cii-aas-2025] 首次给出 SysML v2 → AAS submodel 的元模型级映射规则与原型变换器。

Software and Systems Modeling 2025 综述 *Digital twin and the asset administration shell*[^zehetner-sosym-2025] 系统比较 DT/AAS 两套元模型，并明确 SysML v2 在工业 4.0 数字孪生工程中的接入位点。

### 3.5 v1 → v2 迁移

OMG Part 2 Transformation 规范本身已在 [01-standard-status.md §17](01-standard-status.md#17-sysml-v1--v2-转换现状与本地验证) 详评。**最重要的工业实战报告**是 Sensmetry + DoD 2025 的 DETECT 案例 *DETECT Tool Migration from SysML v1 to SysML v2 with Syside*[^detect-syside-2025]。美国国防部 OUSD(R&E) 在 2025 年 3 月把 DETECT 数字工程评估工具从 v1 迁移到 v2（Syside Modeler + Syside Automator），是首个公开的 v1→v2 工业级迁移实战报告。

中国学者团队 Zhang Yifan、Du Huanchao 等在 IEEE 国际会议 2025 *Research on Model Conversion from SysML v1 to SysML v2*[^zhang-cnki-v1tov2-2025]，重点处理 BDD/IBD/Activity 三类映射，案例为军用嵌入式设备模型。

### 3.6 数据交换全景综述

Zhou、An、Yu、Li 等在 Springer LNCS MDS 2024 的 *Data Exchange for SysML: A Review*[^zhou-mds2024] 把 SysML 数据交换分成 5 类（model exchange / transformation / generation / code-gen / doc-gen），并指出 v2 的 JSON+REST API 如何重构这一图景。

> **维度 B 趋势**：从早期"映射规则"研究（Litwin 2024）走向"端到端代码生成 / 工业级迁移"，并扩展到 AAS、Arcadia、I4.0 数字孪生这三条新航线。HAMR + SysML v2 + Rust + seL4 是 2024–2026 最长的端到端链。

## 4 维度 C：LLM / AI × SysML v2

### 4.1 多 Agent / 模板生成

Bouamra、Yun 等（Lyon 1 / LIRIS）arXiv 2506.21608 *SysTemp: A Multi-Agent System for Template-Based Generation of SysML v2*[^bouamra-2025-systemp]。多 Agent + 模板，~80% 句法正确。

Computers in Industry 2025 *An Agent-Based Approach for the Automatic Generation of Valid SysMLv2 Models in Industrial Contexts*[^ci-rag-2025] 用 RAG + ANTLR 校验闭环，号称 100% 句法合法。

### 4.2 NL → SysML 基准与评估

金芝（北大）等 arXiv 2508.03215 *SysMBench: A System Model Generation Benchmark from Natural Language Requirements*[^jin-2025-sysmbench]。**首个公开 NL → system model 基准**，151 个人工标注场景，17 个 LLM 评测。最高 BLEU 4%、SysMEval-F1 62%——揭示 LLM 表现仍很差。

Wang、Ge、Hu 等（北航软件学院）在 Internetware 2025 的 *Generating SysML Behavior Models via Large Language Models: An Empirical Study*[^wang-2025-internetware]。107 个 SysML 行为模型数据集，17 个 LLM 评测幻觉与生成质量；语义 F1 在序列图最低（50%），是中国学界 LLM × SysML 实证研究代表作。详见 [03-beihang-investigation.md §2.4](03-beihang-investigation.md#24-北航软件学院-llm--sysml-研究)。

### 4.3 跨组织协作 / 语义对齐

arXiv 2508.16181 *LLM-Assisted Semantic Alignment and Integration in Collaborative Model-Based Systems Engineering Using SysML v2*[^arxiv-llm-semantic-2025]。跨组织 MBSE 协作中用 GPT 完成 SysML v2 模型对齐：模型抽取→语义匹配→映射验证三阶段提示工程。

INCOSE IS 2025 Rafique 等 *Enhancing Model-Based Systems Engineering with Large Language Models*[^rafique-incose-2025] 对比 GPT-4（通用）与 CodeT5（领域微调）在 SysML v2 模型生成上的表现；结论是单凭 LLM 全自动生成不可靠，须人机回环。

### 4.4 文档 → 模型自动化

INCOSE IS 2025 Johnson、Williams 等 *Automated Legacy Documentation to SysML Conversion*[^johnson-incose-2025]：非结构化文档 → SysML BDD 的端到端 LLM 管线，引入 Groovy 脚本导入 Cameo + 图论不变量做正确性校验。

arXiv 2507.06803 *Text to Model via SysML: Automated generation of dynamical system computational models from unstructured natural language text via enhanced System Modeling Language diagrams*[^arxiv-text-to-model-2025]。文本→SysML BDD→可执行计算模型的端到端管线；NLP 处理摘要、LLM 仅做验证；单摆案例显示比 zero-shot LLM 显著提升。

INCOSE IS 2026 Paper #137 *Using LLMs to Convert Documentation to SysML*[^incose-paper-137]。

### 4.5 知识图谱缺失链接预测

Wiley Systems Engineering 29 (2026) Karagoz 等 *Identification of Missing Knowledge in MBSE System Models Using Graph-Based Machine Learning*[^karagoz-sysengr-2026]。把 SysML 模型转知识图谱，用 R-GCN/GNN 做缺失链接预测，准确率 72%；可发现人类分析师漏掉的关系。

### 4.6 安全需求

IACIS IIS 2025 *Leveraging AI-Driven Requirements for SysML Modeling of Cybersecurity*[^iacis-iis-2025]。用 ChatGPT 自动从安全需求文本生成 SysML v2 安全要素。

### 4.7 卫星系统架构生成

Tandfonline Journal of Engineering Design 36 (2025) *Assessment of Large Language Models for Use in Generative Design of Model-Based Spacecraft System Architectures*[^ieee-genai-spacecraft-2025]。在 MBSE+SysML 设定下评估 GPT-4/Claude/Gemini 等做卫星系统架构生成的可用性。

### 4.8 NPS DAIR 政策报告

Naval Postgraduate School DAIR *Leveraging Generative AI to Build, Modify, and Query MBSE Models*[^nps-dair-2024] (SYM-AM-24-138, 2024) 是美国国防分析的政策方向论文。

> **维度 C 趋势**：研究焦点从"能否生成"已转到"语法 vs 语义可靠性、人机回环、跨组织对齐、知识图谱预测"；BUAA、欧美工业界与 INCOSE 三股力量同时推进。商用 Visual Paradigm SysML v2 Studio[^vendor-vp] 把 LLM 助手做进了图形 IDE。

## 5 维度 D：行业 / 领域案例研究

### 5.1 航空 / 航天 / 国防

Ahlbrecht 等（DLR）DASC 2024 *Exploring SysML v2 for Model-Based Engineering of Safety-Critical Avionics Systems*[^ahlbrecht-2024-dasc]。

Hardin 等 DASC 2025 / HCSS 2025（已在 §3.1 列出）。

DLR-FT 团队 Wiley Systems Engineering 29 (2026) *Extending SysML v2 for Safety – Open-Source Library for the System-Theoretic Process Analysis*[^ahlbrecht-stpa-sysengr-2026] 为 STPA 系统理论安全分析建一套 SysML v2 viewpoint/view 库，**开源**：<https://github.com/DLR-FT/SysMLv2LibrarySTPA>。

Hintze 等（Airbus + Hamburg 工大）INCOSE IS 2025 *SysML4Sec – Methodology for Security Modeling in the Context of Large-Scale Product Development with Multiple Design Levels*[^hintze-sysml4sec-2025]。空客团队设计的多层安全工程语言扩展，对接 DO-326A。

SAE Tech Paper 2025-01-0172 *Enhancing Airworthiness Security: SysML-Based Approach to Modelling Security Scope Definitions*[^sae-airworthiness-2025] 基于 CORAS profile 的 SysML 安全范围建模，符合 ED-202A/DO-326A。

ESA 团队 Duroy 等 Wiley Systems Engineering 2025 *Paving the Way for SysML v2: An ESA MBSE Methodology Implementation Review*[^duroy-2025-esa]。

GM / GPDIS 2024 *MBSE Collaboration with SysML 2.0: A Pre-Release Use*[^gpdis-2024]。

### 5.2 机械工程

Boelsen、May、Jacobs 等（RWTH Aachen）Forsch. Ingenieurwes. 2025 *SysML v2 based Modelling Guidelines for Mechanical System Elements*[^boelsen-mech-2025]。**首次给机械工程师量身定制 SysML v2 建模规范**（轴承、齿轮、传动件等元素的 stereotype 库），填补机械域使用门槛。

### 5.3 汽车

INCOSE IS 2024 *Bidirectional SysML v2 ↔ Modelica Transformation*[^pepper-2024-incose]（已在 §3.2 列出，汽车视角）。

行业白皮书 *SysML v2: The Next Frontier in Automotive System Modeling*[^automotive-mbse-explained-2025]。汽车制动系统 v2 案例 + ASIL 安全等级映射 + 软硬件协同。

Granrath（RWTH）Wiley Systems Engineering 2025 *Generating Logical Architectures from SysML Behavior Models*[^granrath-2025-logical-arch]：以 SysML AD→IBD 模型到模型变换，从行为模型自动派生逻辑架构；汽车域 LRA 案例验证。

### 5.4 零排放 / 电气化飞行系统

Vincent 等 DLRK 2024 *Applied Model-Based Co-Development for Zero-Emission Flight Systems Based on SysML*[^vincent-2024-zero-emission]。氢能/电推进飞行系统多方协同建模；从 SysML v1 走向 v2 的工业实践。

### 5.5 不确定性建模（PSUM）

Zhang、Li、Yue（北航）arXiv 2602.21641 *Uncertainty Modeling for SysML v2*[^zhang-2026-uncertainty]。把 OMG PSUM 元模型注入 SysML v2/KerML，提出 PSUM-SysMLv2 stereotype 库；7 个案例研究验证。

### 5.6 工业出版物 / 厂商演示

Ansys MODELS 2024 Industry Day *SysML v2 Modeler and Digital Engineering Methodology*[^ansys-models-2024]。

> **维度 D 趋势**：航空（DASC, AvioSE, SAE）+ 安全工程库（DLR STPA, SysML4Sec）成为最活跃的工业入口；机械域刚刚启动；汽车 ISO 26262 更多在白皮书层面。

## 6 维度 E：工作坊 / 立场 / 经验报告

### 6.1 ESA 与欧洲航天联合开发

ESA + Starion + Sensmetry + Obeo + Ansys + Dassault 2025 *MBSE 2025 SysML v2 Hackathon Report — COMET Interceptor 任务*[^mbse2025-hackathon]。**5 工具厂商联合开发**的 v2 空间领域库；ESA CDF COMET Interceptor 可行性研究模型为基底；是 v2 跨工具互操作性的重要实测。

### 6.2 OMG SysML v2 认证体系

Steiner 等 INCOSE IS 2025 *OMG's Approach to Developing its SysMLv2 Certification Program*[^steiner-omg-cert-2025]。OMG SysMLv2 Certification Working Group (SCWG) 的认证考试设计与挑战。

### 6.3 多层建模

Lange、Cederbladh、Feichtinger、Weber INCOSE IS 2025 *An Initial Exploration of MULTI Level Modeling for Model-Based Systems Engineering*[^lange-multi-incose-2025]。在 SysML v2 之上探索多层建模 (MULTI) 范式。

### 6.4 MagicGrid 方法论实践

Aleksandraviciene（Dassault Systèmes）INCOSE IS 2025 *Exploring the Use of SysMLv2 for Solution Architecture Development with the MagicGrid Framework*[^aleksandraviciene-magicgrid-2025]。用 MagicGrid 方法在 v1 vs v2 上做对比案例研究。

### 6.5 Living SysML v2 Blueprint 路线图

Teodorov、Lima、Nogueira、Guerin、Lagadec 在 SBMF 2025 *A Research Agenda for the Living SysML V2 Blueprint*[^teodorov-2025-blueprint] 提出统一执行 / 多宇宙状态探索 / 原生形式化验证的"SysML v2 虚拟机"路线图。

> **维度 E 趋势**：2024–2025 是 v2 的"治理与方法学奠基年"，认证、产品线、多层建模、空间方法论这四条线同时启动。

## 7 维度 F：变体 / 产品线 / 方法学

### 7.1 v2 作为变体建模语言

Debbiche 等 Innovations in Systems and Software Engineering 2024 *Transitioning towards SysML v2 as a Variability Modeling Language*[^debbiche-isse-2024]。把 SysML v2 当作正交变体建模语言，证明 v2 的 Variation/Variant/Choice 构造可代替 FODA 类特征模型；汽车案例。

### 7.2 MBPLE × API 潜能

Weilkiens（oose）INCOSE IS 2025 *Next Generation MBPLE with SysML v2: Feature Modeling, Variability Modeling, and API Potentials*[^weilkiens-mbple-2025]。从产品线工程 (MBPLE) 视角全面解读 v2 的 Variation/Variant 构造与 API 潜能；指出 v2 可成 MBPLE 工具链中枢。

### 7.3 可验证-感知变体建模

Kausch、Pfeiffer、Raco、Rumpe 等 AvioSE'26 *Towards Verifiability-Aware Variability Modeling using SysML v2 for Security- and Safety-Critical Avionics*[^kausch-aviose26]。在 SysML v2 之上构建"可验证-感知"的变体建模框架，把变体选择与安全/性能形式化属性绑定。

### 7.4 行为 → 逻辑架构反向通路

Granrath 2025（已在 §5.3 列出）：以 feature-driven 方法把活动图功能映射到逻辑架构。

> **维度 F 趋势**：v2 的 Variation 构造正快速取代 v1+orthogonal 变体，PhD 级研究开始出现（见 §9）。

## 8 维度 G：综述 / 书目计量 / 对比研究

### 8.1 MBSE 整体格局

ScienceDirect 2026 *Mapping the Landscape of Model-Based Systems Engineering (MBSE) through Bibliometric and Network Analysis*[^mbse-bibliometric-2026]。这是 v2 之外的 MBSE 整体书目计量，是把 v2 放到大背景的"位置定位"参考。

### 8.2 SysML 过程链综述

Zhang 等 ScienceDirect 2025 *SysML Process Chains in MBSE: Systematic Literature Review and Future Research Directions*[^zhang-process-chains-2025]。审视 412 篇 MBSE 文献后精读 43 篇，构建 SysML 过程链分析框架；是 v2 之前的"过程链"系统综述。

### 8.3 数据交换综述

Zhou 等 LNCS MDS 2024（已在 §3.6 列出）。

### 8.4 产业立场

MathWorks DigitalEng 2026 *Why MBSE Still Breaks at the Seams and How SysML v2 Could Help*[^mathworks-2026-mbse-seams]。MathWorks 视角的 v2 与传统 MBSE "缝合"问题诊断。

> **维度 G 趋势**：MBSE 综述虽多，但**专门 SysML v2 的系统综述目前仍稀缺**——本仓库正是填补这一空白。

## 9 维度 H：博士论文与技术报告

### 9.1 第一篇 v2 PhD

Munezero（Old Dominion University）2025 *A SysML v2 Implementation of a Traceability and Verification Metamodel for "-Ilities"*[^munezero-thesis-2025]。**迄今公开的第一份专门以 SysML v2 为载体的 PhD 论文**；构建"非功能需求 (-ilities)"元模型，覆盖 safety / HSI / cybersecurity，对齐 ISO/IEC/IEEE 29148。

### 9.2 美国国防部官方文档

US DoD CTO Office *SysML v1 to SysML v2 Model Conversion Approach (Tech Report 1.3)*[^dod-cto-2024]。美国国防部正式发布的 v1→v2 迁移技术报告，给出预处理、变换、后处理、验证四阶段流程；附 SkyZer 任务模型示例。

US DoD 2025 *SysML v2 Technical Highlight (Info Sheet 08/04/2025)*[^dod-sysml-info-sheet-2025]。v2 在国防数字工程战略 (DoDI 5000.97) 中的官方定位文件。

### 9.3 DARPA PROVERS

DARPA 2025 *Pipelined Reasoning of Verifiers Enabling Robust Systems (PROVERS) Program Description*[^darpa-provers-2025]。DARPA PROVERS 项目把 SysML v2 + AADL + HAMR + Logika 列为关键验证基础；推动"军事级形式化数字工程"的政策性技术路线图。

### 9.4 DLR 内部报告

Frahm 等 DLR Tech Report 2025 *Versionskontrolle und Kollaboration in MBSE: Untersuchung der Git-Integration mit SysML v2*[^dlr-git-mbse-2025]（德文）。研究 v2 文本表示如何利用 Git 做并行开发与冲突合并；是 "MBSE as code" 实践证据。

### 9.5 Fraunhofer IPK 工具演示

Fraunhofer IPK 2025 *Interoperability Live - SysML v2 API in Action / Praktische Anwendung der SysML v2 API am Beispiel von MCAD und Simulation*[^fraunhofer-interop-live-2025]。用 FreeCAD + FCInfo + SysML v2 API 服务器演示机械 CAD ↔ 系统模型的双向同步。

### 9.6 INCOSE 2026 论文

INCOSE IS 2026 Paper #137 *Using LLMs to Convert Documentation to SysML*[^incose-paper-137]。

> **维度 H 趋势**：政府报告（DoD、DARPA、DLR、Fraunhofer）正在把 v2 写进国家级数字工程战略，这是中文综述需要单独提及的政策维度。

## 10 维度 I：区域 / 语言研究

### 10.1 中国

四股主力：

1. **北航**：岳涛 + 吴际 + 葛宁/胡春明，详见 [03-beihang-investigation.md](03-beihang-investigation.md)。
2. **北大**：金芝团队 SysMBench[^jin-2025-sysmbench]。
3. **大连理工**：Ruizhe Yang 团队（[SysMLine][^repo-sysmline-2] / [SysMini][^repo-sysmini-2] / [SysMLOC][^repo-sysmloc-2] / [CODES][^repo-codes-2]）。
4. **个人 / 论文**：Zhang、Du 等 IEEE 2025 *v1→v2 模型转换*[^zhang-cnki-v1tov2-2025]。

**关于 Tao Yue 的额外说明**：经检索 Simula + BUAA 主页 + arXiv + DBLP，目前公开可见的"SysML v2 时代"论文有：(1) *Uncertainty Modeling for SysML v2*（arXiv:2602.21641, 2026）；(2) *Traceability and SysML Design Slices to Support Safety Inspections*（TOSEM 2014，v1 时代经典）。她目前是 OMG Uncertainty Modeling 工作组 Core Package 负责人，参考 <https://www.omgwiki.org/uncertainty/doku.php?id=start>。截至 2026-05，未发现她单独以 v2 为主题的其它学术论文。

### 10.2 德国（学术 + 工业最密集）

- **RWTH Aachen**：MontiCore SysML v2[^repo-monticore-3]、Granrath 逻辑架构论文、Boelsen 机械域指南、Kausch 可验证变体；以及 Vincent 零排放飞行系统多方协同建模。
- **DLR**：Ahlbrecht 团队 DASC 2024 + STPA 库 + Frahm Git 集成报告。
- **Fraunhofer IPK**：v2 API + MCAD 互操作演示。
- **TU München**：Harder 硕论 OWL 互操作。
- **Hamburg + Airbus**：Hintze SysML4Sec 多层安全建模。

### 10.3 法国 / 欧洲航天

- **Thales / Obeo**：Bonnet 等 Arcadia/Capella ↔ v2[^bonnet-arcadia-2024]。
- **ENSTA Bretagne / Lab-STICC**：Teodorov Living Blueprint[^teodorov-2025-blueprint]。
- **Lyon 1 / LIRIS**：Bouamra SysTemp[^bouamra-2025-systemp]。
- **ESA**：Duroy MBSE methodology review[^duroy-2025-esa]、MBSE 2025 Hackathon[^mbse2025-hackathon]。

### 10.4 美国 / 国防与航天

- **Galois**：HARDENS / INSPECTA（[10 §5](10-daltskin-deep-audit.md#5-下游消费者谁用了它)）。
- **Kansas State + Adventium Labs**：HAMR / Sireum 工具线（[05 §4](05-formal-verification.md#4-sireum-hamr--高保障代码生成)）。
- **NASA JPL**：openCAESAR / OML SysML v2 ontology[^opencaesar-onto-2024]、Open MBEE Flexo MMS（[06 §3](06-visualization-collaboration.md#3-open-mbee-flexo-mms)）。
- **Old Dominion University**：Munezero 第一篇 v2 PhD[^munezero-thesis-2025]。
- **NPS DAIR**：政策报告[^nps-dair-2024]。
- **DoD CTO + DARPA**：政策与项目蓝图。

### 10.5 巴西 / 荷兰 / 葡萄牙

- **NEMO/UFES + U. Twente**：Almeida + Ferreira Pires + Guizzardi 一脉，KerML 4D 时空语义批评 + 多层建模本体。

### 10.6 立陶宛 / 商用工业

- **Sensmetry**：Syside Editor + Syside Automator + DETECT 案例[^detect-syside-2025]、Advent of SysML v2[^sensmetry-advent]。
- **Dassault Systèmes**：Aleksandraviciene MagicGrid v1↔v2 对比[^aleksandraviciene-magicgrid-2025]。

### 10.7 意大利

- **FBK Trento**：SAWS² 工具线[^fbk-saws2-2025]、Molnár 2024 论文[^molnar-2024-models]。

### 10.8 奥地利

- **JKU Linz**：JKU IIIA SysML v2 ↔ AAS[^jku-iiia-2024]、Zehetner SoSyM 2025[^zehetner-sosym-2025]。

### 10.9 暂未发现专门 v2 学术论文的区域

韩国（KAIST/Hyundai）、日本（Mgnite 是工业实验，未见学术论文）、印度。**这些是中文综述可以联合的潜在合作面**。

> **维度 I 趋势**：v2 的全球研究中心可清楚识别为**6 大簇** —— ① 美国（DoD/DARPA + Galois + Kansas State + JPL）；② 德国（RWTH + DLR + Fraunhofer + Hamburg + Airbus）；③ 法国/欧洲航天（ESA + Thales + Obeo + CEA）；④ 中国（BUAA Yue 组 + Wang/Ge/Hu Internetware 组 + 大连理工 + 北大）；⑤ 巴西 + 荷兰（UFES NEMO + Twente）；⑥ 立陶宛工业（Sensmetry + Dassault）。

## 11 论文 vs 代码：诚实标注

学术声称与可获得工具之间的 gap 是综述中最容易被掩盖的一点。本仓库依据 cite key 一一核查，结果（**[10 §5 daltskin 下游 + 11 真实语料调研](10-daltskin-deep-audit.md#5-下游消费者谁用了它)** 给的代码状态作为补充）：

### 11.1 有公开代码可复现

| 论文 | 代码仓库 |
|---|---|
| Hardin 2025 DASC（HAMR seL4 Rust）[^hardin-2025-dasc] | [GaloisInc/HARDENS](https://github.com/GaloisInc/HARDENS) |
| Hatcliff 2025 FMICS（HAMR + GUMBO + Logika） | <https://hamr.sireum.org/> |
| Ahlbrecht 2026 STPA Library | [DLR-FT/SysMLv2LibrarySTPA](https://github.com/DLR-FT/SysMLv2LibrarySTPA) |
| Wang 2025 Internetware（北航 LLM 行为模型）[^wang-2025-internetware] | 数据集随论文公布 |
| Jin 2025 SysMBench[^jin-2025-sysmbench] | 基准已公开 |
| Almeida 2024 ER + 2025 type-level（间接） | gUFO + OntoUML 工具链 |
| openCAESAR SysML v2 Ontology[^opencaesar-onto-2024] | [opencaesar/oml](https://github.com/opencaesar/oml) |
| Munezero 2025 PhD | Eclipse IDE 模型示例随论文 |
| Sensmetry DETECT 案例[^detect-syside-2025] | DETECT Web 应用公开 |

### 11.2 论文已发表但代码未公开

| 论文 | 代码状态 |
|---|---|
| Almeida 2024 ER（KerML 语义批评） | 描述性分析；无 SysML v2 → gUFO 校验器 |
| Molnár 2024 MODELS（Gamma 验证）[^molnar-2024-models] | **代码未合并主仓**（详 [05 §3](05-formal-verification.md#3-ftsrggamma--bme-状态机验证框架)） |
| Teodorov 2025 SBMF（Living Blueprint） | 零代码 |
| Litwin 2024 AeroTech（AADL → v2） | 仅论文 |
| Zimmermann 2024 LNCS（Co-simulation） | 无代码 |
| Pepper 2024 INCOSE（双向 v2 ↔ Modelica） | 无代码 |
| Ahlbrecht 2024 DASC（DLR avionics case） | 无代码 |
| Haugen 2025 arXiv（DarTwin × v2） | 无代码 |
| Granrath 2025 Wiley（行为 → 逻辑架构） | 无代码 |
| Imandra 2024 系列博文 | **闭源商业** |
| FBK SAWS² 2025 | **闭源工具页** |
| Bonnet 2024 INCOSE（Arcadia × v2） | SysON 仓库内有试验包 |
| JKU AAS 2024 | 论文中列出仓库链接但需验证 |
| Cii AAS 2025 | 无代码 |
| Boelsen 2025 机械域指南 | 无代码 |
| Hintze 2025 SysML4Sec | 无代码 |
| Steiner 2025 OMG 认证 | 政策文档 |
| Lange 2025 MULTI MBSE | 无代码 |
| Aleksandraviciene 2025 MagicGrid | 商业工具实践 |

### 11.3 政策 / 战略文档（非代码）

DoD CTO 1.3、DARPA PROVERS、DLR Git report、Fraunhofer IPK demo 等。

## 12 主要研究团队（更新版）

| 团队 / 实验室 | 研究方向 | 代表产出 |
|---|---|---|
| **NEMO @ UFES + U. Twente** | UFO 本体论视角的 KerML 语义、多层建模 | Almeida 2024 ER[^almeida-2024-er]、2025 type-level[^almeida-2025-type-level] |
| **FBK Trento + BME 布达佩斯** | SysML v2 → Gamma 形式化验证；FBK SAWS² 工具线 | Molnár 2024 MODELS[^molnar-2024-models]、SAWS² 2025[^fbk-saws2-2025] |
| **Kansas State + Adventium + Galois** | HAMR / Sireum + GUMBO 端到端高保障代码生成 | Hatcliff 2025 FMICS[^hatcliff-fmics-2025]、Hardin 2025 DASC[^hardin-2025-dasc] |
| **DLR（德国宇航中心）** | 民航航电 + STPA 安全分析库 + Git/MBSE 集成 | Ahlbrecht 2024 DASC[^ahlbrecht-2024-dasc]、STPA Library 2026[^ahlbrecht-stpa-sysengr-2026]、Frahm 2025 Git report[^dlr-git-mbse-2025] |
| **RWTH Aachen MontiCore + 机械系** | MontiCore 独立实现、机械域建模指南、可验证变体 | MontiCore/sysmlv2[^repo-monticore-3]、Boelsen 2025[^boelsen-mech-2025]、Kausch 2026[^kausch-aviose26] |
| **ENSTA Bretagne / Lab-STICC** | 执行语义 / Living Blueprint 议程 | Teodorov 2025 SBMF[^teodorov-2025-blueprint] |
| **Université Lyon 1 / LIRIS** | LLM 多 Agent 生成 | Bouamra 2025 SysTemp[^bouamra-2025-systemp] |
| **Hamburg + Airbus** | 多层安全工程语言扩展 | Hintze 2025 SysML4Sec[^hintze-sysml4sec-2025] |
| **Antwerp / NTNU / Linz** | Digital Twin / MDE × v2 | Haugen 2025 DarTwin[^haugen-2025-dartwin] |
| **JKU Linz** | AAS × SysML v2 互操作 + DT 综述 | JKU IIIA 2024[^jku-iiia-2024]、Zehetner 2025[^zehetner-sosym-2025] |
| **Old Dominion University** | -ilities 元模型 + SysML v2 PhD | Munezero 2025 PhD[^munezero-thesis-2025] |
| **NASA JPL + Open MBEE** | OML/OWL ontology + Flexo MMS | openCAESAR 2024[^opencaesar-onto-2024] |
| **Sensmetry + Dassault Systèmes（立陶宛）** | Syside / Automator / DETECT 工业实战 + MagicGrid 方法学 | DETECT 2025[^detect-syside-2025]、Aleksandraviciene 2025[^aleksandraviciene-magicgrid-2025] |
| **北京航空航天大学** | OMG SysML v2 标准化 + PSUM + LLM × SysML 实证 | Yue PSUM Co-chair；Zhang/Li/Yue 2026[^zhang-2026-uncertainty]；Wang/Ge/Hu Internetware 2025[^wang-2025-internetware] |
| **北京大学** | NL → 系统模型基准 | Jin 2025 SysMBench[^jin-2025-sysmbench] |
| **大连理工大学** | SysMLine 工具 + 国内最完整 v2 语料 | [SysMLine + SysMini + SysMLOC + CODES][^repo-sysmline-2] |
| **oose Innovative Informatik（Tim Weilkiens）** | MBPLE × SysML v2 + 教学出版物 | Weilkiens 2025[^weilkiens-mbple-2025]、SysML v2 Book |
| **Imandra Inc.** | IML 自动定理证明 + v2 闭源 transpiler | Imandra 2024 博文系列[^imandra-2024] |
| **TUM** | OWL 互操作 + 知识图谱驱动 MBSE | Harder 2025 硕论[^harder-2025-tum] |
| **Old Dominion + 美国学界** | 知识图谱 ML 缺失链接预测 | Karagoz 2026[^karagoz-sysengr-2026] |

## 13 趋势观察

1. **2024-2026 是 v2 的"学术爆发"周期**：从 Beta（2024）到 Final Adoption（2025-07）再到工业落地（2026），文献数量急剧上升，"高质量学术 + 政策文档"合并约 **60+ 篇**。
2. **形式化与代码生成形成一条主链**：FBK SAWS² → Imandra → HAMR (Sireum) → Galois HARDENS → DARPA PROVERS，是端到端"v2 → 形式证明 → 内存安全代码 → 微内核部署"路径。
3. **LLM × SysML v2 已是 2025 最热子方向**：≥ 10 篇高质量论文（SysTemp、SysMBench、Wang Internetware、Rafique IS2025、Johnson IS2025、arXiv 2507.06803、arXiv 2508.16181、IACIS 2025、Karagoz Sysengr 2026、IS 2026 #137）。中国 BUAA 与欧美工业咨询机构是双引擎。
4. **政府蓝图**：美国 DoD CTO + DARPA PROVERS + DLR 内部 + Fraunhofer 演示 + ESA Hackathon 表明 v2 已被欧美政府部门写入"国家级数字工程战略"。
5. **PhD 课题刚启动**：Munezero 2025 (ODU) 是首篇专门 v2 PhD；中国博士生窗口期。
6. **专门 SysML v2 的系统综述目前仍稀缺**——本仓库正是填补这一空白。

## 14 缺口（中文综述可作为研究空间指出）

1. **医疗 ISO 14971 + v2** 暂无专门论文（仅 v1 时代 Malins 2015）。
2. **机器人 ROS2 + v2** 还停留在 v1 (MeROS) 时代。
3. **韩国/日本/印度区域研究**几乎空白。
4. **CNKI 中文期刊上 v2 专论**目前仅个别（Zhang/Du IEEE 2025），中文综述本身就是一项贡献。
5. **博士论文级研究刚启动**：Munezero 2025 (ODU) 是首篇；中国博士生应抓紧。
6. **没有 Coq / Isabelle / Lean 4 deep embedding** 的 v2 形式化（除 chantakan/verified-mbse 是孤本）——详见 [05 §1](05-formal-verification.md#1-chantakanverified-mbse--lean-4-形式化) 与 [09 §C.1](09-gaps-opportunities.md#c1-kerml-在-coq--isabelle--lean-4-中的-deep-embedding)。
7. **Hybrid / Probabilistic 形式化扩展**（PRISM/Storm/dReal）尚无 v2 桥接论文。

## 15 关键索引（供综述脚注使用）

- arXiv 关键预印本检索：<https://arxiv.org/list/cs.SE/2025>
- INCOSE IS 2025 SysML v2 合集：<https://incose.onlinelibrary.wiley.com/journal/23348852>
- OMG SysML v2 官方页：<https://www.omg.org/sysml/sysmlv2/>
- DoD SysML v2 转移文档：<https://www.cto.mil/wp-content/uploads/2025/02/SysML-v2-TransitionApproach-1.3.pdf>
- Sireum HAMR + SysML v2：<https://hamr.sireum.org/>
- DLR STPA 开源库：<https://github.com/DLR-FT/SysMLv2LibrarySTPA>
- SysON (Eclipse 实现)：<https://mbse-syson.org/>
- NEMO/UFES 研究页：<https://nemo.inf.ufes.br/>

## 参考文献

[^omg-final]: OMG. *Final Adoption of SysML v2.0 + KerML 1.0 + API & Services 1.0*. 2025-07. <https://www.omg.org/news/releases/pr2025/07-21-25.htm>

[^almeida-2024-er]: Almeida, J.P.A., Ferreira Pires, L., Guizzardi, G., Wagner, G. *An Analysis of the Semantic Foundation of KerML and SysML v2*. ER 2024 (Springer LNCS). <https://link.springer.com/chapter/10.1007/978-3-031-75872-0_8>

[^almeida-2025-type-level]: Almeida, J.P.A., Ferreira Pires, L., Guizzardi, G. *Towards an Ontology of Type-Level Phenomena for System Modeling*. NEMO/UFES 2025. <https://nemo.inf.ufes.br/wp-content/papercite-data/pdf/towards_an_ontology_of_type_level_phenomena_for_system_modeling_2025.pdf>

[^molnar-2024-models]: Molnár, V., Graics, B. *Towards the Formal Verification of SysML v2 Models*. MODELS 2024. <https://dl.acm.org/doi/10.1145/3652620.3687820>

[^fbk-saws2-2025]: Cimatti, A., Tonetta, S. 等. *SAWS² – Safety Analysis, Validation & Verification for SysML v2*. FBK 工具发布 2025. <https://fm.fbk.eu/tools/saws2-safety-analysis-validation-verification-for-sysml-v2/>

[^forsching-ltl-2025]: Forschung im Ingenieurwesen. *Enhancing Model-Based Development with Formalized Requirements*. 2025. <https://link.springer.com/article/10.1007/s10010-025-00806-1>

[^imandra-2024]: Smith, J. 等. *Automated Reasoning for SysML v2 (Parts 1-3)*. Imandra Inc. 博客 2024-25. <https://medium.com/imandra/automated-reasoning-for-sysml-v2-ad7e87addba8>

[^opencaesar-onto-2024]: openCAESAR Team. *SysML v2 Ontology in OWL2-DL*. JPL 2024-25. <https://www.opencaesar.io/projects/2023-8-11-SysML-v2-Ontology.html>

[^harder-2025-tum]: Harder. *Interpreting SysML Diagrams to OWL Ontologies for Engineering Knowledge Reuse*. TUM 硕士论文 2025. <https://mediatum.ub.tum.de/doc/1781831/esg7urklq8udb8td3krvee0pt.2025_harder.pdf>

[^metamodel-2025]: *Ensuring Semantic Consistency in SysML v2 Models Through Metamodel-Driven Validation*. ResearchGate 2025. <https://www.researchgate.net/publication/393598625>

[^litwin-2024]: Litwin, K., Amundson, I., Verma, D., McDermott, T. *Transforming AADL Models Into SysML 2.0*. SAE AeroTech 2024. <https://loonwerks.com/publications/pdf/litwin2024aerotech.pdf>

[^aadl-sigada-2023]: *AADL modelling with SysML v2*. ACM SIGAda Ada Letters 2023. <https://dl.acm.org/doi/10.1145/3631483.3631486>

[^repo-aadl-release-2]: *Systems-Modeling/SysML-v2-AADL-Release*. <https://github.com/Systems-Modeling/SysML-v2-AADL-Release>

[^hatcliff-fmics-2025]: Hatcliff, J., Belt, J., Robby, McKenzie, C., Liang, C. *End-to-End Formal Methods Integrated Development with SysMLv2 Using HAMR*. FMICS 2025, LNCS 16028. <https://link.springer.com/chapter/10.1007/978-3-032-00942-5_13>

[^hardin-2025-dasc]: Hardin, D.S., Slind, K. 等. *Automated SysML v2 System Model to Memory-Safe Language Code Generation for Avionics Applications*. DASC 2025 / HCSS 2025. <https://loonwerks.com/publications/pdf/hardin2025dasc.pdf>

[^zimmermann-2024-cosim]: *SysML v2 for Automated Co-simulation from Systems Architecture Models*. Springer LNCS 2024. <https://link.springer.com/chapter/10.1007/978-3-031-62554-1_4>

[^pepper-2024-incose]: *Bidirectional SysML v2 ↔ Modelica Transformation*. INCOSE IS 2024. <https://incose.onlinelibrary.wiley.com/doi/abs/10.1002/iis2.13239>

[^haugen-2025-dartwin]: Haugen, Ø. 等. *DarTwin made precise by SysML v2 — An Experiment*. arXiv:2510.12478. <https://arxiv.org/abs/2510.12478>

[^bonnet-arcadia-2024]: Bonnet, S. 等. *Integrating Arcadia and Capella with SysML v2*. INCOSE IS 2024. <https://www.incose.org/wp-content/uploads/legacy/presentation/is2024-pdf/presentation_173.pdf>

[^jku-iiia-2024]: JKU Linz Team. *Towards Interoperable Digital Twins: Integrating SysML into AAS with Higher-Order Transformations*. JKU 2024. <https://epub.jku.at/obvulioa/download/pdf/9792231>

[^cii-aas-2025]: *From Engineering Models to Digital Twins: Generating AAS from SysML v2 Models*. Computers in Industry 2025. <https://www.sciencedirect.com/science/article/abs/pii/S0164121225003577>

[^zehetner-sosym-2025]: Zehetner, J., Engel, T., Hinkelmann, K. *Digital Twin and the Asset Administration Shell*. Software and Systems Modeling 2025. <https://link.springer.com/article/10.1007/s10270-024-01255-0>

[^detect-syside-2025]: Sensmetry / DoD. *DETECT Tool Migration from SysML v1 to SysML v2 with Syside*. 2025. <https://sensmetry.com/sysml-v1-to-sysml-v2-migration-of-detect-benefits-lessons-learned/>

[^zhang-cnki-v1tov2-2025]: Zhang, Y., Du, H. 等. *Research on Model Conversion from SysML v1 to SysML v2*. IEEE Xplore 2025 (id 11149189). <https://ieeexplore.ieee.org/abstract/document/11149189/>

[^zhou-mds2024]: Zhou, C., An, B., Yu, B., Li, S. *Data Exchange for SysML: A Review*. Springer LNCS MDS 2024. <https://link.springer.com/chapter/10.1007/978-981-97-7887-4_73>

[^bouamra-2025-systemp]: Bouamra, Y. 等. *SysTemp: A Multi-Agent System for Template-Based Generation of SysML v2*. arXiv:2506.21608. <https://arxiv.org/abs/2506.21608>

[^ci-rag-2025]: *An Agent-Based Approach for the Automatic Generation of Valid SysMLv2 Models in Industrial Contexts*. Computers in Industry 2025. <https://dl.acm.org/doi/10.1016/j.compind.2025.104350>

[^jin-2025-sysmbench]: Jin, D., Jin, Z. 等. *SysMBench: A System Model Generation Benchmark from Natural Language Requirements*. arXiv:2508.03215. <https://arxiv.org/abs/2508.03215>

[^wang-2025-internetware]: Wang, Y., Ge, N., Liu, J., Cao, Z., Chen, Z., Hu, C. *Generating SysML Behavior Models via Large Language Models: An Empirical Study*. Internetware 2025. <https://dl.acm.org/doi/10.1145/3755881.3755926>

[^arxiv-llm-semantic-2025]: *LLM-Assisted Semantic Alignment and Integration in Collaborative MBSE Using SysML v2*. arXiv:2508.16181. <https://arxiv.org/abs/2508.16181>

[^rafique-incose-2025]: Rafique, F. 等. *Enhancing Model-Based Systems Engineering with Large Language Models*. INCOSE IS 2025. <https://incose.onlinelibrary.wiley.com/doi/10.1002/iis2.70067>

[^johnson-incose-2025]: Johnson, K., Williams, R. *Automated Legacy Documentation to SysML Conversion*. INCOSE IS 2025. <https://incose.onlinelibrary.wiley.com/doi/abs/10.1002/iis2.70064>

[^arxiv-text-to-model-2025]: *Text to Model via SysML*. arXiv:2507.06803. <https://arxiv.org/abs/2507.06803>

[^incose-paper-137]: INCOSE IS 2026 Paper #137. *Using LLMs to Convert Documentation to SysML*. <https://www.incose.org/wp-content/uploads/2026/01/Paper-137.pdf>

[^karagoz-sysengr-2026]: Karagoz, S. 等. *Identification of Missing Knowledge in MBSE System Models Using Graph-Based Machine Learning*. Wiley Systems Engineering 29 (2026). <https://incose.onlinelibrary.wiley.com/doi/10.1002/sys.70013>

[^iacis-iis-2025]: IACIS Author Team. *Leveraging AI-Driven Requirements for SysML Modeling of Cybersecurity*. IIS 2025. <https://iacis.org/iis/2025/1_iis_2025_46-62.pdf>

[^ieee-genai-spacecraft-2025]: *Assessment of Large Language Models for Use in Generative Design of MBSE Spacecraft System Architectures*. Journal of Engineering Design 36 (2025). <https://www.tandfonline.com/doi/full/10.1080/09544828.2025.2453401>

[^nps-dair-2024]: NPS DAIR. *Leveraging Generative AI to Build, Modify, and Query MBSE Models*. SYM-AM-24-138. <https://dair.nps.edu/bitstream/123456789/5237/1/SYM-AM-24-138.pdf>

[^ahlbrecht-2024-dasc]: Ahlbrecht, A. 等. *Exploring SysML v2 for Avionics*. DASC 2024. <https://elib.dlr.de/207398/1/DASC_Manuscript_Ahlbrecht_24_Final.pdf>

[^ahlbrecht-stpa-sysengr-2026]: Ahlbrecht, A. 等. *Extending SysML v2 for Safety – Open-Source Library for STPA*. Wiley Systems Engineering 2026. <https://incose.onlinelibrary.wiley.com/doi/10.1002/sys.70057>

[^hintze-sysml4sec-2025]: Hintze, H. 等. *SysML4Sec – Methodology for Security Modeling*. INCOSE IS 2025. <https://incose.onlinelibrary.wiley.com/doi/10.1002/iis2.70108>

[^sae-airworthiness-2025]: *Enhancing Airworthiness Security: SysML-Based Approach*. SAE 2025-01-0172. <https://www.sae.org/publications/technical-papers/content/2025-01-0172/>

[^kausch-aviose26]: Kausch, H., Pfeiffer, M., Raco, D., Rumpe, B. 等. *Towards Verifiability-Aware Variability Modeling using SysML v2*. AvioSE'26. <https://publications.rwth-aachen.de/record/1030294>

[^vincent-2024-zero-emission]: Vincent, A. 等. *Applied Model-Based Co-Development for Zero-Emission Flight Systems Based on SysML*. DLRK 2024. <https://www.se-rwth.de/publications/Applied-Model-Based-Co-Development-for-Zero-Emission-Flight-Systems-Based-on-SysML.pdf>

[^granrath-2025-logical-arch]: Granrath, C. *Generating Logical Architectures from SysML Behavior Models*. Wiley Systems Engineering 2025. <https://incose.onlinelibrary.wiley.com/doi/10.1002/sys.70005>

[^automotive-mbse-explained-2025]: *SysML v2: The Next Frontier in Automotive System Modeling*. MBSE Explained 行业白皮书 2025. <https://mbseexplained.com/blog/sysml-v2-next-frontier-automotive-system-modeling/>

[^zhang-2026-uncertainty]: Zhang, M., Li, Y., Yue, T. *Uncertainty Modeling for SysML v2*. arXiv:2602.21641. <https://arxiv.org/abs/2602.21641>

[^ansys-models-2024]: Ansys. *SysML v2 Modeler and Digital Engineering Methodology*. MODELS 2024 Industry Day. <https://conf.researchr.org/details/models-2024/models-2024-industry-day/2/Ansys-SysML-v2-Modeler-and-Digital-Engineering-Methodology>

[^vendor-vp]: Visual Paradigm. *SysML v2 Studio*. <https://updates.visual-paradigm.com/releases/sysml-v2-studio-competitive-advantages-launch/>

[^duroy-2025-esa]: Duroy, R. 等. *Paving the Way for SysML v2: An ESA MBSE Methodology Implementation Review*. Wiley Systems Engineering 2025. <https://incose.onlinelibrary.wiley.com/doi/10.1002/sys.70046>

[^gpdis-2024]: GM. *MBSE Collaboration with SysML 2.0*. GPDIS 2024. <https://gpdisonline.com/wp-content/uploads/2024/10/ADPAG-KyleHall-ADPAGMBSE-MBSE-Open1.pdf>

[^boelsen-mech-2025]: Boelsen, K., May, M., Jacobs, G. 等. *SysML v2 based Modelling Guidelines for Mechanical System Elements*. Forschung im Ingenieurwesen 2025. <https://link.springer.com/article/10.1007/s10010-025-00827-w>

[^mbse2025-hackathon]: Starion / ESA / Sensmetry / Obeo / Ansys / Dassault. *MBSE 2025 SysML v2 Hackathon Report — COMET Interceptor*. <https://www.stariongroup.eu/sysml-v2-hackathon-the-value-of-collaborative-model-extension-development/>

[^steiner-omg-cert-2025]: Steiner, R. 等. *OMG's Approach to Developing its SysMLv2 Certification Program*. INCOSE IS 2025. <https://incose.onlinelibrary.wiley.com/doi/10.1002/iis2.70071>

[^lange-multi-incose-2025]: Lange, A., Cederbladh, J., Feichtinger, M., Weber, M. *An Initial Exploration of MULTI Level Modeling for MBSE*. INCOSE IS 2025. <https://incose.onlinelibrary.wiley.com/doi/10.1002/iis2.70080>

[^aleksandraviciene-magicgrid-2025]: Aleksandraviciene, A. *Exploring the Use of SysMLv2 for Solution Architecture Development with the MagicGrid Framework*. INCOSE IS 2025. <https://incose.onlinelibrary.wiley.com/doi/10.1002/iis2.70000>

[^teodorov-2025-blueprint]: Teodorov, C. 等. *A Research Agenda for the Living SysML V2 Blueprint*. SBMF 2025. <https://link.springer.com/chapter/10.1007/978-3-032-12086-1_4>

[^debbiche-isse-2024]: Debbiche, J. 等. *Transitioning towards SysML v2 as a Variability Modeling Language*. ISSE 2024. <https://link.springer.com/article/10.1007/s11334-024-00569-y>

[^weilkiens-mbple-2025]: Weilkiens, T. *Next Generation MBPLE with SysML v2*. INCOSE IS 2025. <https://incose.onlinelibrary.wiley.com/doi/10.1002/iis2.70058>

[^mbse-bibliometric-2026]: *Mapping the Landscape of MBSE through Bibliometric and Network Analysis*. ScienceDirect 2026. <https://www.sciencedirect.com/science/article/pii/S2950550X26000014>

[^zhang-process-chains-2025]: Zhang, X. 等. *SysML Process Chains in MBSE: Systematic Literature Review*. ScienceDirect 2025. <https://www.sciencedirect.com/science/article/pii/S2950550X25000032>

[^mathworks-2026-mbse-seams]: MathWorks. *Why MBSE Still Breaks at the Seams and How SysML v2 Could Help*. 2026-04-30. <https://blogs.mathworks.com/digitaleng/2026/04/30/why-mbse-still-breaks-at-the-seams-and-how-sysml-v2-could-help/>

[^munezero-thesis-2025]: Munezero, P. *A SysML v2 Implementation of a Traceability and Verification Metamodel for "-Ilities"*. Old Dominion University PhD Thesis 2025. <https://digitalcommons.odu.edu/emse_etds/244/>

[^dod-cto-2024]: U.S. OUSD(R&E). *SysML v1 to SysML v2 Model Conversion Approach v1.3*. 2024-03. <https://www.cto.mil/wp-content/uploads/2025/02/SysML-v2-TransitionApproach-1.3.pdf>

[^dod-sysml-info-sheet-2025]: US DoD. *SysML v2 Technical Highlight Info Sheet*. 2025-08. <https://www.cto.mil/wp-content/uploads/2025/08/SysML-Info_Sheet_08_04_2025-v4.pdf>

[^darpa-provers-2025]: DARPA. *PROVERS Program Description*. 2025-04. <https://www.darpa.mil/sites/default/files/attachment/2025-04/darpa-portfolio-revolutionizing-cyber-resiliency-military-systems.pdf>

[^dlr-git-mbse-2025]: Frahm, M. 等. *Versionskontrolle und Kollaboration in MBSE: Untersuchung der Git-Integration mit SysML v2*. DLR Tech Report 2025. <https://elib.dlr.de/217321/>

[^fraunhofer-interop-live-2025]: Fraunhofer IPK. *Interoperability Live - SysML v2 API in Action*. 2025. <https://publica.fraunhofer.de/entities/publication/adaf3473-1ccd-475f-a2da-02077ec3aa3a>

[^repo-monticore-3]: *MontiCore/sysmlv2*. <https://github.com/MontiCore/sysmlv2>

[^repo-sysmline-2]: *Ruizhe-Yang/SysMLine* + 同作者多个仓库（SysMini / SysMLOC / CODES）。<https://github.com/Ruizhe-Yang/SysMLine>

[^repo-sysmini-2]: *Ruizhe-Yang/SysMini*. <https://github.com/Ruizhe-Yang/SysMini>

[^repo-sysmloc-2]: *Ruizhe-Yang/SysMLOC*. <https://github.com/Ruizhe-Yang/SysMLOC>

[^repo-codes-2]: *Ruizhe-Yang/CODES*. <https://github.com/Ruizhe-Yang/CODES>

[^sensmetry-advent]: Sensmetry. *Advent of SysML v2 — 25 Lesson Series*. <https://sensmetry.com/advent-of-sysml-v2/>
