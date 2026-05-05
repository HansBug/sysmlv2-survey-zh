# 08 基线对照：与 UML / SysML v1 / AADL / Modelica / Capella / BPMN / TLA+ / Alloy 的成熟度横评

## 本章简介

本章把 SysML v2 与八个同类建模语言的开源基础设施做成熟度横评，按解析 / IDE、形式化 / 语义、可视化、协作 / Diff、Codegen / 仿真五个维度评分。读者读完应能回答：「SysML v2 的工具生态比起前辈处于什么水平？哪些维度领先、哪些落后多少？」

## 1 评分体系

> 1 = 学术原型 / 单点工具；3 = 工业可用、多家选择；5 = 行业标杆、事实标准。

每条评分均附一手证据（仓库、规范、出版物），具体见各节子章。

## 2 八种建模语言生态评估

### 2.1 UML（"老炮"参照系，约 25 年生态）

| 维度 | 评分 | 依据 |
|---|---|---|
| 解析 / IDE | **5** | [Eclipse Papyrus 7.x][^papyrus]（2025-06 切到 Sirius-Desktop 渲染）、[Modelio][^modelio]、IBM RSAD/Rhapsody 商业线长期并存。Marketplace 数百插件。 |
| 形式化 / 语义 | 4 | UML2Alloy（MODELS'07 起）[^uml2alloy]、HOL-OCL（Isabelle）、UML Sequence in Coq 等，几十年学术积累；语义本身仍非"机械证明的标准"，但形式化文献量数千篇级 |
| 可视化 | 5 | Papyrus + Sirius、Modelio、商业 EA / MagicDraw / Rhapsody，13 类视图工业级 |
| 协作 / Diff | 5 | [EMF Compare][^emf-compare]（含 Papyrus GMF 图形比对）、EMF DiffMerge、CDO 仓库、Git+XMI 都已是默认 |
| Codegen / 仿真 | 5 | [Acceleo][^acceleo]（OMG MOF M2T 标准实现）、Xtend、ATL、QVT 全栈，Java/C++/C# 模板生态成熟 |

### 2.2 SysML v1（UML profile，已 ~20 年）

| 维度 | 评分 | 依据 |
|---|---|---|
| 解析 / IDE | 4 | [Papyrus SysML 1.6][^papyrus-sysml]、[Modelio SysML Architect][^modelio-sysml-arch]；商用生态由 MagicDraw/Cameo、Rhapsody、Enterprise Architect 主导；社区结论："开源仍逊于商业产品" |
| 形式化 / 语义 | 4 | 复用 UML 形式化工具链；MARTE / profile 层面的形式化工作尤其活跃 |
| 可视化 | 5 | 9 类标准图，所有工具完整支持；MagicDraw 的图形质量被视为业界天花板 |
| 协作 / Diff | 4 | Cameo Teamwork Cloud（商业）、EMF Compare/Git（开源）皆主流 |
| Codegen / 仿真 | 4 | Cameo Simulation Toolkit、ParaMagic / OpenModelica / Modelica 桥接、Acceleo 模板 |

### 2.3 AADL（航空安全关键）

| 维度 | 评分 | 依据 |
|---|---|---|
| 解析 / IDE | 4 | [OSATE 2.18][^osate]（Eclipse + Xtext，源码 [osate/osate2][^repo-osate] 54★）；规模虽小但 SEI 持续维护 15 年+ |
| 形式化 / 语义 | **5** | 这是 AADL 的强项—— [Oqarina][^repo-oqarina]（AADL 在 Coq 中的机械化）、Real-Time Maude 行为语义、HAMR/Isabelle AADL runtime 形式化、AGREE（compositional assume-guarantee）、[Resolute][^repo-resolute]（结构化保证用例）、Resolint linter |
| 可视化 | 3 | OSATE 图形编辑器、AADL Inspector（商业 Ellidiss） |
| 协作 / Diff | 2 | 依赖 Git + 文本 .aadl 文件，缺乏专用图形 diff |
| Codegen / 仿真 | 4 | Ocarina、HAMR（Slang/JVM/C）做代码生成；与 SCADE、Simulink 互通 |

### 2.4 Modelica（物理 / 连续时间）

| 维度 | 评分 | 依据 |
|---|---|---|
| 解析 / IDE | 5 | [OpenModelica 1.26.0][^repo-openmodelica]（2025 winter）、Dymola/MapleSim/SimulationX 商业线繁荣 |
| 形式化 / 语义 | 2 | 形式语义有学术尝试（混合自动机、PVS），但**没有事实工业级机械化**；FMI 标准成为"事实合约"，并非证明意义上的形式化 |
| 可视化 | 5 | OMEdit、Dymola 图形编辑器经数十年打磨 |
| 协作 / Diff | 3 | OMSimulator + Git + FMU 包；图形 diff 能力一般 |
| Codegen / 仿真 | **5** | Modelica Standard Library + DAE 求解器 + [FMI 1.0/2.0/3.0 导入导出][^om-fmi]是行业基准 |

