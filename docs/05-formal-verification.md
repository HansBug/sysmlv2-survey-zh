# 05 形式化与验证基础设施

## 本章简介

本章评估 SysML v2 / KerML **形式化、模型检查、定理证明**方向的开源工具实情。覆盖 11 条路径：Lean 4 形式化、SysMD 约束求解、Gamma 状态机验证、Sireum HAMR 高保障代码生成、UFO 本体论分析、OCL 校验、时序逻辑性质规约、定理证明器后端、符号执行 / 抽象解释、政府资助工具、Living Blueprint 路线图。每条路径标记 **Production-ready / Beta / PoC / Vapor / Spec-only** 五档可用度。

读者读完应能回答：「我现在能用什么工具对 SysML v2 模型做形式化验证？哪些路径仍是论文 / 闭源 / 烂尾？」

## 1 chantakan/verified-mbse —— Lean 4 形式化

[chantakan/verified-mbse][^repo-verified-mbse]：Apache-2.0、2★、2026-04-23 last push、创建于 2026-04-10（极新）。

### 1.1 范围

**形式化范围有限**：不是 KerML / SysML v2 整个抽象语法的元模型形式化，而是对 KerML 的**核心子集**（Element / Feature / PartDef / Port / Connector / Specialization / Interpretation）做了带依赖类型语义的 Lean 4 编码（`VerifiedMBSE/Core/`），加上独立打造的 Kripke-LTL 行为框架（`Always` / `Eventually` / `Until` / `ProductKripke`），以及 V&V matrix 的依赖类型完整性定理。

### 1.2 不是 translator

没有从 SysML v2 文本 / JSON 解析这一步——所有模型都得手写 Lean。定位是「Lean 库 + 卫星 case study」。

### 1.3 工程质量

库是单作者项目（chantakan，疑似日本航天背景，注释里大量日文，引用 ECSS-E-ST-10C 8 层）。`lake build` 报告 ~4450 行库 + 3630 行案例，**zero `sorry`**——是真正能编译的证明。

已证定理：每个 reachable 状态满足 invariant（归纳法）、parallel composition 的 well-formedness、N-way `composeMany`、V-Matrix 的 `Complete` 定理、HoTT 风的 `DesignSpace` quotient 上的 `ua` / `transport`。例子是航天器 EPS / AOCS / TCS / TTC 4 子系统。

### 1.4 判断

**早期 PoC**。技术质量很高（zero-sorry、依赖类型、Mathlib 加持），但缺 SysML v2 文本前端，缺生态联动，2 stars / 单作者——值得跟踪，但今天**没法直接用于工业 V&V matrix**。

## 2 tukcps/SysMD —— RPTU Notebook + AADD 求解器

[tukcps/SysMD][^repo-sysmd]：Apache-2.0、Kotlin/JVM、2026-04-21 v4.2.1、月度迭代、38★。

### 2.1 Solver 不是 SMT

它用的是同一团队自研的 [Multiplatform-AADD][^repo-aadd]：**Affine Arithmetic Decision Diagrams**——半符号区间 + LP（GLPK / Apache Commons Math）做约束传播，目标是给区间一个 over-approximation 或返回空集（不可满足）。

**这不是完备 SMT**——只解 SysML v2 表达式 + 约束 + 单位一致性，不解一阶逻辑。

### 2.2 能做的 vs 不能做的

| 能做 | 不能做 |
|---|---|
| value / units consistency | 状态机的时序属性 |
| 约束传播算缺失值 | refinement |
| requirement 满足性检查（早期分析阶段） | 行为等价 |

README 明说："automata and states might compile, but the constraint propagation mechanism does not use the respective parts properly"。

### 2.3 集成方式

Notebook UI（Markdown 单元 + 模型单元）+ 编译器把 model cell 翻译到 KerML metamodel + REST headless 模式（http://localhost:8081/swagger-ui）+ Gradle JLink 打包安装器。**Markdown 即文档即模型**，可邮件交换，是它最有特色的工程化点。

### 2.4 判断

**唯一稳定迭代的开源 SysML v2 工具之一**，有 RPTU + NXP + HOOD Group 的产业支撑。但 solver 能力是约束传播级别，不是定理证明级别——和 ISSE 论文对它的定位一致。**生产可用度（早期工程计算）**：可以用；**形式化验证**：不在它射程之内。

## 3 ftsrg/gamma —— BME 状态机验证框架

