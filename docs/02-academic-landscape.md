# 02 学术地图

## 本章简介

本章按主题梳理 SysML v2 / KerML 的学术研究现状，覆盖语义批评与形式化、模型转换与桥接、LLM × SysML v2、工业 / 航天案例四大方向；列出 9 个主要研究团队及其代表性产出；并以一个篇末小节诚实地标记 "已发文但代码未公开" 的论文，便于读者评估实际可获得的工具。

## 1 主题分类与代表论文

### 1.1 语义批评与形式化

SysML v2 设计的最大野心是"用 KerML 提供干净的核心语义、由形式化方法保护"，但截至 2026-05，学术批评与正面建构两端都尚处早期。

最具引用势能的工作来自 NEMO/UFES（Almeida、Guizzardi）与 U. Twente（Ferreira Pires）的合作[^almeida-2024]。该论文以 UFO（Unified Foundational Ontology）4D 时空语义为参照，系统比对 KerML 的 Feature/Specialization/Subsetting 语义与本体论意义上的"分类"概念，指出多处不对齐（例如 KerML 没有显式区分 *kind* / *role* / *phase*）。论文是**纯描述性**的——批评结论清晰但**没有发布形式化工具或转换器**。

形式化验证方向最直接的工作是 BME + FBK 的 Molnár & Graics MODELS 2024 论文[^molnar-2024]，他们提出 SysML v2 状态机 → Gamma 框架 → UPPAAL/Theta/Spin/nuXmv 的转换流水线。**关键限制**：截至 2026-05，转换器代码**没有合并到 ftsrg/gamma 主仓**[^repo-gamma]，伴随仓库 [ftsrg/isse-formal-methods-sysmlv2][^repo-isse-fm] 仅含案例模型不含 transformer 源码——这是论文 → 工具的最近一步，但代码在哪需要联系作者。

Teodorov 等的 SBMF 2025 论文[^teodorov-2025]提出 *Living SysML V2 Blueprint* 路线图（统一执行 / 多宇宙状态探索 / 原生形式化验证的"SysML v2 虚拟机"），但 **目前仅为 research agenda 论文，无代码**。

辅助工作还包括：基于元模型驱动的 SysML v2 一致性校验[^metamodel-2025]（提供工业流水线可用的约束集合）。

### 1.2 与 AADL / Modelica / 仿真域的桥接

AADL 方向的开源工作最成熟。Litwin / Amundson / Verma / McDermott（Collins + SEI + Stevens）的 SAE AeroTech 2024 论文[^litwin-2024]给出 AADL → SysML v2 的转换规则与 case study，并配套 Loonwerks 团队公开的论文 PDF；但**转换器代码本身尚未公开**——目前可用的相关 OSS 仅 [Systems-Modeling/SysML-v2-AADL-Release][^repo-aadl-release]（一组 SysML v2 写出的 AADL 概念 library，**不是转换器**）与 [sireum/hamr-sysml-parser][^repo-hamr-parser]（ANTLR 包装）。Sireum HAMR 走的是另一条路：把 SysML v2 子集映射到与 AADL 共享的 IR，再由 HAMR 既有的 codegen 输出 Slang/JVM/C/seL4 microkit。Hardin 等 DASC 2025 论文[^hardin-2025]验证了 Rust + microkit 路径，[santoslab/sysmlv2-models][^repo-santoslab] 是真案例 + 4 平台 GH Actions（Linux/macOS/Windows/CAmkES Docker）。

Modelica / 仿真桥接相对薄弱：

- Springer LNCS 2024 *SysML v2 for Automated Co-simulation*[^zimmermann-2024]提出基于 v2 API + FMI 的自动 co-sim 网络，**无开源代码**。
- INCOSE IS 2024 双向转换论文[^pepper-2024]同样未释出代码。
- 唯一已开源的相关项目是 2014 年的 [Gatech SysML Modelica Integration][^repo-gatech-modelica]——但仅针对 SysML **v1** 与 MagicDraw，与 v2 基本无关。

