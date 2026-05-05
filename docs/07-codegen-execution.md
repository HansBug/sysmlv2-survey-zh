# 07 代码生成、转换、执行与仿真基础设施

## 本章简介

本章评估 SysML v2 的**模型转换、代码生成、行为执行、仿真桥接、CI/CD 与 linter**基础设施现状。每条路径标记 **Production-ready / Beta / PoC / Vapor / Spec-only** 五档可用度。读完本章可回答：「我能不能从 SysML v2 模型生成 Modelica / Simulink / 代码？能不能跑状态机？v1 能迁移过来吗？有没有 SysML v2 lint 工具？」

## 1 模型转换与代码生成

### 1.1 SysML v2 → AADL —— **PoC（仅 library，无 transformer）**

[Systems-Modeling/SysML-v2-AADL-Release][^repo-aadl-release]（CC-BY-ND、8★、2026-03 推送）只是一组 `.sysml` 文件（`AADL.sysml`、`Thread_Properties.sysml` 等），是一个 **profile / library**——让你用 SysML v2 写出 AADL 概念，**没有任何 AADL → SysML 或反向的代码转换器**。README 明确写着 "AADL flows / modes 尚未翻译"。

[sireum/hamr-sysml-parser][^repo-hamr-parser]也只是 ANTLR 生成物的二次发布。Litwin 等人的 SAE AeroTech 2024 论文[^litwin-2024]**没有发布 transformer 代码**——纯方法学论文。

### 1.2 SysML v2 → Modelica / FMI / Simulink —— **基本 Vapor**

- Zimmermann 等 [LNCS 2024 论文][^zimmermann-2024]没有公开仓库。
- 唯一开源的相关项目是 **2014 年遗留的** [SysMLModelicaIntegration/edu.gatech.mbse.mdsysmlmodelica][^repo-gatech]（针对 SysML **v1** + MagicDraw）。
- Pepper 等 [INCOSE 2024 双向转换论文][^pepper-2024]也无代码。
- OpenModelica / JModelica 仍只到 SysML v1。

**机会**：**开源的 SysML v2 → Modelica / FMU 直生成器目前不存在**——见 [09-缺口与机会](09-gaps-opportunities.md) §B.1。

### 1.3 SysML v2 → OWL / RDF —— **Beta，是真转换器**

Pilot 里 [`org.omg.kerml.owl/transforms/SysML2OWL.qvto`][^pilot-owl-qvt]（12.7 KB，QVT-Operational）+ `KerML2OWL.java` 调度器。LGPL-3.0。生成 OWL 2 functional syntax（Class / ObjectProperty / Annotation 全套）。`owl/` 下有真实样例输出（`Parts Tree Demo.owl` 等）。

但**几乎没人用**——GitHub 全网搜不到下游的知识图谱项目消费它。社区似乎倾向于直接走 SysML v2 REST API + 自写 RDF 映射。

### 1.4 SysML v2 → 实现代码 —— **HAMR 是唯一真选项（Beta）**

[sireum/hamr-sysml][^repo-hamr-sysml]（BSD-2-Clause、Scala/Slang、2026-04-17、1★）有 `frontend/jvm/.../hamr/sysml/instantiation/Instantiate.scala`，把 SysML v2 实例化进 HAMR AIR IR；下游通过 [sireum/hamr-codegen][^repo-hamr-codegen]输出 **Slang / Scala / C / Rust**，部署到 JVM / Linux / seL4 microkit。

Hardin 等 [DASC 2025][^hardin-2025]验证了 Rust + microkit 路径。证据是 [santoslab/sysmlv2-models][^repo-santoslab]：4 条 GH Actions（linux / macOS / windows / CAmkES Docker），跑 sysml-isolette、temp-control-mixed-sel4-camkes 等真模型。