### 2.5 Capella / Arcadia

| 维度 | 评分 | 依据 |
|---|---|---|
| 解析 / IDE | 4 | [Eclipse Capella][^capella-mbse]（[源码 315★][^repo-capella]，已发到 7.x），含完整 Arcadia 方法论实施 |
| 形式化 / 语义 | 2 | 弱——以方法论为主，少量与 Simulink/AltaRica 桥接的研究 |
| 可视化 | 5 | Sirius 渲染，工业级；Capella 是 Sirius 自身的旗舰应用 |
| 协作 / Diff | 4 | [Team for Capella][^team-capella] 7.0.1（Obeo 商业，CDO 4.22）做细粒度锁；[capella-collab-manager][^repo-capella-collab]（DB Systel 开源）做容器化协作。注意官方文档明说"内置 diff/merge 不适合长期多人共享" |
| Codegen / 仿真 | 3 | M2Doc 文档生成、Python4Capella、Capella2Modelica 等桥接，工程化但范围窄于 UML |

### 2.6 BPMN 2.0

| 维度 | 评分 | 依据 |
|---|---|---|
| 解析 / IDE | **5** | [bpmn-js][^repo-bpmn-js] **9528★**（最受欢迎建模库之一）、[Camunda Modeler][^repo-camunda-modeler]桌面端、demo.bpmn.io 在线 |
| 形式化 / 语义 | 3 | Petri 网 / 语义已有大量学术工作，但工业生态主要靠**可执行语义**而非证明 |
| 可视化 | **5** | 浏览器内嵌、所见即所得，是 OSS 建模可视化的工程典范 |
| 协作 / Diff | 3 | 基于 XML + Git；bpmn-io 提供 token simulation |
| Codegen / 仿真 | **5** | Camunda 8（Zeebe）、Flowable、jBPM、bpmn-engine（JS）等多家可执行实现；Camunda 7 CE 已 EoL[^camunda-eol]，迁向 Camunda 8 |

### 2.7 TLA+（验证基础设施金标准）

| 维度 | 评分 | 依据 |
|---|---|---|
| 解析 / IDE | 4 | 经典 [TLA+ Toolbox][^repo-tlaplus]（**2892★**）+ [vscode-tlaplus][^repo-vscode-tlaplus]（2025-09 仍活跃，已成主推），CLI tla2tools |
| 形式化 / 语义 | **5** | 本身就是验证基础设施—— TLC 显式状态模型检测、[Apalache][^apalache]（符号 SMT-based）、TLAPS 证明系统 |
| 可视化 | 1 | 弱——错误轨迹查看器、状态图导出，没有图形建模。这是设计取舍，不是缺陷 |
| 协作 / Diff | 2 | 纯文本 + Git，没有专门工具 |
| "执行 / 检查" | **5** | TLC、Apalache 即"运行环境"；AWS、MongoDB、Confluent、Tendermint、CockroachDB 等大量实证案例 |

### 2.8 Alloy

| 维度 | 评分 | 依据 |
|---|---|---|
| 解析 / IDE | 4 | [Alloy Analyzer 6.2.0][^alloy-tools]（2025-01，[源码 832★][^repo-alloy]）；Alloy 6 增加可变状态 + 时序逻辑 |
| 形式化 / 语义 | 4 | 核心即"一阶关系逻辑 + SAT"；Alloy*、电子-Alloy 等扩展支持高阶 / SMT |
| 可视化 | 3 | 内置实例可视化器、新版 Visualizer 体验改善 |
| 协作 / Diff | 2 | 纯文本 + Git |
| Codegen / 仿真 | 4 | 将模型编译成布尔公式后用 SAT 求解，2025-02 出版的 [*Practical Alloy*][^practical-alloy]在线书是新近教材 |

### 2.9 SysML v2（保守评估）

| 维度 | 评分 | 依据 |
|---|---|---|
| 解析 / IDE | **3** | [SysML-v2-Pilot-Implementation][^repo-pilot]（221★）+ [SysIDE/Langium][^repo-sysml-2ls](archived)+ [Eclipse SysON][^repo-syson]；6 套独立 parser；Sensmetry 新版闭源；IntelliJ 完全空白 |
| 形式化 / 语义 | **2** | KerML 半形式化基础好，但暂无 Coq/Isabelle 机械化、无成熟 SMT/Alloy 桥；HAMR + GUMBO + Logika 是唯一端到端开源链 |
| 可视化 | **2** | SysON / Cameo / IncQuery 都在 alpha-beta，多视图（Sequence、Use Case）仍未落地 |
| 协作 / Diff | **2** | 文本 + Git 可用；图形 diff/merge、专用合并工具尚缺；**API 标准本身没定义 merge** |
| Codegen / 仿真 | **2** | REST API 设计很好，但代码生成器、仿真桥（Modelica/FMI）大多在 PoC 阶段 |