DLR 团队 Ahlbrecht 等 DASC 2024 论文[^ahlbrecht-2024]在民航航电领域做了 SysML v2 应用研究，但属于 case study 而非工具。

数字孪生方向：Haugen 等 *DarTwin made precise by SysML v2*[^haugen-2025]把 Digital Twin DSL 用 v2 语言扩展机制重新形式化，发现 Pilot 实现图形化短板。同样无开源工具。

### 1.3 LLM × SysML v2

这是 2024–2026 最热的子方向，但论文集中度仍低、训练语料稀缺、最佳基线 BLEU 个位数。

代表性工作按时间顺序：

- **SysTemp**（Bouamra 等，arXiv 2506.21608）[^bouamra-2025]：多 Agent 流水线（TemplateGenerator + Parser + Writer），自然语言 → SysML v2 句法正确率 ~80%。Lyon 1 / LIRIS 团队。
- **Computers in Industry 2025**[^ci-rag-2025]：RAG + ANTLR 校验闭环，工业方向；号称 100% 句法合法。
- **SysMBench**（Jin Zhi 等 PKU，arXiv 2508.03215）[^jin-2025]：151 个人工标注场景，17 个 LLM 评测；最高 BLEU 仅 4%、SysMEval-F1 62%。这是**首个公开 NL → system model 基准**。**注意**：该工作主导单位是北京大学，作者均署名北大、华中、华东师大、北京控制工程研究所，**无北航**——见 [03-北航专项](03-beihang-investigation.md) §3 的勘误。
- **Internetware 2025**（Wang / Ge / Hu 等北航）[^wang-2025]：107 个 SysML 行为模型数据集，17 个 LLM 评测幻觉与生成质量；这是本土 LLM × SysML 实证研究的代表作，作者全部署名 *School of Software, Beihang University*。
- **INCOSE IS 2026 #137**[^incose-137]：从工程文档抽取 v2 模型。
- **Text-to-Model via SysML**（arXiv 2507.06803）[^text-to-model-2025]：NL → 动力系统计算模型。
- **NPS DAIR 2024**[^nps-dair-2024]：美国国防分析的方向论文 *Leveraging Generative AI to Build, Modify, and Query MBSE Models*。

整体判断：**LLM × SysML v2 仍是开放问题**，存在大量空白机会，详见 [09-缺口与机会](09-gaps-opportunities.md) §A.4。

### 1.4 工业 / 航天 / 汽车案例

公开案例研究截至 2026-05 集中在五处：

- **Airbus**：Apollo 11 SoS 模型完整开源[^repo-airbus]，是目前最大的 v2 公开模型；Airbus Central R&T 同时是 OMG SysML v2 SST 主要贡献方。
- **ESA**：Duroy 等 *Paving the Way for SysML v2: An ESA MBSE Methodology Implementation Review*[^duroy-2025]发表于 *Systems Engineering* 2025。
- **DoD / OUSD R&E**：*SysML v1 to SysML v2 Model Conversion Approach* v1.3[^dod-cto]，2024-03 技术报告，是大型武器系统转换的官方蓝图。
- **GM / GPDIS**：2024 年报告 *MBSE Collaboration with SysML 2.0: A Pre-Release Use*[^gpdis-2024]。
- **NASA / JPL**：通过 [Open-MBEE/SysML-v2-Applications-and-Examples][^repo-open-mbee-examples] 与 [opencaesar/oml + SysML v2 Ontology Project][^opencaesar-sysmlv2] 持续投入；公开论文较少，但 SST 中 JPL（Sandy Friedenthal、Ed Seidewitz 等长期参与）持续推进。

商用厂商演示性的 case study 仍保留私有版权——Ansys MODELS 2024 Industry Day 演示[^ansys-models-2024]、Visual Paradigm SysML v2 Studio[^vendor-vp]发布会等属此类。

## 2 主要研究团队