[ftsrg/gamma][^repo-gamma]：EPL、Xtend / Eclipse、2026-05-05 仍在 push（dev / master 都活跃）、35★、v2.12.0。

### 3.1 已 driven 的 verifiers

UPPAAL（timed automata）、Theta（FBK 系 CEGAR / IC3）、Spin（Promela）、nuXmv（symbolic FSM / IFSM）、Imandra、xSAP（safety）。覆盖时控、有界 / 无界、推理引擎都有。

### 3.2 关键发现：v2 前端代码未释放

**MODELS 2024 那篇 Molnár & Graics 的 SysML v2 前端没有合并进 ftsrg/gamma 主仓**——在 master 与 dev 树上 grep `sysml` / `SysML` 仅命中一个 PlantUML transformer 文件。SysML v2 前端是**论文级原型**，配套案例托管在 [ftsrg/isse-formal-methods-sysmlv2][^repo-isse-fm]（仅 SysML v2 *模型*，不含 transformer 源码，2★，2025-10 last push）。

也就是说"SysML v2 → Gamma → UPPAAL/Theta/nuXmv"的转换器**目前没开源**。Gamma 本体当生产工具用没问题（有 ICSE / JSME 论文背书），但 SysML v2 前端这一段**论文有，代码没放出来**，需要直接联系 BME 团队。

### 3.3 判断

**Gamma 本体生产成熟度高；SysML v2 前端 PoC、不可访问**。这是个明显的开放机会（见 [09-缺口与机会](09-gaps-opportunities.md) §C.3）。

## 4 Sireum HAMR —— 高保障代码生成

主仓 [sireum/hamr-sysml][^repo-hamr-sysml]（BSD-2、Scala/Slang、2026-04-17、1★）+ 辅助 [hamr-sysml-parser][^repo-hamr-parser]（2026-02-03、11★，仅 ANTLR4 grammar 包装）+ [aadl-gumbo][^repo-aadl-gumbo]（合约语言）+ [logika][^repo-logika]。

### 4.1 关系：借 AADL 语义

HAMR 把 SysML v2 的一个子集（足够表达 AADL 模型的部分）翻到和 AADL 共用的 IR，然后走 HAMR 既有的 Slang/JVM、C/Linux、**C/seL4** 代码生成路径。**它借的是 AADL 语义而不是 KerML 语义**——这是关键。

### 4.2 能形式化检查的

通过 GUMBO 合约（assume / guarantee）做 SMT 模型级集成检查 + 组件实现的合约符合性，由 Logika 作为 Slang 验证后端，底层是 SMT（Z3 / cvc4 / cvc5 通常）。

**信息流、调度可行性、deadlock 等不是显式声明的能力**——HAMR 自己页面[Sireum HAMR SysML v2][^hamr-home-sysmlv2]还在找钱"硬化原型支持工业化工作流"，所以是 **DARPA PROVERS / SBIR 经费驱动的 PoC**。

### 4.3 形式化机制层

Coq 没看到，但 **HAMR 运行时语义在 Isabelle 里有 mechanization**[^hamr-isabelle-2023]（用于 AADL semantics）。

### 4.4 真案例 + CI

[santoslab/sysmlv2-models][^repo-santoslab] 是 4 平台 GH Actions（Linux / macOS / Windows / CAmkES Docker）的真案例库，跑 sysml-isolette、temp-control-mixed-sel4-camkes 等模型——这是 **目前唯一一条端到端 SysML v2 → 形式化验证 → 可部署二进制**的开源链。其他全部是 stubs。Hardin 等 DASC 2025 论文[^hardin-2025]验证了 Rust + microkit 路径。

### 4.5 判断

**AADL 那条线已经非常工业化、已有 seL4 部署案例；SysML v2 这条线是 prototype，star 数极低，但有 Sireum / santoslab / Galois / Army SBIR 的稳定金主——值得作为「未来可用」项目跟踪**。

## 5 NEMO/UFES (UFO/OntoUML/gUFO) ↔ SysML v2

- [nemo-ufes/gufo][^repo-gufo] 是 UFO 的 OWL2-DL 实现；[OntoUML/ontouml-vp-plugin][^repo-ontouml-vp]是 Visual Paradigm 的 OntoUML 插件。
- **当前没有任何工具直接把 SysML v2 桥到 gUFO / OntoUML 校验器**。Almeida / Guizzardi 一脉的工作是**描述性语义分析**（如 *An Analysis of the Semantic Foundation of KerML and SysML v2*），指出 SysML v2 哪里和 UFO 不对齐，**没释放 SysML v2-to-gUFO validator**。
- 最接近的产业化路径是 [openCAESAR / OML][^opencaesar-oml]（JPL 出品），它有 [SysML v2 Ontology Project][^opencaesar-sysmlv2]把 SysML v2 metamodel 当作 OML vocabulary 用 DL reasoner 做一致性检查——这条线有代码、能跑。