## 3 综合矩阵

| 语言 | 解析/IDE | 形式化/语义 | 可视化 | 协作/Diff | Codegen/仿真 |
|---|---|---|---|---|---|
| **UML** | 5 | 4 | 5 | 5 | 5 |
| **SysML v1** | 4 | 4 | 5 | 4 | 4 |
| **AADL** | 4 | **5** | 3 | 2 | 4 |
| **Modelica** | 5 | 2 | 5 | 3 | **5** |
| **Capella** | 4 | 2 | 5 | 4 | 3 |
| **BPMN 2.0** | **5** | 3 | **5** | 3 | **5** |
| **TLA+** | 4 | **5** | 1 | 2 | **5** |
| **Alloy** | 4 | 4 | 3 | 2 | 4 |
| **SysML v2** | **3** | **2** | **2** | **2** | **2** |

## 4 SysML v2 落后于「行业标杆」的清单

以下是 v1 / UML / AADL / Modelica 已是标准实践、但 SysML v2 尚缺的工具能力：

1. **图形模型 diff / merge**：UML 有 EMF Compare GMF 集成、Capella 有 Team for Capella，SysML v2 仍依赖文本 diff。
2. **重构引擎 / Quick-Fix 库**：Papyrus / Modelio 内有几十种重构与一致性快速修复；SysON 当前主要还是建模 + 校验。
3. **多目标代码生成器**：UML 有 Acceleo + 数千模板、AADL 有 HAMR / Ocarina；SysML v2 没有 Acceleo 量级的生态，只有几个 PoC。
4. **形式化与机械化**：AADL 有 [Oqarina][^repo-oqarina]、HAMR / Isabelle；UML 有 HOL-OCL；SysML v2 还没有等价的 Coq / Isabelle 形式化。
5. **教材 / 培训密度**：Modelica 有 OpenModelica 用户指南 + Fritzson 教材；TLA+ 有 learntla.com + Lamport video course；SysML v1 有十余本 INCOSE 教材。SysML v2 目前仅 [Sensmetry "Advent of SysML v2"][^sensmetry-advent]、[OOSE Learning Club][^oose]、Siemens / PTC 软文级指南，尚无正式教材出版（Tim Weilkiens 的 *SysML v2 Book* 是少数例外）。
6. **IDE Marketplace 富度**：Papyrus 上插件数百，SysML v2 当前生态在 GitHub 上数十个项目，多是参考实现。
7. **可执行 / 仿真桥**：Modelica/FMI 是事实标准、Camunda 是事实执行引擎；SysML v2 与 Modelica/FMI 的桥接仍在论证。
8. **JetBrains / Vim / Emacs 端工具**：UML / v1 在 IntelliJ 端有商业插件支持；SysML v2 在 IntelliJ 完全空白（详见 [04-解析 IDE §4.2](04-parsing-ide-infrastructure.md#42-jetbrains-marketplace)）。

## 5 SysML v2 真正胜过前辈的清单

不应被批判性评估遮蔽：v2 在以下维度有**代际优势**——

1. **文本表示一等公民**：与 TLA+ / Alloy 同级，原生支持 LSP（Langium、Xtext、ANTLR4、nom 四套实现），Git diff / PR review 流畅，规避了 v1 XMI 的痛。
2. **分层 KerML / SysML**：核心语义在 KerML，避免 UML profile 那种"拼接 metamodel"的难维护，将来形式化更友好（详见 [01-标准状态 §3](01-standard-status.md#3-kerml-与-sysml-v2-的语言分层架构)）。
3. **REST/HTTP API 在标准里**：[SysML-v2-API-Services][^repo-api-services]由 OMG 标准化，UML / v1 都没有同等地位的 API；这是 web 化、CI/CD 集成的根本性优势。
4. **OMG 终稿**（2025-06 行政通过、2025-09 出版）后语义稳定，避免 v1 那种 profile 混乱[^omg-final]。
5. **KPAR + sysand 包管理**：直接复用 OMG 规范的 `.kpar` 格式 + pubgrub SAT-style resolver + IRI 多协议解析（详见 [04-解析 IDE §5](04-parsing-ide-infrastructure.md#5-kpar-包格式与-sensmetry-sysand)），是整套生态最现代的设计选择，UML / v1 完全没有这一层。

## 6 一句话定位

**SysML v2 在语言设计层面已对标 TLA+/Alloy 的"文本+LSP"现代范式，REST API 与 KPAR 是真正的代际飞跃；但工具生态成熟度今天明显落后于 UML/v1（图形 diff/重构/codegen）、AADL（形式化）、Modelica（仿真/FMI）、BPMN（执行引擎）。语言赢了，工具差至少 5 年——这与 OMG 2025-09 才终稿、参考实现仍以 Pilot 命名的客观节点完全自洽。**

## 参考文献

[^papyrus]: Eclipse Papyrus 主页. <https://eclipse.dev/papyrus/>

[^modelio]: Modelio Open Source. <https://store.modelio.org/>

[^uml2alloy]: Anastasakis, K. 等. *UML2Alloy: A Tool for Lightweight Modelling of Discrete Event Systems*. <https://www.cs.colostate.edu/~iray/research/papers/sosym10.pdf>

[^emf-compare]: EMF Compare. <https://eclipse.dev/emfcompare/>

[^acceleo]: Acceleo. <https://eclipse.dev/acceleo/>

[^papyrus-sysml]: Papyrus SysML 1.6. <https://marketplace.eclipse.org/content/papyrus-sysml-16>

[^modelio-sysml-arch]: Modelio SysML Architect Open Source. <https://store.modelio.org/resource/modules/sysml-architect-open-source.html>

[^osate]: OSATE 2.18. <https://osate.org/about-osate.html>

[^repo-osate]: *osate/osate2*. <https://github.com/osate/osate2>

[^repo-oqarina]: *Oqarina/oqarina* — AADL 在 Coq 中的机械化. <https://github.com/Oqarina/oqarina>

[^repo-resolute]: *loonwerks/Resolute*. <https://github.com/loonwerks/Resolute>

[^repo-openmodelica]: *OpenModelica/OpenModelica*. <https://github.com/OpenModelica/OpenModelica>

[^om-fmi]: OpenModelica FMI/TLM Documentation. <https://openmodelica.org/doc/OpenModelicaUsersGuide/latest/fmitlm.html>

[^capella-mbse]: Eclipse Capella 主页. <https://mbse-capella.org/>

[^repo-capella]: *eclipse-capella/capella*. <https://github.com/eclipse-capella/capella>

[^team-capella]: Team for Capella 7.0.1. <https://www.obeosoft.com/en/team-for-capella>

[^repo-capella-collab]: *DSD-DBS/capella-collab-manager*. <https://github.com/DSD-DBS/capella-collab-manager>

[^repo-bpmn-js]: *bpmn-io/bpmn-js*. <https://github.com/bpmn-io/bpmn-js>

[^repo-camunda-modeler]: *camunda/camunda-modeler*. <https://github.com/camunda/camunda-modeler>

[^camunda-eol]: Camunda. *Camunda 7 Enterprise End of Life*. <https://camunda.com/blog/2025/02/camunda-7-enterprise-end-of-life-extension/>

[^repo-tlaplus]: *tlaplus/tlaplus*. <https://github.com/tlaplus/tlaplus>

[^repo-vscode-tlaplus]: *tlaplus/vscode-tlaplus*. <https://github.com/tlaplus/vscode-tlaplus>

[^apalache]: Apalache. <https://apalache.informal.systems/>

[^alloy-tools]: Alloy Analyzer 6.2.0. <https://alloytools.org/>

[^repo-alloy]: *AlloyTools/org.alloytools.alloy*. <https://github.com/AlloyTools/org.alloytools.alloy>

[^practical-alloy]: Macedo, N. 等. *Practical Alloy*（在线书）. <https://haslab.github.io/formal-software-design/>

[^repo-pilot]: *Systems-Modeling/SysML-v2-Pilot-Implementation*. <https://github.com/Systems-Modeling/SysML-v2-Pilot-Implementation>

[^repo-sysml-2ls]: *sensmetry/sysml-2ls*（archived）。<https://github.com/sensmetry/sysml-2ls>

[^repo-syson]: *eclipse-syson/syson*. <https://github.com/eclipse-syson/syson>

[^repo-api-services]: *Systems-Modeling/SysML-v2-API-Services*. <https://github.com/Systems-Modeling/SysML-v2-API-Services>

[^omg-final]: OMG. *Final Adoption: SysML v2.0, KerML v1.0, Systems Modeling API & Services v1.0*. 2025-07-21. <https://www.omg.org/news/releases/pr2025/07-21-25.htm>

[^sensmetry-advent]: Sensmetry. *Advent of SysML v2*. <https://sensmetry.com/advent-of-sysml-v2/>

[^oose]: OOSE. *SysML v2 Learning Club*. <https://clubs.oose.com/courses/sysmlv2/>