| 团队 / 实验室 | 研究方向 | 代表产出 |
|---|---|---|
| **NEMO @ UFES + U. Twente** | UFO 本体论视角的 KerML 语义 | Almeida 等 ER 2024[^almeida-2024] |
| **FBK Trento + BME 布达佩斯** | SysML v2 → Gamma 形式化验证 | Molnár & Graics MODELS 2024[^molnar-2024] |
| **CMU SEI + Collins Aerospace + Stevens** | AADL ↔ SysML v2、安全关键认证扩展 | Litwin 等 SAE AeroTech 2024[^litwin-2024]；SEI 2023 年报[^sei-2023] |
| **DLR（德国宇航中心）** | 民航航电 SysML v2 应用 | Ahlbrecht 等 DASC 2024[^ahlbrecht-2024] |
| **ENSTA Bretagne / Lab-STICC** | 执行语义 / Living Blueprint 议程 | Teodorov 等 SBMF 2025[^teodorov-2025] |
| **Université Lyon 1 / LIRIS** | LLM 多 Agent 生成 | Bouamra 等 SysTemp 2025[^bouamra-2025] |
| **TU Berlin MBSE 组 + Fraunhofer** | 教学与工业实践 | TU Berlin MBSE 主页与 Fraunhofer publica |
| **Antwerp / NTNU / Linz**（Klikovits、Denil、Bordeleau） | Digital Twin / MDE | Haugen 等 *DarTwin* arXiv 2510.12478[^haugen-2025] |
| **Sireum / Kansas State / Loonwerks** | 高保障代码生成 | HAMR + GUMBO + Logika; Hardin 等 DASC 2025[^hardin-2025] |

中国语境下，**北京航空航天大学**（岳涛、吴际、葛宁 / 胡春明）是国内研究存在感最强的单位；详见 [03-北航专项](03-beihang-investigation.md)。**北京大学**（金芝团队）以 SysMBench 基准[^jin-2025]切入 LLM × SysML v2。**大连理工大学**（Ruizhe Yang）以 [SysMLine][^repo-sysmline] 投入开源工程。

## 3 论文 vs 代码：诚实标注

学术声称与可获得工具之间的 gap 是综述中最容易被掩盖的一点。本仓库依据 cite key 一一核查，结果：

| 论文 | 代码状态 | 说明 |
|---|---|---|
| Almeida 2024 ER | **无代码** | 描述性语义分析；批评结论清晰，但无 SysML v2 → gUFO/OntoUML 校验器 |
| Molnár 2024 MODELS | **代码未合并主仓** | ftsrg/gamma 主仓 grep `sysml` 仅命中 PlantUML transformer；伴随仓库 ftsrg/isse-formal-methods-sysmlv2 仅含案例模型 |
| Teodorov 2025 SBMF | **零代码** | research agenda paper |
| Litwin 2024 AeroTech | **无 transformer 代码** | 仅论文；可参考 SysML-v2-AADL-Release library 与 sireum/hamr-sysml |
| Zimmermann 2024 LNCS | **无代码** | Co-simulation 论文 |
| Pepper 2024 INCOSE | **无代码** | 双向转换论文 |
| Ahlbrecht 2024 DASC | **无代码** | DLR case study |
| Haugen 2025 arXiv | **无代码** | DarTwin × v2 实验 |
| Bouamra 2025 SysTemp | **代码状态未公开** | 多 Agent 系统，工业评估方法论 |
| Jin 2025 SysMBench | **基准已公开** | 这是为数不多代码 + 数据齐全的 LLM 工作 |
| Wang 2025 Internetware | **数据集状态待查** | Beihang 实证研究；论文已在 ACM DL |
| Hardin 2025 DASC | **代码已公开** | sireum/hamr-sysml + santoslab/sysmlv2-models 4 平台 CI |

可用工具的实情见 [05-形式化与验证](05-formal-verification.md) 与 [07-代码生成与执行](07-codegen-execution.md)。

## 4 中长期学术机会窗口

