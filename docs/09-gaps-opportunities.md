# 09 缺口与机会窗口

## 本章简介

本章把前面各章识别出的开源空白做集中归类，按**短中长期投入难度**与**按基础设施维度**两套坐标轴交叉组织，给资深工程师 / 学术研究者一份「下一个该做什么」的清单。每条机会附**已存在的相关项目**（避免重复造轮子）、**预计投入规模**与**最近能借鉴的同类语言生态先例**。

> 本章是 [04-解析 IDE](04-parsing-ide-infrastructure.md) §6、[05-形式化与验证](05-formal-verification.md) §12、[06-可视化与协作](06-visualization-collaboration.md) §8、[07-代码生成与执行](07-codegen-execution.md) §4 的合并视图，可作为立项 brainstorm 起点。

## A 短期机会（个人 / 小团队，1–3 月）

### A.1 ESLint 风格 SysML v2 linter

**空白**：Pilot / SysIDE Legacy / daltskin 都有规范级一致性校验（OCL 等价 invariant），但没有领域级 / 风格级 linter。UML / v1 同样没有专门 linter，**这是 SysML v2 反而能比前辈先做的窗口**。

**规则集建议**：

- 命名约定：PartDef / RequirementDef / Action 命名空间约束。
- 可达性：检查 unused features、孤立 PartDef。
- Deprecated keyword 使用警告。
- Library 一致性（multiple library imports 的冲突）。
- KerML / SysML 跨层使用警告（不应在 KerML 文件中用 SysML keyword）。

**借鉴生态**：ESLint / RuboCop / Clippy / TSLint 的 rule plug-in 模式。

**已有相关基础**：daltskin LSP[^repo-daltskin-lsp]的 `analysis/` 目录提供复杂度指标——可作为 lint 规则的载体。

**评估投入**：1 人月（如果在 daltskin LSP 上加 30 条规则）；2–3 人月（如果做独立 CLI 工具支持多 LSP backend）。

### A.2 可复用 GitHub Action

**空白**：`actions/setup-sysmlv2`、`actions/sysmlv2-validate`、`actions/sysmlv2-lint`——目前一个都没有。

