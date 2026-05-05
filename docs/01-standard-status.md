# 01 标准状态：OMG SysML v2 与 KerML

## 本章简介

本章梳理 OMG SysML v2.0、Kernel Modeling Language (KerML) 1.0 与 Systems Modeling API & Services 1.0 三份规范的法律状态、文档结构、与版本演进；解释 KerML 与 SysML v2 的语言分层关系（这是与 SysML v1 最大的架构差异）；列出 v1 → v2 迁移规范的实现现状。读者读完本章应能回答：「我现在引用 SysML v2 的哪个文档？KerML 与 SysML v2 是什么关系？API & Services 标准能不能直接用？v1 模型怎么迁移？」

## 1 法律状态与时间线

OMG 于 **2025-06-30** 完成对 SysML v2.0、KerML 1.0 与 Systems Modeling API & Services 1.0 的 **Final Adoption**，并在 2025-07-21 发布对外公告[^omg-final]。三份规范在 2025 下半年进入 OMG 的 **Finalization Task Force (FTF)** 阶段进行最后整理后正式出版。

历史归档（Beta1 2024-09、Beta2 2025-03 等）仍可在 OMG 网站访问，便于研究者追溯演进轨迹[^omg-beta1][^omg-beta2]。Final 版本与 Beta2 在文本表示与 KerML 元模型层面已无破坏性变更——主要差异是术语统一与 Annex 整理。