1. **KerML 在 Coq / Isabelle / Lean 4 中的 deep embedding**：AADL 已有 Oqarina（Coq）与 HAMR-Isabelle 形式化；UML 有 HOL-OCL；SysML v2 完全空白。chantakan/verified-mbse[^repo-verified-mbse]是 Lean 4 shallow embedding 的孤本（2★、单作者）。
2. **SMT-based 部件实例化可满足性检查**：当前唯一的开源约束求解是 SysMD 的 AADD（区间 + LP），不解一阶逻辑。
3. **v2 → LTL/CTL 性质规约 DSL**：Gamma 的状态机 → UPPAAL/Theta 后端可用，但前端缺失。
4. **领域规则集**：ISO 26262 ASIL、DO-178C objective、ARP4754A、NASA-STD-7150 等的 SysML v2 OSS 实现。
5. **Hybrid / Probabilistic 验证桥**：PRISM / Storm / dReal / SpaceEx 与 SysML v2 仍未连接。

详见 [09-缺口与机会](09-gaps-opportunities.md)。

## 参考文献

[^almeida-2024]: Almeida, J.P.A., Ferreira Pires, L., Guizzardi, G., & Wagner, G. *An Analysis of the Semantic Foundation of KerML and SysML v2*. ER 2024 (Springer LNCS). <https://link.springer.com/chapter/10.1007/978-3-031-75872-0_8>

[^molnar-2024]: Molnár, V., & Graics, B. *Towards the Formal Verification of SysML v2 Models*. ACM/IEEE MODELS 2024. <https://dl.acm.org/doi/10.1145/3652620.3687820>

[^teodorov-2025]: Teodorov, C. 等. *A Research Agenda for the Living SysML V2 Blueprint*. SBMF 2025. <https://link.springer.com/chapter/10.1007/978-3-032-12086-1_4>

[^metamodel-2025]: *Ensuring Semantic Consistency in SysML v2 Models Through Metamodel-Driven Validation*. 2025. <https://www.researchgate.net/publication/393598625>

[^litwin-2024]: Litwin, K., Amundson, I., Verma, D., & McDermott, T. *Transforming AADL Models Into SysML 2.0*. SAE AeroTech 2024. <https://loonwerks.com/publications/pdf/litwin2024aerotech.pdf>

[^hardin-2025]: Hardin, D. 等. *Trustworthy Systems Engineering with SysML v2, AADL, and HAMR on seL4 microkit*. DASC 2025. <https://loonwerks.com/publications/pdf/hardin2025dasc.pdf>

[^zimmermann-2024]: *SysML v2 for Automated Co-simulation from Systems Architecture Models*. Springer LNCS, 2024. <https://link.springer.com/chapter/10.1007/978-3-031-62554-1_4>

[^pepper-2024]: *Bidirectional SysML v2 ↔ Modelica Transformation*. INCOSE IS 2024. <https://incose.onlinelibrary.wiley.com/doi/abs/10.1002/iis2.13239>

[^ahlbrecht-2024]: Ahlbrecht, A. 等. *Exploring SysML v2 for Avionics*. DASC 2024. <https://elib.dlr.de/207398/1/DASC_Manuscript_Ahlbrecht_24_Final.pdf>

[^haugen-2025]: Haugen, Ø. 等. *DarTwin made precise by SysML v2 — An Experiment*. arXiv:2510.12478. <https://arxiv.org/abs/2510.12478>

[^bouamra-2025]: Bouamra, Y., Yun, B., Poisson, A., & Armetta, F. *SysTemp: A Multi-Agent System for Template-Based Generation of SysML v2*. arXiv:2506.21608. <https://arxiv.org/abs/2506.21608>

[^ci-rag-2025]: *An Agent-Based Approach for the Automatic Generation of Valid SysMLv2 Models in Industrial Contexts*. Computers in Industry, 2025. <https://dl.acm.org/doi/10.1016/j.compind.2025.104350>

[^jin-2025]: Jin, D., Jin, Z., Li, L., Fang, Z., Li, J., & Chen, X. *SysMBench*. arXiv:2508.03215. <https://arxiv.org/abs/2508.03215>

[^wang-2025]: Wang, Y., Ge, N., Liu, J., Cao, Z., Chen, Z., & Hu, C. *Generating SysML Behavior Models via LLMs: An Empirical Study*. Internetware 2025. <https://dl.acm.org/doi/10.1145/3755881.3755926>