**这是目前唯一一条端到端 SysML v2 → 形式化验证 → 可部署二进制**的开源链。其他全部是 stubs。详见 [05-形式化与验证 §4](05-formal-verification.md#4-sireum-hamr--高保障代码生成)。

### 1.5 UML → SysML v2 / SysML v1 → v2 —— **Spec only**

Pilot 中 [`org.omg.sysml.uml.ecore.importer`][^pilot-uml-importer]只有两个 Java 文件（CustomUML2EcoreConverter、CustomUMLImporter），是 Ecore 元模型导入器，**不是 UML 实例 → SysML v2 实例迁移**。OMG [Part 4 v1 → v2 Transformation 规范][^omg-transform]和 [DoD CTO 2024 报告][^dod-cto]都明文承认「reference implementation not currently available」。Sensmetry 在 [DETECT 案例][^sensmetry-detect]是手工迁移。

**这是个明确的开源空白点**，见 [09-缺口与机会](09-gaps-opportunities.md) §B.5。

### 1.6 通用 M2M（ATL / QVT / Henshin / VIATRA）—— **零（除了 Pilot 内置 QVTo）**

除上面 OWL 用到的 QVTo 外，没有公开的 ATL / Henshin / VIATRA on KerML 绑定。学术界 [QVT-Based v2 Diff Transformation][^qvt-diff]（IEEE 2024）算尝试，但无代码。[matetamasi/kreate][^repo-kreate]把 KerML 翻译到 Refinery（图变换 / 约束求解器）—— PoC，0 stars。

## 2 执行与仿真

### 2.1 行为执行 —— **几乎不存在真执行**

| 工具 | 真实能力 |
|---|---|
| **Pilot 的 "interpreter"**（[`org.omg.sysml.execution`][^pilot-exec]） | 只是 **expression evaluator**——三角函数、序列操作、reduce/forAll。Jupyter `%eval` magic 只能算 `1 + sin(x)`，**不跑 state machine、不跑 action**。 |
| **SysMD**[^repo-sysmd]（38★） | solver 是**值 / 单位约束传播**（基于他们自己的 AADD 范围算术库），README 自己说 "automata and states 编译过但约束传播不正确处理"——**不是状态机执行器**。 |
| **PyMBE**（[sanbales/pymbe][^repo-pymbe]、GPL-3.0、5★） | 有 `execute_kerml_atoms.py`（49 KB）做 KerML occurrence/atom 解释，**最接近"真执行语义"的开源实现**。但**自 2024-10 停滞**。 |
| **Syside Automator** Sismic 桥（Sensmetry [Lesson 19][^sensmetry-lesson-19]） | **闭源商业** |
| Teodorov [SBMF 2025 "Living SysML"][^teodorov-2025] | Research agenda paper，**零代码** |

### 2.2 离散事件（SimPy / OMNeT++ / NS-3）—— **Vapor**

[se-lib][^repo-se-lib]是 Python DSL 仿 SysML 概念，**不消费 SysML v2 文本**。

### 2.3 连续 / 混合 / Co-simulation —— Pilot / SysON 都靠"导出到 MATLAB"占位

[SysON 文档明说][^syson-sim]——**无开源 FMI master**。

### 2.4 参数 / 约束求解

**SysMD 是唯一开源 ParaMagic 替代**，但只做静态约束，不做 trade-study / optimization。Intercax ParaMagic 仍然闭源。

## 3 流水线与工具链

### 3.1 CI/CD —— **真但都是私有化样例**

| 项目 | 说明 |
|---|---|
| [santoslab/sysmlv2-models][^repo-santoslab] | **最完整的 v2 GH Actions 真案例**：4 平台 + CAmkES Docker（`trustworthysystems/camkes`） |
| Sensmetry [Lesson 20][^sensmetry-lesson-20] | 用 `python -m syside check`——**依赖商业 Syside Automator** |
| [Westfall-io/windtrader][^repo-windtrader]（MIT、0★） | Python 调 Pilot 做 parse-only 校验 |
| [kenji-miyake/sysml-v2-docker][^repo-sysml-docker]  | **已 archive** |

**机会**：没有 `uses: setup-sysmlv2@v1` 这种 reusable GitHub Action。

### 3.2 Bazel / CMake —— **零**

[sensmetry/sysand][^repo-sysand]目前是事实标准包管理器，提供 Python / Java bindings 和 prek/zizmor CI 支持，但只做 KPAR project interchange + checksum，**不调 validator / linter**。

### 3.3 Linter / style checker —— **不存在**

Xtext / Pilot 只做语法 + 约束 OCL 校验。**无 ESLint-for-SysML**。**这是最明显的 opportunity window**——见 [09-缺口与机会](09-gaps-opportunities.md) §A.1。

### 3.4 代码 ↔ 模型双向同步 —— **零**

HAMR 是单向 model → code（developer 在生成的 skeleton 里填业务逻辑，不回灌）。商业的 Visual Paradigm / EA 仍只到 v1。

## 4 综合判断与缺口清单

可生产部署的端到端 codegen 路径**只有一条**（HAMR），且受限于 AADL 子集；其他多数转换路径都在 PoC 或 Vapor 阶段。具体缺口（按"应该有但没有"排序）：

1. **SysML v2 → Modelica/FMU 开源生成器**（最大的实用缺口；写一个 QVTo 或 Python+Jinja 都能填）。
2. **可复用 GitHub Action**（`actions/setup-sysmlv2`、`actions/sysmlv2-validate`）+ 维护中的官方 Docker 镜像。
3. **ESLint 风格的 SysML v2 linter**（命名、可达性、deprecated keyword、library 一致性规则）。
4. **OSS 版 ParaMagic for v2**（绑 OpenModelica / SciPy.optimize，PyMBE 想做但已停滞）。
5. **DoD CTO Part 4 v1 → v2 transformation 的 reference 实现**（OMG / DoD 都说"待实现"）。
6. **代码 ↔ v2 round-trip 同步器**（即便只是 C++ header → SysML part 单向也基本无人做）。
7. **真正能跑 state machine 的开源 SysML v2 simulator**（PyMBE 是基础但需要 maintainer；Syside Sismic 桥的开源平替）。
8. **更多 codegen target**（Rust / Go / Python ABI、protobuf / OpenAPI / GraphQL schema 生成）。

详尽的机会窗口分析见 [09-缺口与机会](09-gaps-opportunities.md)。

## 5 当前真可用路径

| 用例 | 推荐组合 |
|---|---|
| 高保障代码 / seL4 部署 / 嵌入式 | HAMR + GUMBO + Logika（限 v2 ∩ AADL 子集，§1.4） |
| 知识图谱 / RDF 互操作 | Pilot QVTo `SysML2OWL` + opencaesar OML（§1.3） |
| 单位 / 约束 / 早期工程计算 | SysMD + AADD（§2.1） |
| CI 校验 | santoslab CI 模板（§3.1） |
| 行为执行（如还有人维护） | PyMBE（§2.1，**已停滞**） |

## 参考文献

[^repo-aadl-release]: *Systems-Modeling/SysML-v2-AADL-Release*. <https://github.com/Systems-Modeling/SysML-v2-AADL-Release>

[^repo-hamr-parser]: *sireum/hamr-sysml-parser*. <https://github.com/sireum/hamr-sysml-parser>

[^litwin-2024]: Litwin, K. 等. *Transforming AADL Models Into SysML 2.0*. SAE AeroTech 2024. <https://loonwerks.com/publications/pdf/litwin2024aerotech.pdf>

[^zimmermann-2024]: *SysML v2 for Automated Co-simulation from Systems Architecture Models*. Springer LNCS, 2024. <https://link.springer.com/chapter/10.1007/978-3-031-62554-1_4>

[^repo-gatech]: *SysMLModelicaIntegration/edu.gatech.mbse.mdsysmlmodelica*（v1，2014）。<https://github.com/SysMLModelicaIntegration/edu.gatech.mbse.mdsysmlmodelica>

[^pepper-2024]: *Bidirectional SysML v2 ↔ Modelica Transformation*. INCOSE IS 2024. <https://incose.onlinelibrary.wiley.com/doi/abs/10.1002/iis2.13239>

[^pilot-owl-qvt]: Pilot `SysML2OWL.qvto`. <https://github.com/Systems-Modeling/SysML-v2-Pilot-Implementation/blob/master/org.omg.kerml.owl/transforms/SysML2OWL.qvto>

[^repo-hamr-sysml]: *sireum/hamr-sysml*. <https://github.com/sireum/hamr-sysml>

[^repo-hamr-codegen]: *sireum/hamr-codegen*. <https://github.com/sireum/hamr-codegen>

[^hardin-2025]: Hardin, D. 等. *Trustworthy Systems Engineering with SysML v2, AADL, and HAMR on seL4 microkit*. DASC 2025. <https://loonwerks.com/publications/pdf/hardin2025dasc.pdf>

[^repo-santoslab]: *santoslab/sysmlv2-models*. <https://github.com/santoslab/sysmlv2-models>

[^pilot-uml-importer]: Pilot `org.omg.sysml.uml.ecore.importer`. <https://github.com/Systems-Modeling/SysML-v2-Pilot-Implementation/tree/master/org.omg.sysml.uml.ecore.importer>

[^omg-transform]: OMG. *SysML v2 Part 4: v1 to v2 Transformation Specification (Beta1)*. <https://www.omg.org/spec/SysML/2.0/Beta1/Transformation/PDF>

[^dod-cto]: U.S. OUSD(R&E). *SysML v1 to SysML v2 Model Conversion Approach v1.3*. 2024-03. <https://www.cto.mil/wp-content/uploads/2025/02/SysML-v2-TransitionApproach-1.3.pdf>

[^sensmetry-detect]: Sensmetry. *DETECT Case Study: SysML v1 → v2 Migration Lessons Learned*. <https://sensmetry.com/sysml-v1-to-sysml-v2-migration-of-detect-benefits-lessons-learned/>

[^qvt-diff]: *QVT-Based SysML v2 Diff Transformation*. IEEE, 2024. <https://ieeexplore.ieee.org/document/10864958/>

[^repo-kreate]: *matetamasi/kreate*. <https://github.com/matetamasi/kreate>

[^pilot-exec]: Pilot `org.omg.sysml.execution`. <https://github.com/Systems-Modeling/SysML-v2-Pilot-Implementation/tree/master/org.omg.sysml.execution>

[^repo-sysmd]: *tukcps/SysMD*. <https://github.com/tukcps/SysMD>

[^repo-pymbe]: *sanbales/pymbe*. <https://github.com/sanbales/pymbe>

[^sensmetry-lesson-19]: Sensmetry. *Lesson 19 — State Machine Simulation (Sismic)*. <https://sensmetry.com/advent-of-sysml-v2-lesson-19-state-machine-simulation/>

[^teodorov-2025]: Teodorov, C. 等. *A Research Agenda for the Living SysML V2 Blueprint*. SBMF 2025. <https://link.springer.com/chapter/10.1007/978-3-032-12086-1_4>

[^repo-se-lib]: *se-lib/se-lib*. <https://github.com/se-lib/se-lib>

[^syson-sim]: SysON simulation doc. <https://doc.mbse-syson.org/syson/main/user-manual/features/simulation.html>

[^sensmetry-lesson-20]: Sensmetry. *Lesson 20 — CI/CD for SysML v2 Models*. <https://sensmetry.com/advent-of-sysml-v2-lesson-20-ci-cd-for-sysml-v2-models/>

[^repo-windtrader]: *Westfall-io/windtrader*. <https://github.com/Westfall-io/windtrader>

[^repo-sysml-docker]: *kenji-miyake/sysml-v2-docker*（archived）。<https://github.com/kenji-miyake/sysml-v2-docker>

[^repo-sysand]: *sensmetry/sysand*. <https://github.com/sensmetry/sysand>