| 阶段 | 状态 | 链接 |
|---|---|---|
| 立项 | OMG SysML v2 RFP，2017 | [omg.org/sysml/sysmlv2](https://www.omg.org/sysml/sysmlv2/) |
| Beta1 | 2024-09 公开评审 | [omg.org/spec/SysML/2.0/Beta1](https://www.omg.org/spec/SysML/2.0/Beta1/About-SysML)[^omg-beta1] |
| Beta2 | 2025-03 公开评审 | [omg.org/spec/SysML/2.0/Beta2](https://www.omg.org/spec/SysML/2.0/Beta2/About-SysML)[^omg-beta2] |
| **Final Adoption** | **2025-06-30** | 公告 2025-07-21[^omg-final] |
| 出版 | 2025-09 | [omg.org/sysml/sysmlv2](https://www.omg.org/sysml/sysmlv2/) |
| RTF 修订 | 持续 | OMG Revision Task Force 周期性小修订 |

## 2 规范文档结构

OMG 发布的 SysML v2 是一组规范的统称。读者在引用时应区分各 part：

### 2.1 SysML v2 Part 1：抽象语法 + 文本表示 + 图形表示

定义 SysML v2 的抽象语法（基于 KerML 元模型继承）、文本表示（`*.sysml` 源文件）、图形表示（视图族，包括 General View / Action Flow / State Transition / Interconnection / Tree / Requirements Table 等）[^omg-spec]。

### 2.2 SysML v2 Part 2：扩展库

附带的标准 model libraries：单位制（SI Units）、几何、几何代数、量纲分析、运动学、AADL 集成 library 等。**注意**：AADL Library 仅是 SysML v2 写出的 AADL 概念集合，**不是** AADL ↔ SysML v2 的转换器[^repo-aadl-release]。

### 2.3 SysML v2 Part 4：v1 → v2 Transformation

OMG 2025-09 出版的 *SysML v1 to v2 Transformation Specification*[^omg-transform] 给出从 SysML v1（含 UML profile + XMI）到 SysML v2 的元模型映射规则。**实现现状（2026-05）**：

- 规范明文承认 "reference implementation not currently available"。
- Pilot Implementation 仓库[^repo-pilot]中的 `org.omg.sysml.uml.ecore.importer` 子模块**只是 Ecore 元模型导入器**，**不是** v1 实例 → v2 实例迁移工具。
- 美国国防部 OUSD(R&E) 2024-03 发布的 *SysML v1 to SysML v2 Model Conversion Approach* v1.3[^dod-cto]提供了实施蓝图，但同样仅是方法学。
- Sensmetry 在 *DETECT* 案例研究中明确说迁移过程"主要为人工"[^sensmetry-detect]。

这是基础设施侧的**明显空白点**——见 [09-缺口与机会](09-gaps-opportunities.md) §B.5。

### 2.4 KerML 1.0：Kernel Modeling Language

KerML 是 SysML v2 的**底层元模型 + 文本/图形语法**，定义了 Feature、Type、Specialization、Connector、Behavior、Occurrence 等核心概念[^omg-kerml]。SysML v2 通过 KerML 的语言扩展机制定义 Part、Port、Action、State 等系统工程概念。这是与 SysML v1 最大的架构差异（详见 §3）。

### 2.5 Systems Modeling API & Services 1.0

OMG 同时发布了与 SysML v2 配套的 REST API 规范[^omg-api]——这是 **OMG 标准家族中第一个把 web API 作为一等公民**纳入语言规范的尝试。规范包括：

- 资源模型：Project、Branch、Tag、Commit、Element、Relationship、Query。
- HTTP PSM：Play/Postgres 参考实现[^repo-api-services]、Python client[^repo-api-py]、Java client[^repo-api-java]。
- 替代 PSM：Open MBEE Flexo MMS 提供 RDF/SPARQL PSM[^repo-flexo-mms]。

但规范本身**没有定义 merge 端点 / 三方合并算法 / 冲突解决**——这是规范级别的空白，所有商用与开源工具的 merge 实现都是私有不兼容的[^omg-api]。详见 [06-可视化与协作](06-visualization-collaboration.md) §2。

## 3 KerML 与 SysML v2 的语言分层架构

理解 KerML/SysML v2 的最关键概念：**SysML v2 不是 UML profile**，而是**通过 KerML 语言机制扩展出的领域语言**。架构对照如下。

### 3.1 SysML v1（UML profile 模式）

```
[UML 2.x metamodel + Profile mechanism]
        │
        ↓ apply <<profile>>
[SysML v1 Profile (stereotypes + tagged values)]
        │
        ↓ instance
[用户模型 (XMI)]
```

后果：v1 的 Block / Requirement / Allocate 等都是 UML 类的 stereotype，元模型语义"叠"在 UML 之上，导致：(a) 工具实现复杂度高；(b) 模型互操作性差（XMI 1.x/2.x、不同 vendor 的 stereotype 不一致）；(c) 形式化困难。

### 3.2 SysML v2（KerML 扩展模式）

```
[KerML metamodel: Feature / Type / Specialization / Connector / Behavior / Occurrence]
        │
        ↓ language extension
[SysML v2: PartDef / Port / ActionDef / StateDef / RequirementDef / Allocation ...]
        │
        ↓ instance
[用户模型 (.sysml 文本 + JSON-LD via API)]
```

后果：

1. **核心语义独立**——KerML 的 Feature/Specialization/Subsetting 是干净的"分类语义"，可以被独立形式化（Almeida 等基于 UFO 的 4D 时空语义批评就建立在这一点上[^almeida-2024]）。
2. **元模型继承而非 stereotype**——SysML v2 的 PartDef 不是 UML Class 的 stereotype，而是 KerML Type 的特化，避免了 v1 双层语义堆叠。
3. **文本一等**——KerML/SysML v2 同时定义文本与图形表示，文本是规范级一等公民（这与 v1 的"文本是工具私有 trick"截然不同）。
4. **API 在标准里**——见 §2.5。

工程影响：本仓库 [04-解析 IDE](04-parsing-ide-infrastructure.md) 中所有 6 套独立 parser 都是 KerML/SysML 双层语法分模块（Pilot 的 `org.omg.kerml.xtext` + `org.omg.sysml.xtext`、Langium 的 `KerML.langium` + `SysML.langium`、MontiCore 的 12 个 `.mc4` 文件分层、daltskin 的 `KerML.g4` + `SysML.g4`）——这是直接对应 §3.2 架构的物理产物。

## 4 文本表示与 KEBNF

SysML v2 的文本表示由 OMG **KEBNF** (Kernel Extended BNF) 形式定义。KEBNF 是一种带继承和模板的 EBNF 变体，本身在 KerML 规范的 Annex 中给出元定义。

KEBNF → 实现的转换路径有两条主流：

1. **手工 ANTLR/Xtext/Langium 文法**：Pilot、SysIDE Legacy、daltskin 走这条路，但需手工同步规范变更。Pilot 的 SysML grammar 单文件 2438 行；SysIDE 的 KerML+SysML `.langium` 共 3371 行；MontiCore 拆 12 文件最细粒度。
2. **KEBNF 自动生成**：daltskin 的 `make generate` 流程从 [SysML-v2-Release][^repo-release] 拉取规范 BNF 后转 ANTLR4，配合 26 KB 的 `PATCHES.md` 修补差异[^repo-daltskin-grammar]；nomograph-ai/kebnf 是更专业的 KEBNF → ANTLR4 / tree-sitter 转换器[^repo-nomograph-kebnf]。

详见 [04-解析 IDE](04-parsing-ide-infrastructure.md) §1。

## 5 图形表示族

SysML v2 Part 1 规范了若干视图类型，与 v1 的 9 类视图相比有显著重组：

| v2 视图 | 大致对应 v1 | 备注 |
|---|---|---|
| General View / Standard Diagram | BDD + Package Diagram 合并 | "通用视图"，多数 v1 元素归并到此 |
| Interconnection View | IBD | 端口连接 |
| Action Flow View | Activity Diagram | 流程语义清晰化 |
| State Transition View | State Machine Diagram | 引入 Occurrence 语义 |
| Sequence View | Sequence Diagram | 与 Action 整合 |
| Use Case View | Use Case Diagram | 显式 Stakeholder/Concern |
| Tree Explorer View | — | 新增，模型导航 |
| Requirements Table View | — | 新增，表格化 |

工具实现进度并不一致：Eclipse SysON 截至 `v2026.3.0` 实现了 General / Action Flow / State Transition / Interconnection / Tree Explorer / Requirements Table 共 6 类，**未实现** Use Case 与 Sequence；Pilot 的 PlantUML visualizer 通过 Visitor 模式覆盖 v1 类的 9 类视图但**只读**。详见 [06-可视化与协作](06-visualization-collaboration.md)。

## 6 RTF 修订与版本管理

OMG 在 Final Adoption 之后进入 **Revision Task Force (RTF)** 周期：

- 业务模式：每 1–2 年发布一次 minor 修订（如 1.0.1、1.1）。
- 修订内容：Issue 库（OMG 公开 jira）汇集的 spec bug、术语统一、向后兼容的语义澄清。
- 对工具链影响：Pilot Implementation 通常滞后 RTF 修订 3–6 个月；商用厂商滞后 6–12 个月。

本仓库引用规范条款时应以**截至维护日期最新的 spec 版本号**为准（见 [AGENTS.md §7](../AGENTS.md)）。

## 参考文献

[^omg-final]: OMG. *Final Adoption: SysML v2.0, KerML v1.0, Systems Modeling API & Services v1.0*. 2025-07-21. <https://www.omg.org/news/releases/pr2025/07-21-25.htm>

[^omg-spec]: OMG. *Systems Modeling Language v2.0*. <https://www.omg.org/sysml/sysmlv2/>

[^omg-kerml]: OMG. *Kernel Modeling Language (KerML) v1.0*. <https://www.omg.org/spec/KerML>

[^omg-api]: OMG. *Systems Modeling API & Services v1.0 Beta1*. <https://www.omg.org/spec/SystemsModelingAPI/1.0/Beta1/PDF>

[^omg-transform]: OMG. *SysML v2 Part 4: v1 to v2 Transformation Specification (Beta1)*. <https://www.omg.org/spec/SysML/2.0/Beta1/Transformation/PDF>

[^omg-beta1]: OMG. *SysML v2 Beta1 (Historical)*. <https://www.omg.org/spec/SysML/2.0/Beta1/About-SysML>

[^omg-beta2]: OMG. *SysML v2 Beta2 (Historical)*. <https://www.omg.org/spec/SysML/2.0/Beta2/About-SysML>

[^dod-cto]: U.S. OUSD(R&E). *SysML v1 to SysML v2 Model Conversion Approach v1.3*. 2024-03. <https://www.cto.mil/wp-content/uploads/2025/02/SysML-v2-TransitionApproach-1.3.pdf>

[^almeida-2024]: Almeida, J.P.A. 等. *An Analysis of the Semantic Foundation of KerML and SysML v2*. ER 2024. <https://link.springer.com/chapter/10.1007/978-3-031-75872-0_8>

[^repo-pilot]: *Systems-Modeling/SysML-v2-Pilot-Implementation*. <https://github.com/Systems-Modeling/SysML-v2-Pilot-Implementation>

[^repo-release]: *Systems-Modeling/SysML-v2-Release*. <https://github.com/Systems-Modeling/SysML-v2-Release>

[^repo-aadl-release]: *Systems-Modeling/SysML-v2-AADL-Release*（library，非 transformer）。<https://github.com/Systems-Modeling/SysML-v2-AADL-Release>

[^repo-api-services]: *Systems-Modeling/SysML-v2-API-Services*. <https://github.com/Systems-Modeling/SysML-v2-API-Services>

[^repo-api-py]: *Systems-Modeling/SysML-v2-API-Python-Client*. <https://github.com/Systems-Modeling/SysML-v2-API-Python-Client>

[^repo-api-java]: *Systems-Modeling/SysML-v2-API-Java-Client*. <https://github.com/Systems-Modeling/SysML-v2-API-Java-Client>

[^repo-flexo-mms]: *Open-MBEE/flexo-mms-sysmlv2*. <https://github.com/Open-MBEE/flexo-mms-sysmlv2>

[^repo-daltskin-grammar]: *daltskin/sysml-v2-grammar*. <https://github.com/daltskin/sysml-v2-grammar>

[^repo-nomograph-kebnf]: *nomograph-ai/kebnf*. <https://github.com/nomograph-ai/kebnf>

[^sensmetry-detect]: Sensmetry. *DETECT Case Study: SysML v1 → v2 Migration Lessons Learned*. <https://sensmetry.com/sysml-v1-to-sysml-v2-migration-of-detect-benefits-lessons-learned/>