## 6 OCL / 模型校验

- Pilot[^repo-pilot]的 `SysML.ecore` / `kerml.ecore` 内**带规范级 OCL 等价的 invariant**（如 `validateFeatureCrossFeatureType`），并有 Java 适配层做 runtime 校验。**没有外部 OSS rule pack**（安全 / MBSE 方法论合规）。
- 客户端也有：[sensmetry/sysml-2ls / Syside Legacy][^repo-sysml-2ls]的 `KerMLValidator` 实现规范一致性检查，但仍是规范级，不是领域级（ISO 26262 / DO-178C / NASA 7150）规则集。**领域 rule pack 是真空地带**。

## 7 时序逻辑性质规约

- 通过 Gamma 的 nuXmv / Theta 后端可以表达 LTL/CTL，但**前端缺失**（见 §3）。
- *Forsch. Ingenieurwes.* 2025[^forsching-ltl]的 LTL → SysML v2 状态机生成方向相反，且无开源代码。
- **没有 PSLPattern 或 Spec Patterns 风格的 SysML v2 性质 DSL**——这是空白。

## 8 定理证明器后端

| 工具 | 状态 |
|---|---|
| Coq / Rocq | **没有公开项目** |
| Isabelle/HOL | HAMR 运行时语义有，KerML / SysML v2 本身没有公开形式化 |
| F* / Dafny / TLA+ | GitHub 上 grep 不到 SysML v2 相关 |
| Lean 4 | `chantakan/verified-mbse` 是孤本（§1） |
| Imandra / IML | **闭源商业**[^imandra]——SysML v2 → IML transpiler 但需 enterprise license |

## 9 符号执行 / 抽象解释

没有把 SysML v2 行为模型送进 KLEE / angr / CBMC 风格引擎的开源工作。SysMD 的 AADD 算最接近抽象解释（区间域上的约束传播），但仅用于代数约束（见 §2）。

## 10 政府资助工具

- **[Monterey Phoenix Firebird][^repo-monterey]**（GitLab、NPS 出品、Giammarco 接手 Auguston）是**开源、政府资助、活跃**的：基于事件 trace 的有界穷尽行为分析，不是经典 model checker，更接近"小规模行为可视化 + 异常检测"。SysML v2 model collection 已开放。
- **Stevens / Collins Aerospace** 的相关工作（ACL2、ASTM F3269）**没有 SysML v2 专用 OSS 释出**——Collins 的高保障线主要走 AADL + Logika。
- **NPS DAIR 2024**[^nps-dair-2024]是政策报告，无工具释出。

## 11 Living SysML v2 Blueprint

[SBMF 2025 paper][^teodorov-2025]是**纯 research agenda / position paper**，目前没有公开代码或原型。Teodorov 团队（ENSTA Bretagne）此前有 OBP2 model checker，但和 SysML v2 还没拼接。**今天没法用**。

## 12 综合判断与机会缺口

老实说，能投入工业项目的 SysML v2 形式化基建数量是**个位数**，且都不全：

1. **没有完整的 SMT 后端 satisfiability checker for SysML v2 part definitions / constraints**——SysMD 的 AADD 解决简单代数，但 BV / array / quantifier 都不支持。一个走 Z3 / cvc5 + SysML v2 API 的 part 实例存在性检查器是空白。
2. **没有公开的 SysML v2 → LTL/CTL 性质语言 DSL**。Gamma 可以但前端没开源；Imandra 可以但闭源。
3. **没有 hybrid / probabilistic 扩展验证器**（PRISM / Storm / dReal / SpaceEx 都没和 SysML v2 拼）。SAWS² / xSAP 走 fault tree，不走 hybrid。
4. **没有领域 rule pack**（ISO 26262 ASIL decomposition、DO-178C objective traceability、ARP4754A）的 OSS SysML v2 实现。`isse-formal-methods-sysmlv2` 只是案例模型。
5. **SysML v2 → Coq / Isabelle 的 deep embedding 是真空**。`verified-mbse` 是 shallow embedding 且自己手写 Lean，不解析 SysML v2 文本。
6. **Gamma SysML v2 frontend 不开源**——这是论文 → 工具的最近距离机会，但需要 BME 配合或自己重造。

