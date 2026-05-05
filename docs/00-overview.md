# 00 总览

## 本章简介

本章给出 SysML v2 / KerML 生态在 2026-05 的全景速览，包括：调研动机与方法学、生态分层（标准 / 实现 / 研究 / 产业 / 社区）、关键时间节点、五维度基础设施速判、以及如何阅读后续章节。希望读者在 10 分钟之内即可建立全局心智地图，再按需选择性深入后续章节。

## 1 调研动机

OMG SysML v2 自 2017 年立项[^seidewitz-2017]、2023 年起进入 Beta 公开评审、2025-06-30 完成 Final Adoption[^omg-final]、2025-09 正式出版以来，已正式进入"工业部署窗口"。但与 1990 年代末 UML、2000 年代初 SysML v1 类似，**语言规范 ready ≠ 工具链 ready**。本仓库的目的在于回答一组工程实务问题：

1. SysML v2 现在能拿来做什么？哪些事情还做不了？
2. 开源生态的厚度比起 UML / SysML v1 / AADL / Modelica 等同类语言达到了几成？
3. 从「学术 → 工业 → 生态」三层视角看，目前最有价值的开源四件套是什么？最大的隐忧是什么？
4. 中文学术圈，特别是北航，参与 SysML v2 标准化与研究的实情如何？
5. 如果要立项做 SysML v2 相关的开源工具或学术工作，最值得抓的机会窗口在哪里？

下文与各章共同回答这五组问题。

## 2 调研方法

本仓库的全部结论由 **5 路并行 AI 调研代理 + 人工审校**产出，时间锚定 2026-05-05。具体作业流程：

- **代理 A（学术地图）**：以 Google Scholar / arXiv / Semantic Scholar / 出版社网站为入口，覆盖 2023–2026 年 MODELS、SBMF、DASC、ER、INCOSE IS、Internetware、Computers in Industry、Systems Engineering 等会议刊物的 SysML v2 相关论文。
- **代理 B（开源生态）**：以 `gh api` 直查 GitHub 仓库元数据（star / 最近 push / license / 主语言 / topic），并对 ~30 个候选仓库进行源码级 walk-through。
- **代理 C（北航专项）**：以中英双语关键词 + 邮箱后缀（`@buaa.edu.cn`）+ OMG 工作组成员名单交叉核验。
- **代理 D（基础设施深挖）**：分四子线分别覆盖解析 / IDE、形式化 / 验证、可视化 / 协作、代码生成 / 执行。每子线对每个候选项目做技术深度阅读（Xtext 文法行数、`@Check` 校验数量、PlantUML Visitor 模式、API 端点矩阵、Lean 4 定理覆盖等具体量化指标）。
- **代理 E（基线对照）**：对 UML / SysML v1 / AADL / Modelica / Capella / BPMN / TLA+ / Alloy 八个建模语言生态做五维度成熟度评分。