**借鉴生态**：[`actions/setup-node`](https://github.com/actions/setup-node)、[`actions/setup-python`](https://github.com/actions/setup-python)、[`pre-commit/action`](https://github.com/pre-commit/action)。

**已有相关基础**：[santoslab/sysmlv2-models][^repo-santoslab]的 4 平台 CI 工作流是最完整的样例，可作为 cookbook 起点。

**评估投入**：1–2 人月（含 Docker 镜像维护）。

### A.3 sysml-fmt（gofmt 等价物）

**空白**：跨工具 SysML v2 文本格式化结果不收敛，git diff 噪声偏高。

**借鉴生态**：gofmt、prettier、rustfmt。

**已有相关基础**：daltskin LSP / SysIDE Legacy 都有 formatter 实现，但语义不一致；可在其中一个之上做 spec 化"标准 formatter"。

**评估投入**：1–2 人月（若复用现有 formatter；3 人月若从零写）。

### A.4 IntelliJ 平台插件

**空白**：JetBrains marketplace 完全没有 SysML v2 插件（详见 [04-解析 IDE §4.2](04-parsing-ide-infrastructure.md#42-jetbrains-marketplace)）；[luluorta/intellij-plugin-sysml][^repo-intellij-old]是 2015 年的 v1 工程、10 年没动。

**实现路径**：直接调 daltskin 或 spec42 的 LSP，做一个 IntelliJ Platform Plugin（用 `lsp4ij` 或自己集成 LSP4J）。

**借鉴生态**：[VSCode IntelliJ Plugins for LSP](https://plugins.jetbrains.com/plugin/23257-language-server-protocol-lsp-support)。

**评估投入**：2–3 人月（基础功能：syntax highlight、go-to-def、completion、formatter）。

### A.5 Markdown-embedded 渲染器

**空白**：没有 Jekyll / Hugo / MkDocs / Docusaurus 插件直接渲染 `.sysml` fence block。

**实现路径**：复用 daltskin 的 Mermaid 输出能力，包成 markdown plugin。

**借鉴生态**：mermaid-js 在 GitHub README 内置渲染、[plantuml 各 markdown 插件](https://github.com/marketplace?query=plantuml)。

**评估投入**：1 人月（每个 markdown 框架 0.3 人月）。

### A.6 Neovim / Emacs 配置脚本

**空白**：没有打包好的 `nvim-lspconfig` 内置条目、`eglot` 配置示例。

**实现路径**：把 daltskin / spec42 + nomograph tree-sitter 包成 lazy.nvim / `use-package` 一键配置。

**评估投入**：0.5 人月。

## B 中期机会（团队 3–12 月）

### B.1 SysML v2 → Modelica / FMU 开源生成器

**空白**：截至 2026-05，**没有任何开源 SysML v2 → Modelica / FMU 直生成器**。Zimmermann LNCS 2024[^zimmermann-2024]、Pepper INCOSE 2024[^pepper-2024] 都是论文无代码，OpenModelica / JModelica 仍只到 v1。

**实现路径**：QVTo 转换器（仿照 Pilot 的 `SysML2OWL.qvto`）或 Python+Jinja codegen，把 v2 PartDef + Connector + Constraint → Modelica `model` + `connect`。可借助 Modelica Standard Library 的 unit / FMI primitives。

**借鉴生态**：OpenModelica 的 MetaModelica、Acceleo 的 UML 模板生态、HAMR 的 SysML v2 → AADL IR 思路。

**评估投入**：4–6 人月（一个完整子集：物理网络拓扑 + 1D-DAE 模型）。

### B.2 LSP-driven Jupyter kernel

**空白**：OMG 官方 Jupyter kernel 只有 11 magic、**无 LSP-style autocomplete**；daltskin LSP 在 notebook 里走通但**不是 Jupyter kernel**。

**实现路径**：用 `xeus-cling` 或纯 Python `ipykernel` 包一个 kernel，内部启动 daltskin / spec42 LSP，把 cell 内容做增量 parse + completion。

**借鉴生态**：[Jupyter SubKernels](https://github.com/jupyter-server/jupyter-server-proxy)、[xeus-clojure / xeus-* 系列](https://github.com/jupyter-xeus)。

**评估投入**：3–4 人月。

### B.3 视觉模型 diff/merge viewer

**空白**：没有生产级模型 diff/merge viewer（详见 [06-可视化协作 §6](06-visualization-collaboration.md#6-模型-diff--merge-现状)）。**API 标准本身没定义 merge** 是规范级空白。

**实现路径**：

1. 定义 SysML v2 三方合并算法（基于 KerML Element identity + DataVersion）。
2. 在 SysON 或独立工具上实现可视化 diff（diff 双窗 + 冲突高亮）。
3. 提交到 OMG RTF 推动规范级 merge 端点定义。

**借鉴生态**：[EMF Compare][^emf-compare]、[Team for Capella][^team-capella]、Git 三方合并算法。

**评估投入**：6–8 人月（含算法定义 + UI 实现）。

### B.4 Living MMS：统一协同 SaaS 发行版

**空白**：「装一下就有协同 + 版本 + diff」的发行版不存在；SysON 是图编辑、Flexo 是图存储、daltskin LSP 是编辑器，要联动需自己拼装。

**实现路径**：以 [cosgroma/mbse-lab][^repo-mbse-lab]或 docker-compose 包装 SysON + Flexo MMS + daltskin LSP + sysand registry，提供 single-command 部署。

**借鉴生态**：GitLab Omnibus、Mattermost 自托管包。

**评估投入**：4–5 人月（运维 + 升级路径需要长期投入）。

### B.5 v1 → v2 reference 迁移转换器

**空白**：OMG Part 4 v1→v2 Transformation 规范明文承认"reference implementation not currently available"[^omg-transform]；DoD CTO 蓝图同样仅是方法学[^dod-cto]。

**实现路径**：QVTo 或 ATL 转换，输入 SysML v1 XMI，输出 SysML v2 `.sysml` + KPAR。注重保留 trace metadata 与 tagged values。

**借鉴生态**：[Sensmetry DETECT 案例][^sensmetry-detect]的人工迁移经验、Cameo 商业 v2 plugin 的内部转换（不开源）。

**评估投入**：6–10 人月（覆盖度依赖 v1 profile 复杂度）。

### B.6 真正能跑 state machine 的 simulator

**空白**：[sanbales/pymbe][^repo-pymbe]最接近真行为执行的 OSS 工具但 2024-10 停滞；Sensmetry Sismic 桥**闭源商业**。

**实现路径**：复活 PyMBE 或重写——把 KerML occurrence/atom 解释器扩展到完整状态机语义；接 Python 仿真后端。

**借鉴生态**：Sismic（Python）、UPPAAL、SimPy。

**评估投入**：4–6 人月。

## C 长期 / 学术 + 工业（1–3 年）

### C.1 KerML 在 Coq / Isabelle / Lean 4 中的 deep embedding

**空白**：AADL 已有 [Oqarina][^repo-oqarina]（Coq）与 HAMR-Isabelle 形式化；UML 有 HOL-OCL；SysML v2 完全空白。[chantakan/verified-mbse][^repo-verified-mbse]是 Lean 4 shallow embedding 的孤本（2★、单作者、无 v2 文本前端）。

**实现路径**：

1. 把 KerML 抽象语法定义为 Coq inductive type（深嵌入）。
2. 形式化 Specialization / Subsetting / Conjugation 等核心语义。
3. 给出 well-formedness 定理与具体例证。
4. 对接 SysML v2 文本前端（解析器 + 翻译到 deep embedding）。

**借鉴生态**：[Oqarina][^repo-oqarina]的工作流、HOL-OCL（Isabelle）、[verified-mbse][^repo-verified-mbse]的 case study 模式。

**评估投入**：2–3 人年（学术 PhD 课题量级）。

### C.2 通用 SMT-based v2 部件实例化可满足性检查

**空白**：SysMD 的 AADD（区间 + LP）只解代数约束，不解一阶逻辑；没有走 Z3/cvc5 + SysML v2 API 的 part 实例存在性检查器。

**实现路径**：把 PartDef + Connector + Constraint → SMT-LIB2 编码（用 quantifier-free linear arithmetic + uninterpreted functions），用 Z3 / cvc5 求解，给出可满足实例或不可满足证明。

**借鉴生态**：Alloy SAT 编码、[Imandra][^imandra]（闭源）的 IML transpiler、Refinery（[matetamasi/kreate][^repo-kreate]的目标）。

**评估投入**：1–1.5 人年。

### C.3 SysML v2 → LTL/CTL 性质规约 DSL + Gamma 后端开源

**空白**：Gamma 状态机 → UPPAAL/Theta 后端可用，但 SysML v2 前端代码不开源[^molnar-2024]。Imandra 可以但闭源。

**实现路径**：

1. 设计 SysML v2 内嵌的性质规约 DSL（仿照 PSLPattern / Spec Patterns）。
2. 实现 → LTL/CTL 翻译。
3. 复现或对接 Gamma 的 SysML v2 前端（联系 BME 团队或重造）。
4. 集成 nuXmv / UPPAAL / Theta 工作流。

**借鉴生态**：PSL、Spec Patterns、[ftsrg/gamma][^repo-gamma]主仓。

**评估投入**：1–2 人年。

### C.4 Hybrid / Probabilistic 验证桥

**空白**：PRISM / Storm / dReal / SpaceEx 都没和 SysML v2 拼接；SAWS² / xSAP 走 fault tree，不走 hybrid。

**实现路径**：定义 SysML v2 的 hybrid 语义扩展（Continuous Action + ODE 注解）→ PRISM / dReal 输入语言转换。

**借鉴生态**：Modelica + dReal / SpaceEx 的现有桥接、Hybrid Modelica 的语义。

**评估投入**：1.5–2 人年（需要混合系统形式化背景）。

### C.5 领域 Rule Pack（ISO 26262 / DO-178C / ARP4754A）

**空白**：[ftsrg/isse-formal-methods-sysmlv2][^repo-isse-fm]只是案例模型；没有任何 OSS 的功能安全 / 适航 / 系统工程标准的 rule pack。

**实现路径**：

1. 选定一项标准（如 ISO 26262 ASIL decomposition）。
2. 分解为可机检的规则（Allocation 完整性、独立性约束、覆盖率等）。
3. 实现为 SysML v2 校验规则集（OCL 等价 + 自定义检查）。
4. 配套合规文档生成器。

**借鉴生态**：AADL Resolute / Resolint、SCADE Suite 的 DO-178C 模板。

**评估投入**：1–2 人年（按标准复杂度）。

### C.6 代码 ↔ v2 round-trip 同步器

**空白**：HAMR 是单向 model → code；没有 round-trip。

**实现路径**：定义 C++ / Rust header → SysML v2 part 的反向 mapping，用 LSP-style 工具链驱动两端同步。

**借鉴生态**：UML 的 round-trip CASE 工具（Rational Rose、IBM Rational Software Architect）传统经验。

**评估投入**：2 人年（语言 binding × 工具链复杂度）。

## D 按维度归类的开源空白索引

| 维度 | 空白 | 章节 |
|---|---|---|
| 解析 / IDE | 没有 IntelliJ 插件 | A.4 |
| 解析 / IDE | 没有打包好的 nvim / Emacs plugin | A.6 |
| 解析 / IDE | 没有 LSP-driven Jupyter kernel | B.2 |
| 解析 / IDE | 新版 Sensmetry 闭源（无替代） | — |
| 解析 / IDE | 没有横向 benchmark | — |
| 解析 / IDE | 没有 SBOM / 包签名（KPAR） | — |
| 解析 / IDE | 没有 LSIF / SCIP 索引器 | — |
| 形式化 | SysML v2 → Coq / Isabelle deep embedding | C.1 |
| 形式化 | SMT 后端可满足性检查 | C.2 |
| 形式化 | LTL / CTL 性质 DSL + Gamma 前端开源 | C.3 |
| 形式化 | Hybrid / Probabilistic 桥 | C.4 |
| 形式化 | 领域 rule pack（ISO 26262 / DO-178C / ARP4754A） | C.5 |
| 可视化 | WebGL / 3D 视图 | — |
| 可视化 | Markdown 嵌入渲染器 | A.5 |
| 协作 | API 标准没定义 merge 端点 | B.3 |
| 协作 | 视觉 diff / merge viewer | B.3 |
| 协作 | P2P / CRDT 协同编辑 | — |
| 协作 | 官方 sysml-fmt | A.3 |
| 协作 | 一体化 SaaS 协同发行版 | B.4 |
| 协作 | MCP 写 + diff + merge 多 commit | — |
| Codegen | SysML v2 → Modelica / FMU | B.1 |
| Codegen | UML / v1 → v2 迁移转换器 | B.5 |
| Codegen | 多目标代码生成（Rust/Go/Python ABI） | — |
| Codegen | 真正能跑 state machine 的 simulator | B.6 |
| Codegen | 代码 ↔ 模型双向同步 | C.6 |
| 流水线 | 可复用 GitHub Action | A.2 |
| 流水线 | ESLint 风格 linter | A.1 |

## E 推荐立项策略

按**风险 × 回报**做一张分布图：

- **低风险 + 高回报**（个人 / 小团队首选）：A.1 linter、A.2 GitHub Action、A.3 sysml-fmt、A.4 IntelliJ 插件、A.5 Markdown 渲染器。这五项每项 ≤3 月，用户面广，能立刻填补社区刚需。
- **中风险 + 中回报**：B.1 Modelica 桥（生态最大空白之一）、B.5 v1→v2 转换器（OMG / DoD 都说"待实现"）。这两项是工程量大但商用价值清晰。
- **高风险 + 高回报**（学术 / 长期）：C.1 Coq/Isabelle 形式化（PhD 课题，3 年）、C.2 SMT 检查器、C.5 领域 rule pack（监管刚需）。这三项可能改变 SysML v2 在安全关键领域的可用性。
- **非商业可行**（学术 / 经费驱动）：C.3 性质 DSL、C.4 Hybrid 验证、C.6 双向同步。需 NSF / DARPA / 欧盟 H2020 等基础研究经费。

## 参考文献

[^repo-daltskin-lsp]: *daltskin/sysml-v2-lsp*. <https://github.com/daltskin/sysml-v2-lsp>

[^repo-santoslab]: *santoslab/sysmlv2-models*. <https://github.com/santoslab/sysmlv2-models>

[^repo-intellij-old]: *luluorta/intellij-plugin-sysml*（v1，2015）。<https://github.com/luluorta/intellij-plugin-sysml>

[^zimmermann-2024]: *SysML v2 for Automated Co-simulation*. Springer LNCS 2024. <https://link.springer.com/chapter/10.1007/978-3-031-62554-1_4>

[^pepper-2024]: *Bidirectional SysML v2 ↔ Modelica Transformation*. INCOSE IS 2024. <https://incose.onlinelibrary.wiley.com/doi/abs/10.1002/iis2.13239>

[^repo-mbse-lab]: *cosgroma/mbse-lab*. <https://github.com/cosgroma/mbse-lab>

[^omg-transform]: OMG. *SysML v2 Part 4: v1 to v2 Transformation Specification (Beta1)*. <https://www.omg.org/spec/SysML/2.0/Beta1/Transformation/PDF>

[^dod-cto]: U.S. OUSD(R&E). *SysML v1 to SysML v2 Model Conversion Approach v1.3*. 2024-03. <https://www.cto.mil/wp-content/uploads/2025/02/SysML-v2-TransitionApproach-1.3.pdf>

[^sensmetry-detect]: Sensmetry. *DETECT Case Study*. <https://sensmetry.com/sysml-v1-to-sysml-v2-migration-of-detect-benefits-lessons-learned/>

[^repo-pymbe]: *sanbales/pymbe*. <https://github.com/sanbales/pymbe>

[^repo-oqarina]: *Oqarina/oqarina*. <https://github.com/Oqarina/oqarina>

[^repo-verified-mbse]: *chantakan/verified-mbse*. <https://github.com/chantakan/verified-mbse>

[^imandra]: Imandra. *SysML v2 Solution*（闭源）。<https://www.imandra.ai/sysml>

[^repo-kreate]: *matetamasi/kreate*. <https://github.com/matetamasi/kreate>

[^molnar-2024]: Molnár, V., & Graics, B. *Towards the Formal Verification of SysML v2 Models*. MODELS 2024. <https://dl.acm.org/doi/10.1145/3652620.3687820>

[^repo-gamma]: *ftsrg/gamma*. <https://github.com/ftsrg/gamma>

[^repo-isse-fm]: *ftsrg/isse-formal-methods-sysmlv2*. <https://github.com/ftsrg/isse-formal-methods-sysmlv2>

[^emf-compare]: EMF Compare. <https://eclipse.dev/emfcompare/>

[^team-capella]: Team for Capella. <https://www.obeosoft.com/en/team-for-capella>