## 13 实际可用路径推荐

如果今天要在 SysML v2 上跑形式化验证，唯一**真能跑通端到端、开源、有维护**的路径是：

1. **HAMR + GUMBO + Logika**（受限于 AADL 子集），见 §4。
2. **Imandra**（受限于商业授权），见 §8。
3. **SysMD**（做 type / units / constraint 一致性），见 §2。

其他都还在 PoC 或论文阶段。详尽的机会窗口分析见 [09-缺口与机会](09-gaps-opportunities.md) §C。

## 参考文献

[^repo-verified-mbse]: *chantakan/verified-mbse*. <https://github.com/chantakan/verified-mbse>

[^repo-sysmd]: *tukcps/SysMD*. <https://github.com/tukcps/SysMD>

[^repo-aadd]: *tukcps/Multiplatform-AADD*. <https://github.com/tukcps/Multiplatform-AADD>

[^repo-gamma]: *ftsrg/gamma*. <https://github.com/ftsrg/gamma>

[^repo-isse-fm]: *ftsrg/isse-formal-methods-sysmlv2*. <https://github.com/ftsrg/isse-formal-methods-sysmlv2>

[^repo-hamr-sysml]: *sireum/hamr-sysml*. <https://github.com/sireum/hamr-sysml>

[^repo-hamr-parser]: *sireum/hamr-sysml-parser*. <https://github.com/sireum/hamr-sysml-parser>

[^repo-aadl-gumbo]: *sireum/aadl-gumbo*. <https://github.com/sireum/aadl-gumbo>

[^repo-logika]: *sireum/logika*. <https://github.com/sireum/logika>

[^hamr-home-sysmlv2]: Sireum HAMR for SysML v2. <https://sireum.org/hamr-sysmlv2/>

[^hamr-isabelle-2023]: *HAMR Runtime Semantics in Isabelle/HOL*. Springer, 2023. <https://link.springer.com/chapter/10.1007/978-3-031-52183-6_3>

[^repo-santoslab]: *santoslab/sysmlv2-models*. <https://github.com/santoslab/sysmlv2-models>

[^hardin-2025]: Hardin, D. 等. *Trustworthy Systems Engineering with SysML v2, AADL, and HAMR on seL4 microkit*. DASC 2025. <https://loonwerks.com/publications/pdf/hardin2025dasc.pdf>

[^repo-gufo]: *nemo-ufes/gufo*. <https://github.com/nemo-ufes/gufo>

[^repo-ontouml-vp]: *OntoUML/ontouml-vp-plugin*. <https://github.com/OntoUML/ontouml-vp-plugin>

[^opencaesar-oml]: *opencaesar/oml*. <https://github.com/opencaesar/oml>

[^opencaesar-sysmlv2]: openCAESAR. *SysML v2 Ontology Project*. <https://www.opencaesar.io/projects/2023-8-11-SysML-v2-Ontology.html>

[^repo-pilot]: *Systems-Modeling/SysML-v2-Pilot-Implementation*. <https://github.com/Systems-Modeling/SysML-v2-Pilot-Implementation>

[^repo-sysml-2ls]: *sensmetry/sysml-2ls*. <https://github.com/sensmetry/sysml-2ls>

[^forsching-ltl]: *Enhancing Model-Based Development with Formalized Requirements*. Forschung im Ingenieurwesen, 2025. <https://link.springer.com/article/10.1007/s10010-025-00806-1>

[^imandra]: Imandra. *SysML v2 Solution*（闭源）。<https://www.imandra.ai/sysml>

[^repo-monterey]: NPS. *Monterey Phoenix Firebird — SysML v2 Behavior Model Collection*. <https://gitlab.nps.edu/monterey-phoenix/sysml-v2-behavior-model-collection>

[^nps-dair-2024]: NPS DAIR. *Leveraging Generative AI to Build, Modify, and Query MBSE Models*. SYM-AM-24-138. <https://dair.nps.edu/bitstream/123456789/5237/1/SYM-AM-24-138.pdf>

[^teodorov-2025]: Teodorov, C. 等. *A Research Agenda for the Living SysML V2 Blueprint*. SBMF 2025. <https://link.springer.com/chapter/10.1007/978-3-032-12086-1_4>
