# 参考文献

本文档汇总本仓库所有调研引用的标准、论文、开源仓库、商用产品与第三方资料。引用键（cite key）形如 `[作者-年份-关键词]`，便于跨文档检索。所有 URL 均在 2026-05-05 由独立调研代理直接访问核对，引用页内容若日后失效请以 [Wayback Machine](https://web.archive.org/) 快照为准。

## 1 标准与规范

- <a id="omg-sysmlv2-final-2025"></a>**[omg-sysmlv2-final-2025]** Object Management Group. *OMG Final Adoption: SysML v2.0, KerML v1.0, Systems Modeling API & Services v1.0*. Press release, 21 July 2025. <https://www.omg.org/news/releases/pr2025/07-21-25.htm>
- <a id="omg-sysmlv2-spec"></a>**[omg-sysmlv2-spec]** Object Management Group. *OMG Systems Modeling Language v2.0 — Language Specification*. <https://www.omg.org/sysml/sysmlv2/>
- <a id="omg-kerml-spec"></a>**[omg-kerml-spec]** Object Management Group. *Kernel Modeling Language (KerML) v1.0*. <https://www.omg.org/spec/KerML>
- <a id="omg-sysml-api-spec"></a>**[omg-sysml-api-spec]** Object Management Group. *Systems Modeling API & Services v1.0 Beta1*. <https://www.omg.org/spec/SystemsModelingAPI/1.0/Beta1/PDF>
- <a id="omg-sysmlv2-beta1"></a>**[omg-sysmlv2-beta1]** Object Management Group. *SysML v2 Beta1 Specification (Historical)*. <https://www.omg.org/spec/SysML/2.0/Beta1/About-SysML>
- <a id="omg-sysmlv2-beta2"></a>**[omg-sysmlv2-beta2]** Object Management Group. *SysML v2 Beta2 Specification (Historical)*. <https://www.omg.org/spec/SysML/2.0/Beta2/About-SysML>
- <a id="omg-sysmlv2-transformation"></a>**[omg-sysmlv2-transformation]** Object Management Group. *SysML v2 Part 4: SysML v1 to v2 Transformation Specification (Beta1)*. <https://www.omg.org/spec/SysML/2.0/Beta1/Transformation/PDF>
- <a id="dod-cto-2024"></a>**[dod-cto-2024]** U.S. Office of the Secretary of Defense — OUSD(R&E). *SysML v1 to SysML v2 Model Conversion Approach*. Version 1.3, March 2024. <https://www.cto.mil/wp-content/uploads/2025/02/SysML-v2-TransitionApproach-1.3.pdf>
- <a id="omg-psum-wiki"></a>**[omg-psum-wiki]** OMG. *Precise Semantics for Uncertainty Modeling (PSUM) Working Group Wiki*. <https://www.omgwiki.org/uncertainty/doku.php?id=start>

## 2 学术论文（按主题分组）

### 2.1 语义与形式化

- <a id="almeida-2024-kerml"></a>**[almeida-2024-kerml]** Almeida, J. P. A., Ferreira Pires, L., Guizzardi, G., & Wagner, G. (2024). *An Analysis of the Semantic Foundation of KerML and SysML v2*. ER 2024 (Springer LNCS). <https://nemo.inf.ufes.br/wp-content/papercite-data/pdf/an_analysis_of_the_semantic_foundation_of_kerml_and_sysml_v2_2024.pdf> · <https://link.springer.com/chapter/10.1007/978-3-031-75872-0_8>
- <a id="molnar-2024-formal-verif"></a>**[molnar-2024-formal-verif]** Molnár, V., & Graics, B. (2024). *Towards the Formal Verification of SysML v2 Models*. ACM/IEEE MODELS 2024. <https://dl.acm.org/doi/10.1145/3652620.3687820> · <https://cris.fbk.eu/retrieve/fc3415d4-028e-4a9f-9178-8a184958aecf/3652620.3687820%20(1).pdf>
- <a id="teodorov-2025-blueprint"></a>**[teodorov-2025-blueprint]** Teodorov, C., Lima, L., Nogueira, A., Guerin, S., & Lagadec, L. (2025). *A Research Agenda for the Living SysML V2 Blueprint*. SBMF 2025 (Springer). <https://link.springer.com/chapter/10.1007/978-3-032-12086-1_4>
- <a id="metamodel-validation-2025"></a>**[metamodel-validation-2025]** *Ensuring Semantic Consistency in SysML v2 Models Through Metamodel-Driven Validation* (2025). <https://www.researchgate.net/publication/393598625>

### 2.2 与 AADL / Modelica / 仿真桥接

- <a id="litwin-2024-aadl"></a>**[litwin-2024-aadl]** Litwin, K., Amundson, I., Verma, D., & McDermott, T. (2024). *Transforming AADL Models Into SysML 2.0: Insights and Recommendations*. SAE AeroTech 2024. <https://loonwerks.com/publications/pdf/litwin2024aerotech.pdf> · <https://saemobilus.sae.org/papers/transforming-aadl-models-sysml-20-insights-recommendations-2024-01-1947>
- <a id="aadl-sigada-2023"></a>**[aadl-sigada-2023]** *AADL modelling with SysML v2*. ACM SIGAda Ada Letters, 2023. <https://dl.acm.org/doi/10.1145/3631483.3631486>
- <a id="sei-aadl-sysmlv2-2023"></a>**[sei-aadl-sysmlv2-2023]** Carnegie Mellon SEI. *Extending SysML v2 with AADL Concepts to Support Engineering and Certification of Safety-Critical Systems*. SEI 2023 Year in Review. <https://www.sei.cmu.edu/annual-reviews/2023-year-in-review/extending-sysml-v2-with-aadl-concepts-to-support-engineering-and-certification-of-safety-critical-systems/>
- <a id="ahlbrecht-2024-avionics"></a>**[ahlbrecht-2024-avionics]** Ahlbrecht, A. *et al.* (2024). *Exploring SysML v2 for Model-Based Engineering of Safety-Critical Avionics Systems*. DASC 2024. <https://ieeexplore.ieee.org/iel8/10748556/10748653/10749311.pdf> · <https://elib.dlr.de/207398/1/DASC_Manuscript_Ahlbrecht_24_Final.pdf>
- <a id="zimmermann-2024-cosim"></a>**[zimmermann-2024-cosim]** *SysML v2 for Automated Co-simulation from Systems Architecture Models*. Springer LNCS, 2024. <https://link.springer.com/chapter/10.1007/978-3-031-62554-1_4>
- <a id="haugen-2025-dartwin"></a>**[haugen-2025-dartwin]** Haugen, Ø., Klikovits, S., Andersen, B., Beaulieu, A., Bordeleau, F., Denil, J., & Mertens, T. (2025). *DarTwin made precise by SysML v2 — An Experiment*. arXiv:2510.12478. <https://arxiv.org/abs/2510.12478>
- <a id="pepper-2024-incose"></a>**[pepper-2024-incose]** *Bidirectional SysML v2 ↔ Modelica Transformation*. INCOSE International Symposium, 2024. <https://incose.onlinelibrary.wiley.com/doi/abs/10.1002/iis2.13239>
- <a id="hardin-2025-dasc"></a>**[hardin-2025-dasc]** Hardin, D. *et al.* (2025). *Trustworthy Systems Engineering with SysML v2, AADL, and HAMR on seL4 microkit*. DASC 2025. <https://loonwerks.com/publications/pdf/hardin2025dasc.pdf>

### 2.3 LLM × SysML v2

- <a id="bouamra-2025-systemp"></a>**[bouamra-2025-systemp]** Bouamra, Y., Yun, B., Poisson, A., & Armetta, F. (2025). *SysTemp: A Multi-Agent System for Template-Based Generation of SysML v2*. arXiv:2506.21608. <https://arxiv.org/abs/2506.21608>
- <a id="ci-2025-agentic-rag"></a>**[ci-2025-agentic-rag]** *An Agent-Based Approach for the Automatic Generation of Valid SysMLv2 Models in Industrial Contexts*. Computers in Industry, 2025. <https://dl.acm.org/doi/10.1016/j.compind.2025.104350>
- <a id="jin-2025-sysmbench"></a>**[jin-2025-sysmbench]** Jin, D., Jin, Z., Li, L., Fang, Z., Li, J., & Chen, X. (2025). *A System Model Generation Benchmark from Natural Language Requirements (SysMBench)*. arXiv:2508.03215. <https://arxiv.org/abs/2508.03215>
- <a id="wang-2025-internetware"></a>**[wang-2025-internetware]** Wang, Y., Ge, N., Liu, J., Cao, Z., Chen, Z., & Hu, C. (2025). *Generating SysML Behavior Models via Large Language Models: An Empirical Study*. Internetware 2025. <https://dl.acm.org/doi/10.1145/3755881.3755926>
- <a id="incose-paper-137-2026"></a>**[incose-paper-137-2026]** *Using LLMs to Convert Documentation to SysML*. INCOSE IS 2026, Paper #137. <https://www.incose.org/wp-content/uploads/2026/01/Paper-137.pdf>
- <a id="text-to-model-2025"></a>**[text-to-model-2025]** *Text-to-Model via SysML*. arXiv:2507.06803. <https://arxiv.org/abs/2507.06803>
- <a id="nps-dair-2024"></a>**[nps-dair-2024]** Naval Postgraduate School DAIR. *Leveraging Generative AI to Build, Modify, and Query MBSE Models*. SYM-AM-24-138, 2024. <https://dair.nps.edu/bitstream/123456789/5237/1/SYM-AM-24-138.pdf>

### 2.4 工业 / 航空航天案例研究

- <a id="duroy-2025-esa"></a>**[duroy-2025-esa]** Duroy, R. *et al.* (2025). *Paving the Way for SysML v2: An ESA MBSE Methodology Implementation Review*. Systems Engineering. <https://incose.onlinelibrary.wiley.com/doi/10.1002/sys.70046>
- <a id="gpdis-gm-2024"></a>**[gpdis-gm-2024]** General Motors. *MBSE Collaboration with SysML 2.0: A Pre-Release Use*. GPDIS 2024. <https://gpdisonline.com/wp-content/uploads/2024/10/ADPAG-KyleHall-ADPAGMBSE-MBSE-Open1.pdf>
- <a id="ansys-models-2024"></a>**[ansys-models-2024]** Ansys. *SysML v2 Modeler and Digital Engineering Methodology*. MODELS 2024 Industry Day. <https://conf.researchr.org/details/models-2024/models-2024-industry-day/2/Ansys-SysML-v2-Modeler-and-Digital-Engineering-Methodology>

### 2.5 北航与中文学术

- <a id="zhang-2026-uncertainty"></a>**[zhang-2026-uncertainty]** Zhang, M., Li, Y., & Yue, T. (2026). *Uncertainty Modeling for SysML v2*. arXiv:2602.21641. 三作者邮箱均为 `@buaa.edu.cn`。<https://arxiv.org/abs/2602.21641>
- <a id="repo-psum-sysmlv2"></a>**[repo-psum-sysmlv2]** WSE-Laboratory. *PSUM-SysMLv2*——arXiv 2602.21641 的配套 artifact，含 7 个工业域案例（Adaptive Cruise Control / Camera / 等）。<https://github.com/WSE-Laboratory/PSUM-SysMLv2>
- <a id="gb-45803"></a>**[gb-45803]** *GB/T 45803-2025 系统与软件工程 基于模型的系统工程 统一架构建模语言*. 国家市场监督管理总局 / 国家标准化管理委员会，**2025-05-30 发布，2025-12-01 实施**。起草单位含**北京航空航天大学（鲁金直为核心起草人）**、北京理工大学、中国电子技术标准化研究院、商飞、兵器、航天等 16 家。<https://www.ndls.org.cn/standard/detail/28c9f842f8a22c6a6fee666390d8b1c0> · SAMR <https://std.samr.gov.cn/gb/search/gbDetailed?id=DF55C2967EADD24BE05397BE0A0A5C25>
- <a id="mbse-copilot-loughborough"></a>**[mbse-copilot-loughborough]** Wenheng Zhang 等 (Loughborough University). *MBSE Co-Pilot: A Research Roadmap*. INCOSE *Systems Engineering* 29(1):20-33 (2026), DOI 10.1002/sys.70011. **与北航 ModelCopilot 同名同题但完全无关**——前者是 vision-only 路线图论文，没有平台没有代码；后者是 vision + 平台 + profile + 案例 + 多个工具的"做实型"路径。<https://incose.onlinelibrary.wiley.com/doi/10.1002/sys.70011>
- <a id="yue-traceability-2014"></a>**[yue-traceability-2014]** Yue, T. *et al.* (2014). *Traceability and SysML Design Slices to Support Safety Inspection*. ACM TOSEM. <https://research.buaa.edu.cn/en/publications/traceability-and-sysml-design-slices-to-support-safety-inspection/>
- <a id="mbse-bibliometric-2026"></a>**[mbse-bibliometric-2026]** *MBSE 文献计量综述（2026）*。ScienceDirect. <https://www.sciencedirect.com/science/article/pii/S2950550X26000014>
- <a id="yue-tao-homepage"></a>**[yue-tao-homepage]** Yue, Tao. *Personal Homepage*（含 OMG SysML v2 standardisation contributor、PSUM co-chair 自述）。<https://yue-tao.github.io/>
- <a id="buaa-scse-yue"></a>**[buaa-scse-yue]** 北京航空航天大学计算机学院. *岳涛教授信息页*。<https://scse.buaa.edu.cn/info/1387/10998.htm>
- <a id="ge-ning-scholar"></a>**[ge-ning-scholar]** Ge, Ning. *Google Scholar Profile*. <https://scholar.google.com/citations?user=66slC9EAAAAJ>
- <a id="jin-zhi-pku"></a>**[jin-zhi-pku]** Jin, Zhi. *PKU Faculty Page*（明确北大教授，**非北航**，用于勘误）。<https://faculty.pku.edu.cn/zhijin/>

### 2.6 形式化方法综述

- <a id="uml2alloy-survey"></a>**[uml2alloy-survey]** Anastasakis, K., Bordbar, B., Georg, G., & Ray, I. *UML2Alloy: A Tool for Lightweight Modelling of Discrete Event Systems*. <https://www.cs.colostate.edu/~iray/research/papers/sosym10.pdf>
- <a id="formal-verif-survey"></a>**[formal-verif-survey]** *Formal Verification of Static Software Models in MDE: A Systematic Review*. <https://modeling-languages.com/wp-content/uploads/2014/05/mvsystematicreview.pdf>
- <a id="hamr-isabelle-2023"></a>**[hamr-isabelle-2023]** *HAMR Runtime Semantics in Isabelle/HOL*. Springer, 2023. <https://link.springer.com/chapter/10.1007/978-3-031-52183-6_3>
- <a id="logika-sttt-2025"></a>**[logika-sttt-2025]** *Logika: The Sireum Verification Framework*. STTT 2025. <https://link.springer.com/article/10.1007/s10009-025-00828-8>
- <a id="forsching-ltl-2025"></a>**[forsching-ltl-2025]** *Enhancing Model-Based Development with Formalized Requirements*. Forschung im Ingenieurwesen, 2025. <https://link.springer.com/article/10.1007/s10010-025-00806-1>
- <a id="qvt-diff-2024"></a>**[qvt-diff-2024]** *QVT-Based SysML v2 Diff Transformation*. IEEE, 2024. <https://ieeexplore.ieee.org/document/10864958/>

## 3 OMG / SysML v2 官方与参考实现

- <a id="repo-pilot"></a>**[repo-pilot]** *Systems-Modeling/SysML-v2-Pilot-Implementation*（Eclipse + Xtext + Java 21；KerML + SysML 双层 grammar；Jupyter kernel；PlantUML visualizer）。LGPL-3.0，221★，2026-05 活跃。<https://github.com/Systems-Modeling/SysML-v2-Pilot-Implementation>
- <a id="repo-release"></a>**[repo-release]** *Systems-Modeling/SysML-v2-Release*（OMG 官方发行/规范聚合，最新 tag `2026-03`）。LGPL-3.0，824★。<https://github.com/Systems-Modeling/SysML-v2-Release>
- <a id="repo-api-services"></a>**[repo-api-services]** *Systems-Modeling/SysML-v2-API-Services*（Play + Postgres 参考 REST 服务器）。LGPL-3.0，84★。<https://github.com/Systems-Modeling/SysML-v2-API-Services>
- <a id="repo-api-cookbook"></a>**[repo-api-cookbook]** *Systems-Modeling/SysML-v2-API-Cookbook*（Jupyter 范例）。56★。<https://github.com/Systems-Modeling/SysML-v2-API-Cookbook>
- <a id="repo-api-python"></a>**[repo-api-python]** *Systems-Modeling/SysML-v2-API-Python-Client*. 58★。<https://github.com/Systems-Modeling/SysML-v2-API-Python-Client>
- <a id="repo-api-java"></a>**[repo-api-java]** *Systems-Modeling/SysML-v2-API-Java-Client*. 17★。<https://github.com/Systems-Modeling/SysML-v2-API-Java-Client>
- <a id="repo-aadl-release"></a>**[repo-aadl-release]** *Systems-Modeling/SysML-v2-AADL-Release*（AADL profile/library，**非 transformer**）。CC-BY-ND，8★。<https://github.com/Systems-Modeling/SysML-v2-AADL-Release>

## 4 第三方开源实现

### 4.1 图形建模与协作

- <a id="repo-syson"></a>**[repo-syson]** *eclipse-syson/syson* — Obeo 牵头基于 Sirius Web 的开源 v2 图形建模器。EPL-2.0，278★，`v2026.3.0`，2026-05-04 活跃。<https://github.com/eclipse-syson/syson>
- <a id="syson-doc"></a>**[syson-doc]** Eclipse SysON 用户文档。<https://doc.mbse-syson.org/>
- <a id="syson-eclipse-project"></a>**[syson-eclipse-project]** Eclipse Foundation Project Page. <https://projects.eclipse.org/projects/modeling.syson>
- <a id="mbse-syson-org"></a>**[mbse-syson-org]** MBSE SysON 官方主页。<https://mbse-syson.org/>
- <a id="repo-flexo-mms"></a>**[repo-flexo-mms]** *Open-MBEE/flexo-mms-sysmlv2* — Kotlin/Ktor + Apache Jena + Fuseki，OMG v2 API 的 RDF/SPARQL PSM。11★，2026-05-05。<https://github.com/Open-MBEE/flexo-mms-sysmlv2>
- <a id="repo-flexo-mcp"></a>**[repo-flexo-mcp]** *Open-MBEE/flexo-mms-sysmlv2-mcp* — FastMCP/Python streamable HTTP，转发 Bearer token。2★。<https://github.com/Open-MBEE/flexo-mms-sysmlv2-mcp>
- <a id="repo-flexo-syside"></a>**[repo-flexo-syside]** *Open-MBEE/flexo_syside*（与 SysIDE 桥接）。4★。<https://github.com/Open-MBEE/flexo_syside>
- <a id="open-mbee-flexo"></a>**[open-mbee-flexo]** Open MBEE Flexo 项目主页。<https://www.openmbee.org/flexo.html>
- <a id="repo-open-mbee-examples"></a>**[repo-open-mbee-examples]** *Open-MBEE/SysML-v2-Applications-and-Examples*. <https://github.com/Open-MBEE/SysML-v2-Applications-and-Examples>
- <a id="repo-oslc-server"></a>**[repo-oslc-server]** *oslc-op/sysml-oslc-server* — Eclipse Lyo 生成的 OSLC 4.0 server。Apache-2.0，13★，2025-12。<https://github.com/oslc-op/sysml-oslc-server>
- <a id="repo-redsteve-mcp"></a>**[repo-redsteve-mcp]** *redsteve/SysML-v2-API-MCP-Server* — C++/MIT，将 v2 API 暴露为 MCP tool。17★。<https://github.com/redsteve/SysML-v2-API-MCP-Server>
- <a id="repo-mbse-tool"></a>**[repo-mbse-tool]** *mhlscvk/mbse-tool* — TypeScript Web 平台原型。3★，无 license。<https://github.com/mhlscvk/mbse-tool>

### 4.2 解析、LSP、IDE

- <a id="repo-sysml-2ls"></a>**[repo-sysml-2ls]** *sensmetry/sysml-2ls (SysIDE Legacy)* — Langium + Chevrotain + TS。**已 archive**。52★。<https://github.com/sensmetry/sysml-2ls>
- <a id="syside-rebirth"></a>**[syside-rebirth]** Sensmetry. *Syside Editor Rebirth: SysML v2.0, 50× speed-up, license change* (公告)。<https://sensmetry.com/syside-editor-rebirth-sysml-v2-0-50x-speed-up-license-change-free-as-before/>
- <a id="vscode-syside"></a>**[vscode-syside]** VS Code Marketplace — *sensmetry.syside-editor*（4254 安装，闭源）。<https://marketplace.visualstudio.com/items?itemName=sensmetry.syside-editor>
- <a id="repo-daltskin-lsp"></a>**[repo-daltskin-lsp]** *daltskin/sysml-v2-lsp* — TS/ANTLR4 LSP server + MCP CLI。MIT，12★。<https://github.com/daltskin/sysml-v2-lsp>
- <a id="repo-daltskin-grammar"></a>**[repo-daltskin-grammar]** *daltskin/sysml-v2-grammar* — ANTLR4 grammar，由 OMG KEBNF 自动生成。MIT，6★。<https://github.com/daltskin/sysml-v2-grammar>
- <a id="repo-daltskin-vscode"></a>**[repo-daltskin-vscode]** *daltskin/VSCode_SysML_Extension*. 32★。<https://github.com/daltskin/VSCode_SysML_Extension>
- <a id="vscode-jamied"></a>**[vscode-jamied]** VS Code Marketplace — *JamieD.sysml-v2-support*（961 安装）。<https://marketplace.visualstudio.com/items?itemName=JamieD.sysml-v2-support>
- <a id="repo-spec42"></a>**[repo-spec42]** *elan8/spec42* — Rust + tower-lsp 0.20 LSP server，含 Zed query files。MIT，6★，2026-05。<https://github.com/elan8/spec42>
- <a id="repo-sysml-v2-parser"></a>**[repo-sysml-v2-parser]** *elan8/sysml-v2-parser* — Rust + nom 8 parser，含 `parse_for_editor` resilient 模式。MIT，2★。<https://github.com/elan8/sysml-v2-parser>
- <a id="vscode-spec42"></a>**[vscode-spec42]** VS Code Marketplace — *Elan8.spec42*（182 安装）。<https://marketplace.visualstudio.com/items?itemName=Elan8.spec42>
- <a id="repo-monticore"></a>**[repo-monticore]** *MontiCore/sysmlv2* — RWTH MontiCore 语言工坊实现，12 个 `.mc4` 模块化文法。33★。<https://github.com/MontiCore/sysmlv2>
- <a id="repo-kerml-net"></a>**[repo-kerml-net]** *STARIONGROUP/KerML.NET* — .NET 端 KerML in-memory model + JSON serializer，**无 parser**。Apache-2.0，1★。<https://github.com/STARIONGROUP/KerML.NET>
- <a id="repo-intellij-sysml-old"></a>**[repo-intellij-sysml-old]** *luluorta/intellij-plugin-sysml*（**已废弃**，2015 年 v1 工程，0★，10 年未动——证据：IntelliJ 端 v2 完全空白）。<https://github.com/luluorta/intellij-plugin-sysml>

### 4.3 包管理与工程化

- <a id="repo-sysand"></a>**[repo-sysand]** *sensmetry/sysand* — Rust SysML v2/KerML 包管理器，pubgrub resolver + `.kpar`（OMG KerML §10.3）+ `sysand-lock.toml`。MIT/Apache-2.0，29★，2026-05 活跃。<https://github.com/sensmetry/sysand>
- <a id="sysand-docs"></a>**[sysand-docs]** sysand 用户文档。<https://docs.sysand.org/>
- <a id="sysand-arch"></a>**[sysand-arch]** sysand ARCHITECTURE.md。<https://github.com/sensmetry/sysand/blob/main/ARCHITECTURE.md>
- <a id="sysand-metadata"></a>**[sysand-metadata]** sysand 元数据规范。<https://github.com/sensmetry/sysand/blob/main/docs/src/metadata.md>
- <a id="pubgrub-crate"></a>**[pubgrub-crate]** pubgrub crate（Rust SAT-style version solver，与 cargo-next/uv 同款）。<https://crates.io/crates/pubgrub>

### 4.4 形式化与验证

- <a id="repo-verified-mbse"></a>**[repo-verified-mbse]** *chantakan/verified-mbse* — Lean 4 形式化 KerML 核心子集 + Kripke-LTL 行为框架 + 4 子系统航天器案例，zero-`sorry`。Apache-2.0，2★，2026-04 创建。<https://github.com/chantakan/verified-mbse>
- <a id="repo-sysmd"></a>**[repo-sysmd]** *tukcps/SysMD* — RPTU Kaiserslautern Notebook 风格工具，内置 AADD 约束求解器（区间 + LP，**非 SMT**）。Apache-2.0，38★，2026-04 v4.2.1。<https://github.com/tukcps/SysMD>
- <a id="repo-aadd"></a>**[repo-aadd]** *tukcps/Multiplatform-AADD* — Affine Arithmetic Decision Diagrams 库（SysMD 的求解器后端）。<https://github.com/tukcps/Multiplatform-AADD>
- <a id="repo-gamma"></a>**[repo-gamma]** *ftsrg/gamma* — BME 状态机验证框架，驱动 UPPAAL/Theta/Spin/nuXmv。EPL，35★，v2.12.0，2026-05 活跃。<https://github.com/ftsrg/gamma>
- <a id="repo-isse-fm-sysmlv2"></a>**[repo-isse-fm-sysmlv2]** *ftsrg/isse-formal-methods-sysmlv2* — MODELS 2024 Molnár 工作的伴随仓库；**仅含 SysML v2 模型，不含 transformer 源码**。2★，2025-10。<https://github.com/ftsrg/isse-formal-methods-sysmlv2>
- <a id="repo-hamr-sysml"></a>**[repo-hamr-sysml]** *sireum/hamr-sysml* — HAMR 主仓 SysML v2 前端（Scala/Slang，借 AADL 语义产 Slang/JVM/C/seL4 microkit）。BSD-2，1★，2026-04 活跃。<https://github.com/sireum/hamr-sysml>
- <a id="repo-hamr-parser"></a>**[repo-hamr-parser]** *sireum/hamr-sysml-parser*. 11★。<https://github.com/sireum/hamr-sysml-parser>
- <a id="repo-hamr-codegen"></a>**[repo-hamr-codegen]** *sireum/hamr-codegen*. <https://github.com/sireum/hamr-codegen>
- <a id="repo-aadl-gumbo"></a>**[repo-aadl-gumbo]** *sireum/aadl-gumbo* — assume/guarantee 合约语言。<https://github.com/sireum/aadl-gumbo>
- <a id="repo-logika"></a>**[repo-logika]** *sireum/logika* — Sireum 验证框架（Slang 后端，用 Z3/cvc4/cvc5）。<https://github.com/sireum/logika>
- <a id="hamr-sysmlv2-home"></a>**[hamr-sysmlv2-home]** Sireum HAMR for SysML v2 项目主页。<https://sireum.org/hamr-sysmlv2/>
- <a id="hamr-home"></a>**[hamr-home]** HAMR 项目主页。<https://hamr.sireum.org/>
- <a id="repo-santoslab-models"></a>**[repo-santoslab-models]** *santoslab/sysmlv2-models* — HAMR 真案例 + 4 平台 GH Actions（Linux/macOS/Windows/CAmkES Docker）。<https://github.com/santoslab/sysmlv2-models>
- <a id="fbk-saws2"></a>**[fbk-saws2]** FBK *SAWS² (Safety Analysis Validation & Verification for SysML v2)*. <http://fm.fbk.eu/tools/saws2-safety-analysis-validation-verification-for-sysml-v2/>
- <a id="imandra-sysml"></a>**[imandra-sysml]** Imandra. *SysML v2 Solution*（**闭源企业 license**）。<https://www.imandra.ai/sysml>
- <a id="repo-monterey-firebird"></a>**[repo-monterey-firebird]** Naval Postgraduate School. *Monterey Phoenix Firebird — SysML v2 Behavior Model Collection*（GitLab，事件 trace 有界穷尽）。<https://gitlab.nps.edu/monterey-phoenix/sysml-v2-behavior-model-collection>
- <a id="repo-opencaesar-oml"></a>**[repo-opencaesar-oml]** *opencaesar/oml* — JPL 的 OWL2 DL ontology 工程框架。<https://github.com/opencaesar/oml>
- <a id="opencaesar-sysmlv2"></a>**[opencaesar-sysmlv2]** openCAESAR. *SysML v2 Ontology Project*. <https://www.opencaesar.io/projects/2023-8-11-SysML-v2-Ontology.html>
- <a id="repo-gufo"></a>**[repo-gufo]** *nemo-ufes/gufo*（UFO 的 OWL2-DL 实现）。<https://github.com/nemo-ufes/gufo>
- <a id="repo-ontouml-vp"></a>**[repo-ontouml-vp]** *OntoUML/ontouml-vp-plugin*. <https://github.com/OntoUML/ontouml-vp-plugin>
- <a id="repo-pymbe"></a>**[repo-pymbe]** *sanbales/pymbe* — Python KerML occurrence/atom 解释器，最接近真行为执行的 OSS 工具，但 2024-10 停滞。GPL-3.0，5★。<https://github.com/sanbales/pymbe>
- <a id="repo-kreate"></a>**[repo-kreate]** *matetamasi/kreate* — KerML → Refinery（图变换/约束求解器）PoC。0★。<https://github.com/matetamasi/kreate>

### 4.5 Tree-sitter / 语法工具

- <a id="repo-nomograph-ts"></a>**[repo-nomograph-ts]** *nomograph-ai/tree-sitter-sysml* — 71 KB grammar.js，覆盖 OMG training/std-library。<https://github.com/nomograph-ai/tree-sitter-sysml>
- <a id="repo-nomograph-kebnf"></a>**[repo-nomograph-kebnf]** *nomograph-ai/kebnf* — OMG KEBNF → ANTLR4/tree-sitter 转换器。<https://github.com/nomograph-ai/kebnf>
- <a id="repo-jackhale-ts"></a>**[repo-jackhale-ts]** *jackhale98/tree-sitter-sysml*（51 KB grammar.js，213 OMG 文件 + 92/94 std-library 零错误）。<https://github.com/jackhale98/tree-sitter-sysml>
- <a id="repo-samonjourus-ts"></a>**[repo-samonjourus-ts]** *Samonjourus/tree-sitter-sysmlv2* — WIP（25 章 done 3）。<https://github.com/Samonjourus/tree-sitter-sysmlv2>

### 4.6 模型库 / 案例 / 教程

- <a id="repo-airbus-apollo"></a>**[repo-airbus-apollo]** *airbus/apollo-11-sysml-v2* — 空客发布的完整 Apollo 11 v2 参考模型。50★，2026-04。<https://github.com/airbus/apollo-11-sysml-v2>
- <a id="repo-gfse-models"></a>**[repo-gfse-models]** *GfSE/SysML-v2-Models* — 德国系统工程协会维护语料。65★。<https://github.com/GfSE/SysML-v2-Models>
- <a id="repo-mbse4u-jupyterbook"></a>**[repo-mbse4u-jupyterbook]** *MBSE4U/SysMLv2JupyterBook* — Tim Weilkiens 教材伴随。33★。<https://github.com/MBSE4U/SysMLv2JupyterBook>
- <a id="repo-mbse4u-batmobile"></a>**[repo-mbse4u-batmobile]** *MBSE4U/dont-panic-batmobile* — 教学示例。<https://github.com/MBSE4U/dont-panic-batmobile>

### 4.7 国内开源

- <a id="repo-sysmline"></a>**[repo-sysmline]** *Ruizhe-Yang/SysMLine* — 大连理工大学 PoC。EPL-2.0，5★。<https://github.com/Ruizhe-Yang/SysMLine>
- <a id="gitcode-mirror"></a>**[gitcode-mirror]** GitCode 镜像 — `SysML-v2-Release`. <https://gitcode.com/gh_mirrors/sy/SysML-v2-Release>

## 5 商用产品（含路线图）

- <a id="vendor-cameo-2026x"></a>**[vendor-cameo-2026x]** Dassault Systèmes. *Cameo / CATIA No Magic 2026x SysMLv2 Plugin*. <https://docs.nomagic.com/spaces/CATIA/pages/261619716/CATIA+SysML+v2+Solution> · 第三方分析 <https://www.goengineer.com/blog/advantages-of-sysml-v2-now-available-in-no-magic-cameo-and-catia-magic-2026>
- <a id="vendor-ibm-rhapsody-se"></a>**[vendor-ibm-rhapsody-se]** IBM. *Rhapsody Systems Engineering*. <https://www.ibm.com/products/rhapsody-systems-engineering>
- <a id="vendor-siemens-system-modeler"></a>**[vendor-siemens-system-modeler]** Siemens. *System Modeler for SysML v2 (合作 IBM)*. <https://news.siemens.com/en-us/siemens-system-modeler-for-sysml/>
- <a id="vendor-siemens-capital-2512"></a>**[vendor-siemens-capital-2512]** Siemens. *Capital 2512 Release Notes*. <https://blogs.sw.siemens.com/ee-systems/2026/02/27/whats-new-in-capital-2512/>
- <a id="vendor-ptc-windchill-10"></a>**[vendor-ptc-windchill-10]** PTC. *Windchill Modeler 10 — Introducing SysML v2 First Phase*. <https://www.ptc.com/en/blogs/alm/introducing-windchill-modeler-10-whats-new-and-noteworthy>
- <a id="vendor-ansys-sam"></a>**[vendor-ansys-sam]** Ansys. *SAM 2026 R1 — Advancing MBSE*. <https://www.ansys.com/blog/advancing-mbse-ansys-sam-2026-r1>
- <a id="vendor-vp-sysmlv2"></a>**[vendor-vp-sysmlv2]** Visual Paradigm. *SysML v2 Studio*. <https://updates.visual-paradigm.com/releases/sysml-v2-studio-competitive-advantages-launch/>
- <a id="webel-sysmlv2"></a>**[webel-sysmlv2]** Webel. *SysML v2 资源汇总*. <https://www.webel.com.au/sysml/sysmlv2>
- <a id="vendor-modelcopilot-platform"></a>**[vendor-modelcopilot-platform]** WSE-Laboratory (BUAA). *ModelCopilot Platform* — 闭源 Web SPA，平台代码暂未开源（仓库 `WSE-Laboratory/WSE-ModelCopilot` 截至 2026-05-05 仍为占位状态）。HTTP-only 部署位于 `http://116.204.36.247`，对外开放注册。前端为 Vue 3 + Element Plus（bundle `index-DrCKs0sc.js`，1.17 MB），后端为 Spring Boot + MongoDB；可视化引擎为 PlantUML（base64 SVG）。本快照采样于 2026-05-05；详细实测见 [docs/13](13-modelcopilot-platform-walkthrough.md)。
- <a id="vendor-mdesign"></a>**[vendor-mdesign]** 杭州华望系统科技有限公司. *M-Design v2 alpha* — 国内**唯一公开商用化**的 SysML v2 平台，2025-09-14 发布。是国家标准 GB/T 45803-2025 商业落地的代表产品。详见 docs/00 §5 时间节点条目。

## 6 同类建模语言基础设施（baseline 对照）

### 6.1 UML

- <a id="papyrus"></a>**[papyrus]** Eclipse Papyrus 7.x. <https://eclipse.dev/papyrus/>
- <a id="modelio"></a>**[modelio]** Modelio Open Source. <https://store.modelio.org/>
- <a id="emf-compare"></a>**[emf-compare]** EMF Compare. <https://eclipse.dev/emfcompare/>
- <a id="acceleo"></a>**[acceleo]** Acceleo (OMG MOF M2T 标准实现). <https://eclipse.dev/acceleo/>

### 6.2 SysML v1

- <a id="papyrus-sysml-16"></a>**[papyrus-sysml-16]** Papyrus SysML 1.6. <https://marketplace.eclipse.org/content/papyrus-sysml-16>
- <a id="modelio-sysml-arch"></a>**[modelio-sysml-arch]** Modelio SysML Architect Open Source. <https://store.modelio.org/resource/modules/sysml-architect-open-source.html>
- <a id="sysml-tools-org"></a>**[sysml-tools-org]** SysML Tools 总览. <https://sysml.org/sysml-tools/>

### 6.3 AADL

- <a id="osate"></a>**[osate]** OSATE 2.18. <https://osate.org/about-osate.html>
- <a id="repo-osate"></a>**[repo-osate]** *osate/osate2*. 54★。<https://github.com/osate/osate2>
- <a id="repo-oqarina"></a>**[repo-oqarina]** *Oqarina/oqarina* — AADL 在 Coq 中的机械化。<https://github.com/Oqarina/oqarina>
- <a id="repo-resolute"></a>**[repo-resolute]** *loonwerks/Resolute*. <https://github.com/loonwerks/Resolute>
- <a id="resolint-update"></a>**[resolint-update]** Resolute Updates. <http://loonwerks.com/Resolute-Updates/>

### 6.4 Modelica

- <a id="repo-openmodelica"></a>**[repo-openmodelica]** *OpenModelica/OpenModelica*. 1303★。<https://github.com/OpenModelica/OpenModelica>
- <a id="modelica-newsletter-2025"></a>**[modelica-newsletter-2025]** Modelica Newsletter 2025-01. <https://newsletter.modelica.org/2025-01/index>
- <a id="openmodelica-fmi-tlm"></a>**[openmodelica-fmi-tlm]** OpenModelica FMI/TLM Documentation. <https://openmodelica.org/doc/OpenModelicaUsersGuide/latest/fmitlm.html>
- <a id="repo-sysml-modelica-legacy"></a>**[repo-sysml-modelica-legacy]** *SysMLModelicaIntegration/edu.gatech.mbse.mdsysmlmodelica*（**SysML v1 + MagicDraw，2014 遗留**）。<https://github.com/SysMLModelicaIntegration/edu.gatech.mbse.mdsysmlmodelica>

### 6.5 Capella

- <a id="capella-mbse"></a>**[capella-mbse]** Eclipse Capella 主页. <https://mbse-capella.org/>
- <a id="repo-capella"></a>**[repo-capella]** *eclipse-capella/capella*. 315★，7.x。<https://github.com/eclipse-capella/capella>
- <a id="team-for-capella"></a>**[team-for-capella]** Team for Capella 7.0.1（Obeo 商业，CDO 4.22）. <https://www.obeosoft.com/en/team-for-capella>
- <a id="team-for-capella-guide"></a>**[team-for-capella-guide]** Team for Capella 7.0.1 Guide PDF. <https://www.obeosoft.com/download/release/team-for-capella/7.0/7.0.1/TeamForCapella-7.0.1-Guide.pdf>
- <a id="repo-capella-collab"></a>**[repo-capella-collab]** *DSD-DBS/capella-collab-manager*. <https://github.com/DSD-DBS/capella-collab-manager>

### 6.6 BPMN

- <a id="repo-bpmn-js"></a>**[repo-bpmn-js]** *bpmn-io/bpmn-js*. 9528★。<https://github.com/bpmn-io/bpmn-js>
- <a id="repo-camunda-modeler"></a>**[repo-camunda-modeler]** *camunda/camunda-modeler*. <https://github.com/camunda/camunda-modeler>
- <a id="camunda7-eol"></a>**[camunda7-eol]** Camunda. *Camunda 7 Enterprise End of Life Extension*. <https://camunda.com/blog/2025/02/camunda-7-enterprise-end-of-life-extension/>

### 6.7 TLA+

- <a id="repo-tlaplus"></a>**[repo-tlaplus]** *tlaplus/tlaplus*. 2892★。<https://github.com/tlaplus/tlaplus>
- <a id="repo-vscode-tlaplus"></a>**[repo-vscode-tlaplus]** *tlaplus/vscode-tlaplus*. <https://github.com/tlaplus/vscode-tlaplus>
- <a id="apalache"></a>**[apalache]** Apalache (Informal Systems). <https://apalache.informal.systems/>
- <a id="apalache-mbt"></a>**[apalache-mbt]** Informal Systems MBT 工具栈. <https://mbt.informal.systems/docs/tla_basics_tutorials/ecosystem.html>

### 6.8 Alloy

- <a id="alloy-tools"></a>**[alloy-tools]** Alloy Analyzer 6.2.0. <https://alloytools.org/>
- <a id="repo-alloy"></a>**[repo-alloy]** *AlloyTools/org.alloytools.alloy*. 832★。<https://github.com/AlloyTools/org.alloytools.alloy>
- <a id="practical-alloy"></a>**[practical-alloy]** Macedo, N. *et al.* *Practical Alloy*（在线书，2025-02）. <https://haslab.github.io/formal-software-design/>

## 7 教程与培训资源

- <a id="sensmetry-advent"></a>**[sensmetry-advent]** Sensmetry. *Advent of SysML v2 — 25 Lesson Series*. <https://sensmetry.com/advent-of-sysml-v2/>
- <a id="sensmetry-lesson-6"></a>**[sensmetry-lesson-6]** *Lesson 6 — Version Control with Git*. <https://sensmetry.com/advent-of-sysml-v2-lesson-6-version-control-with-git/>
- <a id="sensmetry-lesson-19"></a>**[sensmetry-lesson-19]** *Lesson 19 — State Machine Simulation (Sismic)*. <https://sensmetry.com/advent-of-sysml-v2-lesson-19-state-machine-simulation/>
- <a id="sensmetry-lesson-20"></a>**[sensmetry-lesson-20]** *Lesson 20 — CI/CD for SysML v2 Models*. <https://sensmetry.com/advent-of-sysml-v2-lesson-20-ci-cd-for-sysml-v2-models/>
- <a id="syside-automator-docs"></a>**[syside-automator-docs]** Sensmetry. *Syside Automator Documentation*（闭源商业）。<https://docs.sensmetry.com/automator/>
- <a id="oose-clubs-sysmlv2"></a>**[oose-clubs-sysmlv2]** OOSE. *SysML v2 Learning Club*. <https://clubs.oose.com/courses/sysmlv2/>
- <a id="sensmetry-detect-migration"></a>**[sensmetry-detect-migration]** Sensmetry. *DETECT Case Study: SysML v1 → v2 Migration Lessons Learned*. <https://sensmetry.com/sysml-v1-to-sysml-v2-migration-of-detect-benefits-lessons-learned/>

## 8 第三方工具与平台

- <a id="repo-windtrader"></a>**[repo-windtrader]** *Westfall-io/windtrader* — Python parse-only validator wrapper（MIT，0★）。<https://github.com/Westfall-io/windtrader>
- <a id="repo-mbse-lab"></a>**[repo-mbse-lab]** *cosgroma/mbse-lab*. <https://github.com/cosgroma/mbse-lab>
- <a id="repo-sysml-docker-archived"></a>**[repo-sysml-docker-archived]** *kenji-miyake/sysml-v2-docker*（**已 archive**）。<https://github.com/kenji-miyake/sysml-v2-docker>
- <a id="repo-explorer"></a>**[repo-explorer]** *dandelivers/sysml-v2-explorer*. <https://github.com/dandelivers/sysml-v2-explorer>
- <a id="sirius-web"></a>**[sirius-web]** Sirius Web 主页. <https://eclipse.dev/sirius/sirius-web.html>
- <a id="repo-sirius-web-tutorial"></a>**[repo-sirius-web-tutorial]** *ObeoNetwork/Sirius-Web-Tutorial*. <https://github.com/ObeoNetwork/Sirius-Web-Tutorial>
- <a id="repo-cdp4-comet"></a>**[repo-cdp4-comet]** *STARIONGROUP/COMET-IME-Community-Edition*（ECSS-E-TM-10-25 工具）。<https://github.com/STARIONGROUP/COMET-IME-Community-Edition>
- <a id="jupyter-jvm-basekernel"></a>**[jupyter-jvm-basekernel]** *SpencerPark/jupyter-jvm-basekernel*. <https://github.com/SpencerPark/jupyter-jvm-basekernel>