所有代理独立工作、互不干扰；最终结果由维护人（[@HansBug](https://github.com/HansBug)）合并、勘误、统一引用规范。引用规范见 [AGENTS.md §3](../AGENTS.md)。

## 3 生态分层

整体生态可分为五层：

| 层级 | 内容 | 关键产物 |
|---|---|---|
| **标准层** | OMG 规范、KerML 元模型、API 规范、KEBNF 语法 | 见 [01-standard-status.md](01-standard-status.md) |
| **参考实现层** | OMG 官方 Pilot Implementation 与配套 API/Cookbook/Java/Python client、AADL Library | 见 [04-parsing-ide-infrastructure.md](04-parsing-ide-infrastructure.md) §1 |
| **第三方实现层** | sensmetry SysIDE、Eclipse SysON、daltskin、elan8 spec42、MontiCore、KerML.NET、Open-MBEE Flexo MMS、HAMR 等 | 见 [04-](04-parsing-ide-infrastructure.md) / [05-](05-formal-verification.md) / [06-](06-visualization-collaboration.md) / [07-](07-codegen-execution.md) |
| **学术研究层** | 语义批评、形式化、桥接、LLM × SysML v2、工业 case study | 见 [02-academic-landscape.md](02-academic-landscape.md) |
| **产业商用层** | Cameo / CATIA No Magic、IBM Rhapsody SE、Siemens System Modeler / Capital、PTC Windchill Modeler、Ansys SAM、Visual Paradigm | 见 [README.md](../README.md#商用厂商支持速览) |

## 4 关键时间节点

| 年份 | 事件 |
|---|---|
| 2017 | OMG SysML v2 RFP 发布；提交团队（SST）成立 |
| 2018–2022 | KerML 元模型与 SysML v2 抽象语法、文本表示与图形表示设计 |
| 2023 | Beta1 公开评审；商用厂商（PTC Windchill Modeler 10 等）开始首阶段集成[^vendor-ptc] |
| 2024-09 | Beta2；MODELS 2024 出现首批 v2 形式化论文[^molnar-2024] |
| 2024-10 | Almeida 等 ER 2024 发布 KerML 4D 时空语义批评[^almeida-2024] |
| 2024–2025 | LLM × SysML v2 论文涌现（SysTemp、SysMBench、Internetware 实证）[^bouamra-2025][^jin-2025][^wang-2025] |
| 2025-06-30 | OMG **Final Adoption**[^omg-final] |
| 2025-07-21 | OMG 公开发布公告 |
| 2025-09 | 规范文档正式出版 |
| 2026-01 | Cameo / CATIA No Magic 2026x 上市；价格调涨 ~20%[^vendor-cameo] |
| 2026-02 | Siemens Capital 2512 集成 SysML v2 经 Teamcenter 流转[^vendor-siemens-cap] |
| 2026-02 | 北航三作者 *Uncertainty Modeling for SysML v2* arXiv 上线[^zhang-2026] |
| 2026-04 | Sensmetry 把 SysIDE 闭源升级到商业 Syside Editor[^syside-rebirth] |
| 2026-05 | Eclipse SysON `v2026.3.0` 发布[^repo-syson]；HAMR + santoslab 真案例 4 平台 CI 工作流稳定[^repo-santoslab] |

## 5 五维度速判（详见各章）

| 维度 | 一句话定位 | 详见 |
|---|---|---|
| **解析 / IDE** | 6 套独立 parser、3 个 tree-sitter 语法并存；VS Code 较丰富但 IntelliJ 完全空白；Sensmetry 新版闭源是最大隐忧 | [04-](04-parsing-ide-infrastructure.md) |
| **形式化 / 验证** | 端到端开源唯有 HAMR + GUMBO + Logika（限 v2 ∩ AADL 子集）；Imandra 闭源；Coq/Isabelle deep embedding 完全空白 | [05-](05-formal-verification.md) |
| **可视化 / 协作** | SysON 是唯一接近生产级图形建模器，但 BDD/IBD/Sequence/Use Case 仍不全；API 标准本身没有 merge 端点 | [06-](06-visualization-collaboration.md) |
| **代码生成 / 执行** | 多数转换（Modelica/Simulink）是 Vapor；OWL 转换 Beta；HAMR codegen 是唯一端到端真案例；行为执行几乎无开源 | [07-](07-codegen-execution.md) |
| **包管理 / CI** | sysand（Rust + pubgrub + KPAR）是整套生态最现代的设计选择；GitHub Action 没有 reusable 包，Linter 完全空白 | [04-](04-parsing-ide-infrastructure.md) §3、[07-](07-codegen-execution.md) §3 |

## 6 中国语境

中文圈对 SysML v2 的参与目前主要集中在三处：

1. **北航**：岳涛教授（OMG 标准化 contributor、PSUM 标准 co-chair）、吴际副教授（OMG PSUM 工作组北航代表）、葛宁 / 胡春明团队（LLM × SysML 实证）。详见 [03-beihang-investigation.md](03-beihang-investigation.md)。
2. **北大**：金芝团队 SysMBench 是首个公开的 NL → 系统模型基准[^jin-2025]；勿与北航混淆。
3. **大连理工**：Ruizhe Yang 的 [SysMLine][^repo-sysmline] 是国内唯一已开源的 SysML v2 PoC（5★，学术工程）。

国内**商用 MBSE 厂商**（索为系统、安世亚太、山大华天等）截至 2026-05 **未开源任何 v2 工具**。

## 7 阅读路径建议

- **管理者 / 选型决策者**：仅读本章 + [01-标准状态](01-standard-status.md) + [08-基线对照](08-baseline-comparison.md) + [09-缺口与机会](09-gaps-opportunities.md)。
- **架构师 / 工具链负责人**：补读 [04-解析 IDE](04-parsing-ide-infrastructure.md)、[06-可视化与协作](06-visualization-collaboration.md)、[07-代码生成与执行](07-codegen-execution.md)。
- **形式化工程师 / 学术研究者**：补读 [02-学术地图](02-academic-landscape.md) + [05-形式化与验证](05-formal-verification.md)。
- **中文学术圈合作 / 北航相关方**：先读 [03-北航专项](03-beihang-investigation.md) + [02-学术地图](02-academic-landscape.md)。

## 参考文献

[^omg-final]: OMG. *Final Adoption: SysML v2.0, KerML v1.0, Systems Modeling API & Services v1.0*. 2025-07-21. <https://www.omg.org/news/releases/pr2025/07-21-25.htm>

[^seidewitz-2017]: Seidewitz, E. SysML v2 RFP 提交背景，OMG 历史归档。完整背景见 [omg.org/sysml/sysmlv2](https://www.omg.org/sysml/sysmlv2/)。

[^vendor-ptc]: PTC. *Windchill Modeler 10*. <https://www.ptc.com/en/blogs/alm/introducing-windchill-modeler-10-whats-new-and-noteworthy>

[^molnar-2024]: Molnár, V., & Graics, B. *Towards the Formal Verification of SysML v2 Models*. ACM/IEEE MODELS 2024. <https://dl.acm.org/doi/10.1145/3652620.3687820>

[^almeida-2024]: Almeida, J.P.A. 等. *An Analysis of the Semantic Foundation of KerML and SysML v2*. ER 2024. <https://link.springer.com/chapter/10.1007/978-3-031-75872-0_8>

[^bouamra-2025]: Bouamra, Y. 等. *SysTemp: A Multi-Agent System for Template-Based Generation of SysML v2*. arXiv:2506.21608. <https://arxiv.org/abs/2506.21608>

[^jin-2025]: Jin, D., Jin, Z. 等. *SysMBench: A System Model Generation Benchmark from Natural Language Requirements*. arXiv:2508.03215. <https://arxiv.org/abs/2508.03215>

[^wang-2025]: Wang, Y., Ge, N. 等. *Generating SysML Behavior Models via LLMs: An Empirical Study*. Internetware 2025. <https://dl.acm.org/doi/10.1145/3755881.3755926>

[^vendor-cameo]: Dassault. *CATIA SysML v2 Solution Docs*. <https://docs.nomagic.com/spaces/CATIA/pages/261619716/CATIA+SysML+v2+Solution>

[^vendor-siemens-cap]: Siemens. *Capital 2512 Release Notes*. <https://blogs.sw.siemens.com/ee-systems/2026/02/27/whats-new-in-capital-2512/>

[^zhang-2026]: Zhang, M., Li, Y., & Yue, T. *Uncertainty Modeling for SysML v2*. arXiv:2602.21641. <https://arxiv.org/abs/2602.21641>

[^syside-rebirth]: Sensmetry. *Syside Editor Rebirth*. <https://sensmetry.com/syside-editor-rebirth-sysml-v2-0-50x-speed-up-license-change-free-as-before/>

[^repo-syson]: *eclipse-syson/syson*. <https://github.com/eclipse-syson/syson>

[^repo-santoslab]: *santoslab/sysmlv2-models*. <https://github.com/santoslab/sysmlv2-models>

[^repo-sysmline]: *Ruizhe-Yang/SysMLine*（大连理工 PoC）。<https://github.com/Ruizhe-Yang/SysMLine>