[^incose-137]: INCOSE. *Using LLMs to Convert Documentation to SysML*. INCOSE IS 2026, Paper #137. <https://www.incose.org/wp-content/uploads/2026/01/Paper-137.pdf>

[^text-to-model-2025]: *Text-to-Model via SysML*. arXiv:2507.06803. <https://arxiv.org/abs/2507.06803>

[^nps-dair-2024]: NPS DAIR. *Leveraging Generative AI to Build, Modify, and Query MBSE Models*. SYM-AM-24-138. <https://dair.nps.edu/bitstream/123456789/5237/1/SYM-AM-24-138.pdf>

[^duroy-2025]: Duroy, R. 等. *Paving the Way for SysML v2: An ESA MBSE Methodology Implementation Review*. Systems Engineering, 2025. <https://incose.onlinelibrary.wiley.com/doi/10.1002/sys.70046>

[^dod-cto]: U.S. OUSD(R&E). *SysML v1 to SysML v2 Model Conversion Approach v1.3*. 2024-03. <https://www.cto.mil/wp-content/uploads/2025/02/SysML-v2-TransitionApproach-1.3.pdf>

[^gpdis-2024]: GM. *MBSE Collaboration with SysML 2.0*. GPDIS 2024. <https://gpdisonline.com/wp-content/uploads/2024/10/ADPAG-KyleHall-ADPAGMBSE-MBSE-Open1.pdf>

[^ansys-models-2024]: Ansys. *SysML v2 Modeler and Digital Engineering Methodology*. MODELS 2024 Industry Day. <https://conf.researchr.org/details/models-2024/models-2024-industry-day/2/Ansys-SysML-v2-Modeler-and-Digital-Engineering-Methodology>

[^vendor-vp]: Visual Paradigm. *SysML v2 Studio*. <https://updates.visual-paradigm.com/releases/sysml-v2-studio-competitive-advantages-launch/>

[^sei-2023]: CMU SEI. *Extending SysML v2 with AADL Concepts*. SEI 2023 Year in Review. <https://www.sei.cmu.edu/annual-reviews/2023-year-in-review/extending-sysml-v2-with-aadl-concepts-to-support-engineering-and-certification-of-safety-critical-systems/>

[^repo-aadl-release]: *Systems-Modeling/SysML-v2-AADL-Release*. <https://github.com/Systems-Modeling/SysML-v2-AADL-Release>

[^repo-hamr-parser]: *sireum/hamr-sysml-parser*. <https://github.com/sireum/hamr-sysml-parser>

[^repo-santoslab]: *santoslab/sysmlv2-models*. <https://github.com/santoslab/sysmlv2-models>

[^repo-airbus]: *airbus/apollo-11-sysml-v2*. <https://github.com/airbus/apollo-11-sysml-v2>

[^repo-gamma]: *ftsrg/gamma*. <https://github.com/ftsrg/gamma>

[^repo-isse-fm]: *ftsrg/isse-formal-methods-sysmlv2*. <https://github.com/ftsrg/isse-formal-methods-sysmlv2>

[^repo-gatech-modelica]: *SysMLModelicaIntegration/edu.gatech.mbse.mdsysmlmodelica*（v1，2014 遗留）。<https://github.com/SysMLModelicaIntegration/edu.gatech.mbse.mdsysmlmodelica>

[^repo-open-mbee-examples]: *Open-MBEE/SysML-v2-Applications-and-Examples*. <https://github.com/Open-MBEE/SysML-v2-Applications-and-Examples>

[^opencaesar-sysmlv2]: openCAESAR. *SysML v2 Ontology Project*. <https://www.opencaesar.io/projects/2023-8-11-SysML-v2-Ontology.html>

[^repo-sysmline]: *Ruizhe-Yang/SysMLine*. <https://github.com/Ruizhe-Yang/SysMLine>

[^repo-verified-mbse]: *chantakan/verified-mbse*. <https://github.com/chantakan/verified-mbse>
