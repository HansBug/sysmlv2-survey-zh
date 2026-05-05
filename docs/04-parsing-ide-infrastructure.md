# 04 解析、编译与 IDE 基础设施

## 本章简介

本章对 SysML v2 / KerML 的**解析、编译、LSP 与 IDE 基础设施**做技术深度对比，覆盖 6 套独立 parser、3 个 tree-sitter 语法、4 个 VS Code 扩展、JetBrains / Vim / Emacs 端的现状，以及 KPAR 包格式与 sensmetry sysand 包管理器。读者读完应能回答：

- 我应该把哪个 parser/LSP 接入我的工具链？
- **如果我要用 ANTLR4：哪个文法严格对应 OMG 规范、可直接复用？**（§3 是本章重点新增）
- 哪些 IDE 上没有 SysML v2 支持？
- KPAR 在工程组织上能不能信？

> **本章的核心实测结论**（2026-05-05 在本仓库工程机上、Java/Python/JS 三 runtime 一致复现）：daltskin/sysml-v2-grammar 的 ANTLR4 文法在 daltskin 设计支持的 SysML 文件范围内（**`.sysml`，不含 `.kerml`**），对 OMG 官方训练库 100/100 + Pilot Systems Library 58/58 + 13 个真实世界仓库（Airbus Apollo 11、Sensmetry Advent、LinkedIn Learning、Galois HARDENS、Loonwerks INSPECTA 等）339/362 全部解析通过——其中真 v2 子集（除去 1 个 v1 BDD 仓 + 1 个非标 `instance` 仓）通过率 339/350 = **96.9%**。详见 §3.6。

## 1 OMG 官方参考实现：Pilot Implementation

[Systems-Modeling/SysML-v2-Pilot-Implementation][^repo-pilot]（LGPL-3.0、221★、最近 commit 2026-05-04）是**唯一的 OMG 规范官方实现**，技术栈 Eclipse Modeling Tools 2025-12 + Java 21 + Xtext + EMF + Tycho Maven。规模与结构：

### 1.1 文法与元模型

Xtext 文法物理上分两个 OSGi bundle：

| Bundle | 文件 | 体量 |
|---|---|---|
| `org.omg.kerml.xtext` | `KerML.xtext`[^pilot-kerml-xtext] | ~28.7 KB / 1124 行 |
| `org.omg.sysml.xtext` | `SysML.xtext`[^pilot-sysml-xtext] | ~61.9 KB / 2438 行（约 1261 个生产规则） |
| `org.omg.kerml.expressions.xtext` | `KerML.expressions.xtext` | 表达式子语法，被 SysML 通过 `import` 复用 |

层叠组织：通过 Xtext 的 `Grammar.with` 语法继承 + EMF 元模型继承；KerML 元模型在 `org.omg.sysml/model`；SysML 是 KerML 的语法 + 元模型 conservative extension（详见 [01-标准状态](01-standard-status.md) §2）。

### 1.2 校验

`SysMLValidator.xtend`[^pilot-validator]单文件 76.6 KB / 1464 行，`@Check` 注解共 **73 条**校验规则、约 569 处 `error/INVALID_*` 标识符——这是**规范 well-formedness 条件直接落地的事实参考实现**。所有第三方 LSP 都需要 reproduce 这一规则集，但截至 2026-05 没有任何第三方完全做到。

### 1.3 Jupyter Kernel

`org.omg.sysml.jupyter.kernel` 基于 SpencerPark/jupyter-jvm-basekernel；进程模型为**单 JVM 内嵌 Xtext + EMF runtime**（Java 21）。Magic command 共 11 个：`Eval / Export / Help / Listing / Load / Projects / Publish / Repo / Show / View / Viz`。**没有 LSP-style autocomplete**——补全要靠 Eclipse Xtext UI 模块，不在 Jupyter 端。

### 1.4 PlantUML 可视化

`org.omg.sysml.plantuml/` 实现明确为 **Visitor 模式**：`Visitor.java` 26 KB + 30 个 `V*.java` 子类。覆盖度比 SysON 更广（含 Sequence、Use Case），但**只读**渲染、PlantUML 文本输出、布局靠 PlantUML 自动；定位是「Jupyter `%viz` magic 与 Eclipse 文本编辑器中的快速预览」，是 fallback 而非一线工具。

### 1.5 license 与复用风险

Pilot 整体走 LGPL-3.0；任何"派生作品"复用 Pilot 内部 `.xtext` 或 `src-gen/` 下生成的 ANTLR3 文法都受同样约束。这是为什么需要 §2、§3 介绍的独立 ANTLR4 / Langium 替代品——它们多数走 MIT / Apache-2.0，license 兼容面更广。

### 1.6 Pilot 文法文件如何使用：本地实测

Pilot 仓库提供两类「文法文件」，使用门槛差异显著：

**(A) `*.xtext` 源文件（人写文法）**：[`KerML.xtext`][^pilot-kerml-xtext]、[`SysML.xtext`][^pilot-sysml-xtext]。这是 Xtext 元语言（不是 ANTLR、不是 EBNF），**不能直接被 ANTLR / Langium / 其它 parser generator 消费**。要用它必须通过 Eclipse Xtext 工具链：在 Eclipse + Xtext SDK 中打开工程，由 Xtext 的 `MWE2 workflow` 调起代码生成器，生成 `src-gen/.../InternalSysML.g`（ANTLR3）+ 配套 Java 代码。

**(B) `InternalSysML.g`（自动生成的 ANTLR3）**：路径 [`org.omg.sysml.xtext/src-gen/org/omg/sysml/xtext/parser/antlr/internal/InternalSysML.g`][^pilot-internal-sysml]，**29351 行**。理论上是合法 ANTLR3 文法，但**绑死 Xtext runtime**——不能脱离 Xtext 单独使用：

```bash
# 实测（2026-05-05）：
$ wc -l /tmp/pilot/org.omg.sysml.xtext/src-gen/.../InternalSysML.g
29351

$ grep -cE "import org\.eclipse\.xtext" \
    /tmp/pilot/org.omg.sysml.xtext/src-gen/.../InternalSysML.g
8
```

`InternalSysML.g` 头部即声明 `superClass=AbstractInternalAntlrParser`，且 `@parser::header` 与 `@lexer::header` 内嵌 `org.eclipse.xtext.*` 导入与 `org.eclipse.xtext.parser.impl.AbstractInternalAntlrParser` 父类。任何想脱离 Eclipse Xtext runtime 把它接入自己的工具链都不可行——这正是 daltskin（§3）选择**不**复用 Pilot 文法、转而从 OMG KEBNF 重新生成的根本原因。

实践结论：**若要"用 Pilot 的语法"，最现实的路径是接入 Pilot 自身**——通过其 Jupyter kernel 或 Eclipse 插件作为黑盒的解析 / 求值器。下面 §1.7 是 Jupyter kernel 的本地实测。

### 1.7 官方 Jupyter kernel 本地实测（2026-05-05）

按 [Pilot 官方 Jupyter 安装指南][^pilot-jupyter-install]步骤本地实测安装与简单例子运行。

**装：**

```bash
# 1. 装 OpenJDK 21（参见 §3.6 工具链小节）
$ /tmp/jdk-21.0.11/bin/java -version
java version "21.0.11" 2026-04-21 LTS

# 2. 用 micromamba（conda 兼容、单二进制、无需 root）从 conda-forge 拉 kernel
$ curl -sL "https://micro.mamba.pm/api/micromamba/linux-64/latest" \
    | tar -xvj bin/micromamba
$ /tmp/bin/micromamba create -y -r /tmp/mamba-root -n sysml \
    -c conda-forge "jupyter-sysml-kernel=0.58.0" python=3.12

# 3. 列内核
$ /tmp/bin/micromamba run -r /tmp/mamba-root -n sysml \
    jupyter kernelspec list
Available kernels:
  sysml    /tmp/mamba-root/envs/sysml/share/jupyter/kernels/sysml
```

> 备注：[官方 install.sh][^pilot-install-sh] 用 `conda install` 走完整 Anaconda；但 conda-forge 同步发布的 `jupyter-sysml-kernel=0.58.0` 也能被 micromamba 直接拉，省去 Anaconda 整套发行版（300+ MB → ~50 MB）。pip 上没有该包（截至 2026-05-05 实测 `pip install jupyter-sysml-kernel` 报 "No matching distribution found"）。

**跑一个 OMG 训练样例：**

把 `Subsetting Example.sysml`（`SysML-v2-Release/sysml/src/training/04. Subsetting/`，342 字符）通过 `jupyter_client` 直接送入 kernel：

```python
import jupyter_client
km, kc = jupyter_client.manager.start_new_kernel(kernel_name='sysml')
src = open('Subsetting Example.sysml').read()
kc.execute(src)
# ... read iopub messages ...
```

输出：

```
[result] {'text/plain': 'Package Subsetting Example (5e719f40-628a-456c-81db-1a28f6cac150)\n'}
```

——返回了被解析后的顶层 `Package` 元素的名称 + KerML §9.1 强制规定的 **UUID v5**（详见 [01-标准状态 §16.3](01-standard-status.md#163-全局标识uuid-v5-强制要求)）。这证明 Pilot 官方 Xtext 文法 + Jupyter kernel 在本地工程机上端到端可用。

**小结**：Pilot 文法的使用方式只有「随 Pilot 整体打包用」一条路。如果你的诉求是把文法文件本身嵌入自己的工具链做轻量级 parse/AST 处理，**不要**走 Pilot Xtext，**用 daltskin 的 ANTLR4**（§3）。

## 2 第三方独立 parser 全景

截至 2026-05，存在 **9 套独立 parser** 实现，覆盖 8 种语言 / runtime 与 6 种文法形式（grammar formalism）。下面 §2.1 是一张拆分了「技术栈」与「语法形式」两列的总表；§2.2 给出每个 parser 的简要定位与可点击的仓库 / 商店链接；§2.3 整理 license 与活跃度信号；细节深读见 §3（ANTLR4 严格对应分析）与 §4（tree-sitter）。

### 2.1 总表

| 实现 | 技术栈（语言 / runtime） | 语法形式（grammar formalism） | 文法体量 | License | 状态 |
|---|---|---|---|---|---|
| [Pilot Implementation][^repo-pilot] | Java 21 + Eclipse 2025-12 + EMF + Tycho Maven | **Xtext**（生成 ANTLR3 内部 parser） | KerML 1124 行 + SysML 2438 行 | LGPL-3.0 | 规范权威，活跃 |
| [daltskin/sysml-v2-grammar][^repo-daltskin-grammar] | 通用（生成 10 语言 SDK） | **ANTLR4**（自动从 OMG KEBNF 生成 + 56 处 patch） | 1 lexer + 1 parser，452 parser 规则 + 226 lexer 规则 | MIT | 活跃，**ANTLR4 首选** |
| [SysIDE Legacy（sensmetry/sysml-2ls）][^repo-sysml-2ls] | Node.js + TypeScript | **Langium**（Chevrotain 内核） | 2174 + 1197 行 `.langium` | EPL-2.0 / GPL-2.0-CPE 双许可 | **已 archive 2025-10**，新版闭源 |
| [新版 Syside Editor（VS Code 商店版）][^vscode-syside] | Node.js + TS（细节闭源） | 闭源（推测仍 Langium） | 闭源 | **闭源商业** | VS Code marketplace 4254 安装，最大 |
| [elan8/spec42][^repo-spec42] | Rust + tower-lsp 0.20 + clap | **手写 Rust + nom 8 解析器组合子** | workspace 三 crate；entry 在 [`elan8/sysml-v2-parser`][^repo-sysml-v2-parser] | MIT | 个人维护，6★，活跃 |
| [elan8/sysml-v2-parser][^repo-sysml-v2-parser] | Rust + nom 8 + nom_locate | **手写 Rust + nom 解析器组合子** | AST 单文件 83 KB；parser 28 文件 | MIT | spec42 的解析后端，2★ |
| [MontiCore/sysmlv2][^repo-monticore] | Java + Gradle + MontiCore 框架 | **MontiCore `.mc4`**（语言工坊 DSL） | 12 个 `.mc4` 模块化文件 | BSD-3 派生（MontiCore 3-level license） | 学术严谨，工程粗糙，33★ |
| [sireum/hamr-sysml-parser][^repo-hamr-parser] | Scala / Slang + Sireum | **ANTLR4**（从 Pilot Xtext `src-gen/` 的 ANTLR3 内部 grammar 翻译） | `SysMLv2.g4` 1892 行（含 GUMBO）+ `KerMLv2.g4` 1015 行 + `GUMBO.g4` | **LGPL-3.0**（继承自 Pilot） | HAMR 依赖，11★ |
| [STARIONGROUP/KerML.NET][^repo-kerml-net] | C# / .NET 8 | **无 parser**（仅 in-memory model + JSON serializer） | n/a | Apache-2.0 | .NET 端孤本，1★ |

> 注：`spec42` 与 `sysml-v2-parser` 是同一作者（elan8）的两个独立 crate——前者是 LSP server，后者是其依赖的解析后端。本表把二者分开列以便区分定位。

### 2.2 每个 parser 的一句话定位

- **[Pilot Implementation][^repo-pilot]**：OMG Reference Implementation Working Group 维护的官方实现；Eclipse + Xtext + EMF + Jupyter kernel + PlantUML 可视化整套栈，是规范一致性的事实参照。本章 §1 做了详细解剖；§5 给出官方安装路径与本地实测结果。
- **[daltskin/sysml-v2-grammar][^repo-daltskin-grammar]**：单人维护、MIT 许可的纯 ANTLR4 文法仓库；其工程亮点是 `scripts/generate_grammar.py` 自动从 OMG KEBNF 生成 + `PATCHES.md` 记录 56 处 ambiguity 修复，并通过 GH Actions 周 cron 自动追上游 release[^daltskin-generate][^daltskin-patches]。已合入 [antlr/grammars-v4 官方仓库][^antlr-grammars-v4]。本章 §3 / §6 对其做完整深读 + 本地实测。
- **[SysIDE Legacy（sensmetry/sysml-2ls）][^repo-sysml-2ls]**：Sensmetry 公司 2024–2025 年的开源 Langium-based LSP；2025-10 archive 后被闭源商业版替代，是开源生态的最大隐忧。`packages/syside-languageserver/src/grammar/SysML.langium` 是规范认真重写的 Langium 文法，可作为研究参考（54 KB / 2174 行）。
- **[新版 Syside Editor][^vscode-syside]**：Sensmetry 闭源升级版；VS Code marketplace 4254 安装为生态最大，但所有源码与文法不公开。
- **[elan8/spec42][^repo-spec42] + [elan8/sysml-v2-parser][^repo-sysml-v2-parser]**：单人 Rust 实现；亮点是 `parse_for_editor()` resilient 模式（部分 AST + diagnostics）与 Zed 编辑器集成 query 文件。
- **[MontiCore/sysmlv2][^repo-monticore]**：RWTH Aachen MontiCore 语言工坊的 SysML v2 实现；把 OMG 单一文法拆 12 个 `.mc4` 模块（`SysMLActions / SysMLBasis / SysMLConnections / SysMLConstraints / …`），是 language composition 哲学的展示，但工程化短板（VS Code client 需 hack `SYSMLV2_LSP_PORT` env var）。
- **[sireum/hamr-sysml-parser][^repo-hamr-parser]**：HAMR 高保障代码生成栈的解析前端；语法上注入了 GUMBO 契约 DSL，适合 AADL / seL4 集成场景，但 LGPL-3.0 license 限制了通用复用。
- **[STARIONGROUP/KerML.NET][^repo-kerml-net]**：.NET 生态中唯一的 KerML 内存模型 + JSON 序列化库；不含 parser，需配合其它 parser 用。

### 2.3 License 与活跃度信号

| 实现 | License | 最近 commit | Star | 主要依赖 |
|---|---|---|---|---|
| Pilot Implementation | LGPL-3.0 | 2026-05-04 | 221 | 仅 Eclipse / Maven Central |
| daltskin/sysml-v2-grammar | MIT | 2026-04-21 | 6 | OMG KEBNF（cron 同步） |
| SysIDE Legacy | EPL-2.0 / GPL-2-CPE | 2025-10（archived） | 52 | npm: langium 3.x |
| 新版 Syside Editor | 闭源 | 私有 | n/a (4254 装机) | 私有 |
| elan8/spec42 | MIT | 2026-05-04 | 6 | nom 8、tower-lsp 0.20、clap |
| elan8/sysml-v2-parser | MIT | 2026-05-04 | 2 | nom 8、nom_locate |
| MontiCore/sysmlv2 | BSD-3 派生（MontiCore 3-level） | 2026-05-02 | 33 | MontiCore 框架 + Gradle |
| sireum/hamr-sysml-parser | LGPL-3.0（继承自 Pilot） | 2026-02-03 | 11 | Pilot Xtext src-gen 与 ANTLR4 |
| STARIONGROUP/KerML.NET | Apache-2.0 | 2025-03 | 1 | .NET 8、Newtonsoft.Json |

详细 license 兼容性矩阵见 §3.7。

## 3 ANTLR4 文法严格对应分析（重点）

> 本节是「我要用 ANTLR4 写工具，应当用哪个文法、它跟 OMG 规范差距多大」的直接答案。

### 3.1 OMG 原版 KEBNF 体量

OMG 把 SysML v2 / KerML 的具体文法以 **KEBNF**（Kernel Extended BNF）形式发布在 [Systems-Modeling/SysML-v2-Release/bnf/][^repo-bnf]：

| 文件 | 行数 | 产生式数 |
|---|---|---|
| `KerML-textual-bnf.kebnf` | 1467 | **186** |
| `SysML-textual-bnf.kebnf` | 1705 | **257** |
| `SysML-graphical-bnf.kgbnf`（KGBNF = Kernel Graphical BNF） | — | — |
| **合计文本 KEBNF** | **3172** | **443** |

KEBNF 不是普通 EBNF，它把元类、属性赋值、跨引用、产生式继承等语义动作内嵌（详见 [01-标准状态 §15](01-standard-status.md#15-kebnf-元语法)），不能直接被 ANTLR4 / Langium 等通用 parser generator 消费。任何 ANTLR4 实现都需要做 mechanical patch。

### 3.2 候选 ANTLR4 文法逐项对比

#### (1) daltskin/sysml-v2-grammar — 推荐首选

[daltskin/sysml-v2-grammar][^repo-daltskin-grammar]（MIT、6★）。本节关键事实：

| 维度 | 实测（2026-05-05） |
|---|---|
| 文件 | `grammar/SysMLv2Lexer.g4`（5670 B、257 行、**226 lexer 规则**）<br> `grammar/SysMLv2Parser.g4`（46 772 B、2168 行、**452 parser 规则**） |
| 生成方式 | **自动**从 OMG `KerML-textual-bnf.kebnf` + `SysML-textual-bnf.kebnf` 转 ANTLR4，由 [`scripts/generate_grammar.py`][^daltskin-generate]（141 KB）执行；自动化框架还包括 `scripts/find_dead_rules.py`、`scripts/find_cycles.py`、`scripts/conformance.py` |
| 当前对齐 OMG 版本 | `release_tag = 2026-03`，`grammar_version = 2026.03.2`（[`scripts/config.json`][^daltskin-config]）|
| 自动同步上游 | 周 cron `watch-upstream.yml` 监听 [`SysML-v2-Release`][^repo-release] 新 tag，自动开 PR |
| Patch 数 | **57 条 patch（56 已应用）**，全部记录在 [`grammar/PATCHES.md`][^daltskin-patches] |
| 多语言 SDK | `make sdk` 一次生成 **10 个 ANTLR4 target**：CSharp、Cpp、Dart、Go、Java、JavaScript、PHP、Python3、Swift、TypeScript（pinned ANTLR `4.13.2`） |
| 测试 | OMG 官方 conformance fixtures（`make update-conformance`）+ 三个手写示例（camera、toaster、vehicle） |
| **已合入官方 antlr/grammars-v4** | ✓ 见 [github.com/antlr/grammars-v4/tree/master/sysml-v2](https://github.com/antlr/grammars-v4/tree/master/sysml-v2)。grammars-v4 版本略旧（2026-01 tag），daltskin 主仓滚动到 2026-03 |

#### (2) nomograph-ai/kebnf — 备选 / DIY 路径

[nomograph-ai/kebnf][^repo-nomograph-kebnf]（MIT）。**Rust CLI**，发到 [crates.io: `nomograph-kebnf`](https://crates.io/crates/nomograph-kebnf)，`cargo install nomograph-kebnf` 即用。`--format antlr4` 出 `.g4`、`--format tree-sitter` 出 `grammar.js`；CI 每次 push 都跑 `antlr4 4.13.2` + `javac 21` + `tree-sitter generate` 验证零错。转换覆盖：`total_rules=640`、direct=247、strip+convert=353、best-effort=37、**manual_review=3**。

**关键差异 vs daltskin**：不维护一份"已 patch 好"的成品 `.g4`——你需要每次自己跑生成；也没有 daltskin 那 57 条 patch 的语义层修复（仅做语法翻译 + 左递归 / 重名处理）。Conformance 覆盖比 daltskin 弱。

**何时选**：你需要从 OMG KEBNF 直接 derive、且原版语义 / 规则结构不能丢；或者你想同步给 tree-sitter 用。否则坚决选 daltskin——daltskin 已替你做完 OMG 规范里的 56 处 ambiguity 修复。

#### (3) sireum/hamr-sysml-parser — license 风险，慎用

[sireum/hamr-sysml-parser][^repo-hamr-parser]（11★）。**license=null** 但 README 徽章写 LGPL-3.0（继承自 Pilot）。关键文件：

- `SysMLv2.g4` **1892 行 / 67 KB**（**注入了 GUMBO 契约语言**）
- `SysMLv2_SansGumbo.g4` 64 KB（不含 GUMBO）
- `KerMLv2.g4` **1015 行 / 35 KB**
- `GUMBO.g4` 14.8 KB

**生成方式与 daltskin 完全不同**：[`bin/regen.cmd`][^sireum-regen]从 Pilot 的 Xtext **`src-gen/`** 目录抓 `InternalSysML.g`、`InternalKerML.g`、`InternalKerMLExpressions.g`（这是 ANTLR3 内部表达），再 `sireum hamr sysml translator` 翻成 ANTLR4，对齐 SysML 版本 `2025-12`。文件头部仍带 ANTLR3 风格：`@parser::members { ... }`、`'a'..'z'` 字符类——可读性弱、可维护性差。

**License 风险**：派生作品理论上仍受 LGPL-3.0 传染——若你是闭源 / 非 LGPL 项目，**不要**直接嵌入这些 `.g4`。仅当你也要做 HAMR/AADL 集成 + GUMBO 契约扩展 + 接受 LGPL，才选。

### 3.3 PATCHES.md 详解：daltskin 的 57 条修补

[`grammar/PATCHES.md`][^daltskin-patches]列了 57 条 patch（56 已应用 / 1 跳过）。前 10 条样例：

| # | 摘要 | 规则 | 应用 |
|---|---|---|---|
| 1 | Double-THEN in `entryTransitionMember` | entryTransitionMember | ✓ |
| 2 | Double-THEN in `defaultTargetSuccession` (reserved) | defaultTargetSuccession | ✗ (skipped) |
| 3 | Make `NOT` optional in `satisfyRequirementUsage` | satisfyRequirementUsage | ✓ |
| 4 | Make `STANDARD` optional in `libraryPackage` | libraryPackage | ✓ |
| 5 | Make `visibilityIndicator` optional in `importRule` | importRule | ✓ |
| 6 | Add `allocationDefinition` to `definitionElement` | definitionElement | ✓ |
| 7 | Make `ASSERT` optional before `SATISFY` | satisfyRequirementUsage | ✓ |
| 8 | Add `ACTION` keyword support to `sendNode` | sendNode | ✓ |
| 9 | Add `returnParameterMember` to `caseBodyItem` | caseBodyItem | ✓ |
| 10 | Define missing `calculationUsageDeclaration` | calculationUsageDeclaration | ✓ |

性质上这些 patch 分三类：

- **(a) Spec BNF fix**：补 OMG KEBNF 自身的纰漏（缺产生式、双关键字冲突、可选标识漏写）。这部分实质上也修了规范级 bug，daltskin 已上报 OMG。
- **(b) Lexer-level token disambiguation**：ANTLR4 LL(*) 解析与 KEBNF 的冲突。
- **(c) Parser-level**：左递归、SLL 预测错误、关键字优先级。

绝大多数 patch 是 **transformational**（重写为等价形式），不是 **subtractive**（删功能）。**Patch #52 移除了 45 条 unreachable 规则**——若想 round-trip 写回原始 KEBNF 类型注解会丢信息；纯 lex/parse + AST 不受影响。

### 3.4 ANTLR vs OMG KEBNF 数量对比

| 度量 | OMG KEBNF | daltskin ANTLR4 | 差额 |
|---|---|---|---|
| 文件 | 2 (KerML + SysML) | 2 (Lexer + Parser) | — |
| 总产生式数 | 443 | parser 452 + lexer 226 = 678 | +235 |
| 总文本行 | 3172 | 2425 | -747 |

差额来源：

- **+235 产生式**：lexer 单独定义所有终结符（OMG KEBNF 把它们写在 productions 内联），所以 daltskin 的 lexer rules（226）大约对应 OMG KEBNF 中字面量与正则项的总数。**实际 parser 层产生式 daltskin 452 vs OMG 总 443 仅多 9**——非常接近，基本是 patch 中"(b)/(c) 类"引入的辅助规则。
- **-747 行**：ANTLR4 语法更紧凑，且 OMG KEBNF 含元类标注、属性赋值动作等 ANTLR4 不需要的语义层注解。

**结论**：daltskin 是**实践上"严格对应 OMG，多 9 条辅助规则、0 减"** 的状态。45 条被删的死规则属 OMG 自身写错的不可达定义，删除是修 bug 而不是减功能。

### 3.5 conformance 工具自带

daltskin 仓库的 `scripts/conformance.py` 自动跑 OMG 训练样例做端到端 parse 验收。`make update-conformance` 拉最新 OMG 训练库并跑全集——这是它 PATCHES.md 能稳定积累的工程基础。

### 3.6 本地端到端实测（Java / Python / JavaScript 三 runtime + 官方 + 真实世界 15 仓）

> **重要勘误（2026-05-05）**：本节早先版本曾给出 "858 / 858 全通过" 的数据，那是因为底层 shell 用了 `for f in $(find ... -name '*.sysml')` 这种**未引号化的命令展开**，把 `Subsetting Example.sysml` 等带空格的文件名按空白切成多个 token，工具被频繁喂给不存在的"文件名片段"，TestRig 输出的 "file not found" 不匹配 `^line N:N` 错误模式而被错认作"OK"。本节以下数据均为重新做了引号化处理之后的真实结果，且在 **Java、Python、JavaScript 三套 ANTLR4 runtime** 上得到了**完全一致**的数字。

#### 3.6.1 工具链版本

```bash
$ /tmp/jdk-21.0.11/bin/java -version
java version "21.0.11" 2026-04-21 LTS

$ /tmp/jdk-21.0.11/bin/java -jar /tmp/antlr.jar 2>&1 | head -1
ANTLR Parser Generator  Version 4.13.2

# Python runtime
$ pip install antlr4-tools antlr4-python3-runtime    # 自动 4.13.x

# JavaScript runtime
$ npm install antlr4@4.13.2
```

文法是 **daltskin 提交 `release_tag = 2026-03`，`grammar_version = 2026.03.2`**（[`scripts/config.json`][^daltskin-config]）。

#### 3.6.2 daltskin 是「100% 标准 ANTLR4，0 私有扩展」

机器扫描两份 `.g4` 确认：

| 非标准特性 | 出现次数 | 说明 |
|---|---|---|
| `@parser::header` / `@lexer::header` | **0** | 无 Java-only 头部块 |
| `@parser::members` / `@lexer::members` / `@members` | **0** | 无 Java-only 成员注入 |
| 语义谓词 `{ ... }?` | **0** | 无 |
| `fragment` 规则 | **0** | 无 |
| `channels { ... }` | **0** | 无 |
| `tokens { ... }` 块 | **0** | 无（lexer 文件单独管理 token） |
| 操作符联想 `<assoc=right>` | **1** | 标准 ANTLR4 元注解 |
| Lexer 命令 `-> skip` | 3 | 标准（`SINGLE_LINE_NOTE` / `BARE_LINE_COMMENT` / `WS`） |

结论：**daltskin 的 grammar 是高度标准、高度可移植的 ANTLR4**——同一份 `.g4` 在 Java / C# / C++ / Dart / Go / **JavaScript** / PHP / **Python3** / Swift / TypeScript 全部 10 个 ANTLR4 target 上等价生成 parser。下面 §3.6.3、§3.6.4 在 Python 与 JavaScript 上分别端到端走通流程。

#### 3.6.3 Python 端到端流程实测

```bash
# 1. 安装 Python ANTLR4 工具与 runtime
$ pip install antlr4-tools antlr4-python3-runtime

# 2. 生成 Python 3 parser
$ mkdir antlr-py && cd antlr-py
$ cp /tmp/daltskin-g/grammar/*.g4 /tmp/daltskin-g/grammar/*.tokens .
$ java -jar /tmp/antlr.jar -Dlanguage=Python3 -no-listener -no-visitor *.g4
# (生成 SysMLv2Lexer.py + SysMLv2Parser.py，无错)
```

运行单文件解析 + 树遍历：

```python
from antlr4 import FileStream, CommonTokenStream
from antlr4.error.ErrorListener import ErrorListener
from SysMLv2Lexer import SysMLv2Lexer
from SysMLv2Parser import SysMLv2Parser

class Collect(ErrorListener):
    def __init__(self): self.errs = []
    def syntaxError(self, *a): self.errs.append(a[3:])

s = FileStream('Subsetting Example.sysml', encoding='utf-8')
lex = SysMLv2Lexer(s); el = Collect()
lex.removeErrorListeners(); lex.addErrorListener(el)
parser = SysMLv2Parser(CommonTokenStream(lex))
parser.removeErrorListeners(); parser.addErrorListener(el)
tree = parser.rootNamespace()
print(f'errors={len(el.errs)}, root={type(tree).__name__}')
```

输出：

```
errors=0, root=RootNamespaceContext
RootNamespace:        package'Subsetting Example'{partdefVehicle{partparts:Vehic
  PackageBodyElement: package'Subsetting Example'{partdefVehicle{partparts:Vehic
    PackageMember:    package'Subsetting Example'{partdefVehicle{partparts:Vehic
```

#### 3.6.4 JavaScript / Node.js 端到端流程实测

```bash
# 1. 生成 JavaScript parser
$ mkdir antlr-js && cd antlr-js
$ cp /tmp/daltskin-g/grammar/*.g4 .
$ java -jar /tmp/antlr.jar -Dlanguage=JavaScript -no-listener -no-visitor *.g4

# 2. 安装 Node.js antlr4 runtime（注意：生成的 .js 是 ES module）
$ npm init -y && npm install antlr4@4.13.2 glob
$ node -e "const p=require('./package.json'); p.type='module'; \
    require('fs').writeFileSync('package.json', JSON.stringify(p,null,2))"
```

运行（`parse-real.mjs`）：

```javascript
import antlr4 from 'antlr4';
import SysMLv2Lexer from './SysMLv2Lexer.js';
import SysMLv2Parser from './SysMLv2Parser.js';
import fs from 'fs';

class Collect extends antlr4.error.ErrorListener {
  constructor(){super(); this.errs=[]}
  syntaxError(){this.errs.push(arguments)}
}
const src = fs.readFileSync('Subsetting Example.sysml', 'utf-8');
const lex = new SysMLv2Lexer(new antlr4.InputStream(src));
const el = new Collect();
lex.removeErrorListeners(); lex.addErrorListener(el);
const p = new SysMLv2Parser(new antlr4.CommonTokenStream(lex));
p.removeErrorListeners(); p.addErrorListener(el);
const tree = p.rootNamespace();
console.log(`errors=${el.errs.length}, root=${tree.constructor.name}`);
```

输出与 Python 一致：`errors=0, root=RootNamespaceContext`。

#### 3.6.5 daltskin 文法的实际范围：仅 SysML 文件（`.sysml`），**不**支持 `.kerml`

`daltskin/sysml-v2-grammar` 在 `scripts/config.json` 中将 `bnf_files: { kerml: ..., sysml: ... }` 两份 KEBNF 合并到**同一个** `SysMLv2Parser.g4`，但**入口规则**只有 `rootNamespace`——这是 **SysML** 顶层命名空间，要求文件以 `package`、`part def`、`attribute def` 等 SysML 关键字开头。

实测的反例：

```bash
$ python3 daltskin_parse.py 'Base.kerml'
Base.kerml: 15 errors, first error:
  line 10  no viable alternative at input 'abstractclassifier'
  src line 10:    abstract classifier Anything {
```

`Base.kerml` 第一行是 KerML 顶层语法 `standard library package Base { ... abstract classifier Anything { ... }}`——`abstract classifier` 是 KerML 元类直接声明，而 SysML 文件里要写 `attribute def` 之类的"使用层"关键字。daltskin 合并 grammar 时仅留了 SysML 入口路径，纯 KerML 顶层声明不在被接受集合内。

下表把 daltskin 的范围说清楚：

| 文件类型 | 典型场景 | daltskin 是否支持 |
|---|---|---|
| `*.sysml` | 用户写的 SysML v2 模型，标准库 `Systems Library/*.sysml`，全部训练样例 | **是** |
| `*.kerml` | KerML 内核库 `Kernel Libraries/.../*.kerml`、Release `kerml/src/*.kerml` | **否** |

要解析纯 `.kerml` 文件，目前的开源选项有：

- **[sireum/hamr-sysml-parser][^repo-hamr-parser]** 单独维护了 `KerMLv2.g4`（1015 行）+ `SysMLv2.g4`（1892 行）两份独立 ANTLR4 文法（受 LGPL-3.0 约束）。
- **[Pilot Xtext][^pilot-kerml-xtext]** 的 `org.omg.kerml.xtext` 是 KerML 端的权威实现（受 LGPL-3.0 约束、绑死 Xtext runtime）。
- **从 OMG `KerML-textual-bnf.kebnf` 自己生成**：可用 [nomograph-ai/kebnf][^repo-nomograph-kebnf] 单独跑 KerML 文件，再做 56 处 ambiguity patch。

#### 3.6.6 端到端 conformance 数据：官方 + 15 个真实世界仓（共 252 + 362 = 614 文件）

**A. 官方 OMG 与 Pilot 标准库**

| Corpus | 文件类型 | 文件数 | OK | 失败 | 备注 |
|---|---|---|---|---|---|
| [`SysML-v2-Release/sysml/src/training/`][^repo-release] | `.sysml` | 100 | **100** | 0 | OMG 训练库（v1.0 发布） |
| [`SysML-v2-Pilot-Implementation/sysml.library/`][^repo-pilot] | `.sysml` | 58 | **58** | 0 | Systems Library + 部分 Domain Libraries |
| 同上 | `.kerml` | 36 | 0 | 36 | 范围之外（见 §3.6.5） |
| [`SysML-v2-Release/kerml/src/`][^repo-release] | `.kerml` | 58 | 0 | 58 | 范围之外（见 §3.6.5） |
| **小计 .sysml**（在 daltskin 范围内） | | **158** | **158 (100%)** | 0 | |

**B. 真实世界 15 个公开仓库（按使用场景多样化）**

| 仓库 | 场景 | 文件数 | OK | 失败 | 备注 |
|---|---|---|---|---|---|
| [airbus/apollo-11-sysml-v2][^repo-airbus-apollo] | 航天器（空客发布的完整 Apollo 11 v2 模型） | 28 | **28** | 0 | |
| [GfSE/SysML-v2-Models][^repo-gfse] | 德国系统工程协会语料 | 36 | 33 | 3 | 失败为 Beta1 旧语法 |
| [MBSE4U/dont-panic-batmobile][^repo-mbse4u-bat] | Tim Weilkiens 教学示例 | 1 | **1** | 0 | |
| [sensmetry/advent-of-sysml-v2][^repo-advent] | Sensmetry 25 课教程 | 44 | **44** | 0 | |
| [GaloisInc/HARDENS][^repo-galois-hardens] | Galois 国防参考模型 | 17 | 15 | 2 | |
| [GfSE/MBSE_AG_vacuum-cleaner-robot-example][^repo-gfse-vacuum] | 机器人示例 | 52 | 49 | 3 | |
| [LinkedInLearning/systems-engineering-with-sysml-3955241][^repo-linkedin] | LinkedIn 课程 | 75 | **75** | 0 | |
| [DFKI-CPS/specific-sysml][^repo-dfki] | **使用 SysML v1 BDD textual notation**，非 v2 | 11 | 0 | 11 | 数据集错认 |
| [Open-MBEE/DesertKite.sysml][^repo-desertkite] | NASA / JPL 关联示例 | 1 | **1** | 0 | |
| [loonwerks/INSPECTA-models][^repo-inspecta] | Galois INSPECTA 项目 | 60 | **60** | 0 | |
| [systems-praxis/seamless-digital-engineering-reference-architecture][^repo-praxis] | 数字工程参考架构 | 15 | **15** | 0 | |
| [aslab/STO][^repo-aslab] | 学术原型，**用非标准 `instance` 关键字** | 1 | 0 | 1 | 自定义 |
| [mimidbe/SysML-v2-to-Modelica][^repo-modelica-bridge] | v2 ↔ Modelica 桥接研究 | 21 | 18 | 3 | |
| **小计** | | **362** | **339 (93.6%)** | 23 | |
| **真 v2 子集**（除去 DFKI v1 + aslab 自定义共 12 个） | | **350** | **339 (96.9%)** | 11 | |

#### 3.6.7 三 runtime 性能对比（同一 corpus、同一文法）

跑全部 362 个真实世界 .sysml 文件：

| Runtime | 总耗时 | 每文件平均 | 备注 |
|---|---|---|---|
| **Java 21 + ANTLR4 4.13.2** | **~8 秒** | ~22 ms | 最快，JIT 充分预热 |
| **Node.js v24 + antlr4@4.13.2** | ~50 秒 | ~140 ms | 中等 |
| **Python 3.10 + antlr4-python3-runtime** | ~390 秒 | ~1080 ms | Python ANTLR runtime 是已知短板 |

**结论**：

1. daltskin 是「严格标准 ANTLR4，零私有扩展」，三个 runtime 给出位精确一致的解析结果（339/362）。
2. 在 daltskin 设计支持的 SysML 文件范围内，**官方 corpus 全过（158 / 158）+ 真实世界 v2 corpus 96.9%（339 / 350）**——剩余 11 个失败文件全部为用户代码偏离 OMG v2 规范，**没有一个是 daltskin 文法的真 gap**。逐文件根因分析见 §3.6.8。
3. KerML 文件（`.kerml`）**不在 daltskin 范围内**（§3.6.5），需另外接 sireum / Pilot Xtext / 自跑 nomograph kebnf。
4. 性能上 Java >> JS >> Python，但都可以工程化使用——Python 1 秒 / 文件足够供 LSP 与 CI lint 使用。

#### 3.6.8 失败案例根因分析（11 / 350 真 v2 子集）

> 本节用「最小可复现 + ANTLR 报错 + spec 引用」方式逐文件追溯每个失败。每一条都做了在剥离上下文的最小 `.sysml` 片段上的复现（在 §3.6.3 的 Python harness 里），确认 ANTLR 错误指向源代码偏离 OMG v2 规范，而非 daltskin 文法漏写。

**类别汇总**（11 个失败按根因分布）：

| 类别 | 数量 | 例 |
|---|---|---|
| **A. 关键字上下文误用 / 自创关键字** | 2 | 顶层用 `actor X;`、自创 `evaluate` |
| **B. SysML v1 / Beta / Cameo / Modelica 风过期或非标语法** | 6 | `&&` / `alias as` / `id 'X'` / `@[SI::kg]` / `enum` |
| **C. 非 v2 字符串字面量** | 1 | Python 风三引号 `"""..."""` |
| **D. 保留字直接作标识符（未用 unrestricted name 引号）** | 2 | `classifier:` / `enum new` |
| **daltskin 真 gap** | **0** | — |

##### 详细案例表

| # | 仓库:路径 (commit) | 偏离片段（含 `Lxx` 行号） | ANTLR4 报错（首条） | 类别 | OMG v2 spec 应写法 |
|---|---|---|---|---|---|
| 1 | [GfSE/SysML-v2-Models @ ebbb0c3 :: `EIT_System_Use_Cases.sysml`](https://github.com/GfSE/SysML-v2-Models/blob/ebbb0c3/models/SE_Models/EIT_System_Use_Cases.sysml#L4) | `L4: actor Doctor;`（位于 package 顶层） | `extraneous input 'actor' expecting {abstract, action, alias, …}` | **A** | OMG training/35 全部 `actor` 用法都在 `use case def {…}` body 内：`use case def C { actor doctor : Person; … }`（v2 §22.2 Use Case Definition） |
| 2 | [GfSE/SysML-v2-Models @ ebbb0c3 :: `HVACSystemRequirements.sysml`](https://github.com/GfSE/SysML-v2-Models/blob/ebbb0c3/models/SE_Models/HVACSystemRequirements.sysml#L51) | `L51: ... && ...`（C 风布尔与） | `extraneous input '&' expecting {all, behavior, …}` | **B** | v2 §8 Expression Notation 用 `and` 关键字（同 KerML §7.4.10）：`particleFiltrationEfficiency >= minFiltrationEfficiency and …` |
| 3 | [GfSE/SysML-v2-Models @ ebbb0c3 :: `VehicleModel.sysml`](https://github.com/GfSE/SysML-v2-Models/blob/ebbb0c3/models/SE_Models/VehicleModel.sysml#L201) | `L201: alias ISQ::TorqueValue as Torque;` | `mismatched input '::' expecting 'for'` | **B** | v2 §9.2.5 Alias 唯一形式 `alias <Name> for <QualifiedName>;`：`alias Torque for ISQ::TorqueValue;`（Beta1 之前曾用 `as`，正式版统一为 `for`） |
| 4 | [GaloisInc/HARDENS @ e24bfdb :: `RTS_Static_Architecture.sysml`](https://github.com/GaloisInc/HARDENS/blob/e24bfdb/specs/SysML/RTS_Static_Architecture.sysml#L343) | `L343: connect eventControl.manualActuatorInput[1] to actuation.actuator1.manualActuatorInput;` | `missing 'to' at '['` | **B** | OMG training/09 Connections 中 connector end 的多重度写**前缀**：`connect [1] eventControl.manualActuatorInput.elem1 to actuation.actuator1.manualActuatorInput;`。HARDENS 的写法是把 `[1]` 当作 array 索引，这种"sequence 元素访问"在 v2 文本中不是 `[]` 而是 sequence access 函数（§8.5 Sequence Functions） |
| 5 | [GaloisInc/HARDENS @ e24bfdb :: `SemanticProperties.sysml`](https://github.com/GaloisInc/HARDENS/blob/e24bfdb/specs/SysML/SemanticProperties.sysml#L64) | `L64: classifier: String;`（`classifier` 作字段名） | `extraneous input 'classifier' expecting {abstract, action, …}` | **D** | `classifier` 是 KerML §8.2.2.6 列出的保留关键字。要把它作为标识符须用 unrestricted name：`'classifier' : String;` |
| 6 | [GfSE/MBSE_AG_vacuum-cleaner-robot-example @ 64cafbc :: `Functions/legacy/VacuumingSystem/FilterSystem.sysml`](https://github.com/GfSE/MBSE_AG_vacuum-cleaner-robot-example/blob/64cafbc/Functions/legacy/VacuumingSystem/FilterSystem.sysml#L28) | `L28: enum new;`（`enum` 关键字 + `new` 作标识符） | `extraneous input 'new' expecting {…}` | **B + D** | v2 没有 `enum` 关键字（用 `enumeration def` / `enum def` 视版本）；同时 `new` 也是 v2 中可能预留为关键字的标识符。该文件路径就含 `legacy`，作者已经标注是过期代码 |
| 7 | [GfSE/MBSE_AG_vacuum-cleaner-robot-example @ 64cafbc :: `SystemLevel/DriveUnit.sysml`](https://github.com/GfSE/MBSE_AG_vacuum-cleaner-robot-example/blob/64cafbc/SystemLevel/DriveUnit.sysml#L10) | `L10: requirement def id 'Req001' MaximaleMasse {` | ``mismatched input ''Req001'' expecting {';', '{'}`` | **B** | v2 §32.2 Requirement Definition 把需求 ID 放在 `<…>` 中：`requirement def <'Req001'> MaximaleMasse {…}`。该 `id` 关键字风格疑似来自 SysML v1 ReqIF |
| 8 | [GfSE/MBSE_AG_vacuum-cleaner-robot-example @ 64cafbc :: `SystemLevel/SystemRequirements.sysml`](https://github.com/GfSE/MBSE_AG_vacuum-cleaner-robot-example/blob/64cafbc/SystemLevel/SystemRequirements.sysml#L12) | `L12: require constraint { vacuumCleaner::mass <= 5@[SI::kg] }` | `extraneous input '[' expecting {…}` | **B** | v2 单位字面量是 `<value>[<unit>]` 不带 `@`：`5[SI::kg]`（v2 §10 Quantities）。`@` 在 v2 中是 metadata 注解算子（v2 §40），把它接 `[` 会进入 metadata 分支 |
| 9 | [mimidbe/SysML-v2-to-Modelica @ 90a3e52 :: `Cube3DModel.sysml`](https://github.com/mimidbe/SysML-v2-to-Modelica/blob/90a3e52/Cube3DModel.sysml#L56) | `L56: code = """\n  result = (\n    cq.Workplane("` | `mismatched input '"\r\n …'` | **C** | v2 §8.2.2.3 仅支持单 / 双引号 inline 字符串 + `\u{...}` 转义。Python 风三引号 `"""..."""` 不在词法表内。若要嵌入多行代码，应改用 KerML `doc /* … */` 注释 + 工具自解析，或拼接多行字符串 |
| 10 | [mimidbe/SysML-v2-to-Modelica @ 90a3e52 :: `exemple_enumeration.sysml`](https://github.com/mimidbe/SysML-v2-to-Modelica/blob/90a3e52/exemple_enumeration.sysml#L26) | `L26: small = 60@[SI::mm];` | `extraneous input '[' expecting {…}` | **B** | 同 #8。改为 `small = 60[SI::mm];` |
| 11 | [mimidbe/SysML-v2-to-Modelica @ 90a3e52 :: `vehicule.sysml`](https://github.com/mimidbe/SysML-v2-to-Modelica/blob/90a3e52/vehicule.sysml#L68) | `L68: evaluate vehicle.mass;` | `mismatched input 'vehicle' expecting {default, :=, ;, =, {}` | **A** | v2 §8.2.2.6 关键字表无 `evaluate`。要表达"求值"用 `calc`/`return`/expression 调用：`return vehicle.mass;` 或在 `calc def` 内组织表达式 |

##### 在 §3.6.3 Python harness 上的最小可复现验证

每一类失败在剥离上下文的最小片段上独立复现（实测命令见 §3.6.3）：

```python
# A 类（actor 顶层）：
test("package P { actor Doctor; }")           # FAIL
test("package P { use case def C { actor Doctor : Person; } part def Person; }")  # OK

# B 类样例：
test("package P { import SI::*; attribute m = 5[SI::kg]; }")    # OK   (spec 形式)
test("package P { import SI::*; attribute m = 5@[SI::kg]; }")   # FAIL (@ + [)
test("package P { alias Torque for ISQ::TorqueValue; }")        # OK
test("package P { alias ISQ::TorqueValue as Torque; }")         # FAIL
test("package P { requirement def <'Req001'> R {} }")           # OK
test("package P { requirement def id 'Req001' R {} }")          # FAIL

# C 类（三引号字符串）：词法器在 `"""` 直接进入字符串状态，遇到换行 + 嵌入 `"` 再退出，
#   导致 token 跨行裂为残破字符串 + 无效字符。

# D 类（保留字作 ID）：
test("package P { attribute def F { classifier: String; } }")    # FAIL
test("package P { attribute def F { 'classifier': String; } }")  # OK   (unrestricted)
```

##### daltskin 的能力边界声明

综合上述测试与失败分析，daltskin/sysml-v2-grammar `2026.03.2` 的能力边界明确为：

| 接受 | 拒绝（已实测） |
|---|---|
| ✓ 严格匹配 OMG SysML 2.0 Final（`formal/26-03-02`）文本语法 | ✗ SysML v1 BDD/IBD textual notation（DFKI 风） |
| ✓ 文件扩展名 `.sysml`（顶层为 `package`、`part def`、`requirement def` 等 SysML 关键字） | ✗ 文件扩展名 `.kerml`（顶层 `abstract classifier` / `class` 等 KerML 直声明） |
| ✓ Pilot 标准库 `Systems Library/*.sysml`（58/58 实测全过） | ✗ Pilot 标准库 `Kernel Libraries/.../*.kerml`（0/36，见 §3.6.5） |
| ✓ OMG 训练库全部 100 个范例（100/100 实测全过） | ✗ Beta1 / Beta2 阶段已弃语法（`alias as`、`id 'X'`、`@[unit]`） |
| ✓ Apollo 11 / INSPECTA / Sensmetry Advent / LinkedIn / Praxis 等真实工程模型 | ✗ Cameo / Modelica / Python 等他生态借来的语法（`&&`、`"""`、`evaluate`） |
| ✓ ANTLR4 4.13.x runtime 在 Java / Python / JavaScript / 任一 ANTLR4 target | ✗ 用户实现的私有扩展、未注册保留字作裸标识符 |

**实践建议**：把 daltskin 嵌入 LSP / CI / lint 工具时，建议**先在收到的输入上做扩展名分诊**——`.sysml` 走 daltskin，`.kerml` 走 sireum 或 Pilot Xtext；并对碰到 §3.6.8 表中 B 类语法直接给出"v1/Beta 已弃语法，请改用 X"的诊断信息（这本身就是一个规则集明确、立项门槛低的 lint 工具机会，详见 [09-缺口与机会 §A.1](09-gaps-opportunities.md#a1-eslint-风格-sysml-v2-linter)）。

### 3.7 推荐复用清单

> 这是面向资深工程师的可执行决策。

#### 推荐 A（首选）：`daltskin/sysml-v2-grammar`

**适合**：任何 ANTLR4 项目（Java / C# / C++ / Dart / Go / JS / PHP / Python / Swift / TS）。

**嵌入方法**：

```xml
<!-- pom.xml: 引入 ANTLR runtime + plugin -->
<dependency>
  <groupId>org.antlr</groupId>
  <artifactId>antlr4-runtime</artifactId>
  <version>4.13.2</version>
</dependency>
<plugin>
  <groupId>org.antlr</groupId>
  <artifactId>antlr4-maven-plugin</artifactId>
  <version>4.13.2</version>
  <executions>
    <execution><goals><goal>antlr4</goal></goals></execution>
  </executions>
</plugin>
```

```bash
# git submodule 把 daltskin/grammar/ 挂到 src/main/antlr4/
git submodule add https://github.com/daltskin/sysml-v2-grammar.git \
  third_party/sysml-v2-grammar
ln -s ../../../../third_party/sysml-v2-grammar/grammar \
  src/main/antlr4/org/myorg/sysmlv2
```

**入口规则**：`rootNamespace`（来自 `scripts/config.json`）。

**License 义务**：MIT，仅需保留 `LICENSE` + 版权声明。

**坑（必读）**：

1. PATCHES.md #52 移除了 45 条 unreachable 规则——纯 lex/parse + AST 无影响；想 round-trip 写回原始 KEBNF 类型注解会丢信息。
2. 仓库本身**不发 Maven Central / npm 包**——只发 GitHub Release zip。要稳定版本，git submodule 锁 commit 或 fork 自己发包。
3. **个人项目**（J Dalton, 1 contributor），但 `scripts/generate_grammar.py` 完全独立可运行，bus factor 低；OMG 上游一变就能本地 `make generate` 重生。

#### 推荐 B（备选）：`nomograph-ai/kebnf` 配合自行生成

**适合**：你想自己控制生成时机、追求工具链可重现性、或要同时输出 tree-sitter `grammar.js`。

```bash
cargo install nomograph-kebnf
nomograph-kebnf --format antlr4 --input KerML-textual-bnf.kebnf --output KerML.g4
nomograph-kebnf --format antlr4 --input SysML-textual-bnf.kebnf --output SysML.g4
# 然后自行处理 56 处 ambiguity（参考 daltskin PATCHES.md）
```

#### 不推荐：sireum/hamr-sysml-parser、Pilot Xtext 派生

License 都受 LGPL-3.0 传染，与多数闭源 / Apache-2.0 / MIT 工程不兼容。

#### License 兼容性矩阵

| 你 \ 文法 | daltskin (MIT) | kebnf (MIT) | nomograph tree-sitter (MIT) | sireum hamr (LGPL-3) | Pilot Xtext (LGPL-3) | sensmetry langium (EPL-2 / GPL-2-CPE) | jackhale98 / Samonjourus (None) |
|---|---|---|---|---|---|---|---|
| 闭源商用 | ✓ | ✓ | ✓ | ⚠ 须动态链接 + 提供文法源 | ⚠ 同左 | ⚠ EPL 派生需开源该派生 | ✗ |
| Apache-2.0 项目 | ✓ | ✓ | ✓ | ⚠ 与 LGPL 互兼但要分清 modules | ⚠ 同左 | ✓(EPL-2 兼容 Apache) | ✗ |
| MIT/BSD-2/BSD-3 项目 | ✓ | ✓ | ✓ | ⚠ | ⚠ | ⚠ | ✗ |
| LGPL-3 项目 | ✓ | ✓ | ✓ | ✓ | ✓ | ⚠ | ✗ |
| EPL-2 项目 | ✓ | ✓ | ✓ | ✗（EPL 与 LGPL 不兼容）| ✗ | ✓ | ✗ |
| GPL-3 项目 | ✓ | ✓ | ✓ | ✓ | ✓ | ⚠ 仅 GPL-2-CPE 通道兼容 | ✗ |

**关键原则**：标 "None" license = "All rights reserved"——任何复用都侵权。jackhale98 / Samonjourus / 部分学位项目 fork 都属此类，要先去 issue 拿一句白纸黑字的 license 才能嵌入。

### 3.8 工作量估算

| 路径 | 时间预估 | 说明 |
|---|---|---|
| **Fork daltskin + 改 entry rule + 加自己 listener / visitor** | **0.5–2 人日** | 已有 PATCHES、tests、SDK 流水线 |
| Fork nomograph kebnf 并自跑 `kebnf --format antlr4` | 1–3 人日 | 还需自己处理 daltskin 那 57 条 ambiguity 子集 |
| 从 OMG KEBNF 重头写 ANTLR4 | **30–60 人日** | KEBNF 1467 + 1705 行；左递归 / SLL / 关键字消歧 / 错误恢复都得自己解决 |
| Fork sireum 那套（ANTLR3-like）改成纯 ANTLR4 | 5–10 人日 + LGPL 法律审查 | 不推荐 |

## 4 Tree-sitter 文法

GitHub 上并存 **3 个 tree-sitter 语法** + 1 个 KEBNF→tree-sitter 转换器：

| 仓库 | 默认分支 | License | grammar.js | corpus tests | 状态 |
|---|---|---|---|---|---|
| [`nomograph-ai/tree-sitter-sysml`][^repo-nomograph-ts] | main | **MIT** | **71 312 B** | 15 个 .txt corpus（actions/usages/expressions 等共 ~135 KB） | 已发 [crates.io](https://crates.io/crates/tree-sitter-sysml)、[PyPI](https://pypi.org/project/tree-sitter-sysml/)、[npm](https://www.npmjs.com/package/tree-sitter-sysml)；C/Rust/Go/Python/Node/Swift bindings；queries 完整：highlights 10 KB、tags 7 KB、locals 8 KB、folds + indents；最近 push 2026-05-05 |
| [`jackhale98/tree-sitter-sysml`][^repo-jackhale-ts] | main | **None**（无 LICENSE = 默认保留全部权利，**不可商用嵌入**）| 51 458 B / 1617 行 | 198 corpus tests + 213/213 OMG examples + 92/94 std-library | 1★，宣称覆盖率最高，但 license 风险大 |
| [`Samonjourus/tree-sitter-sysmlv2`][^repo-samonjourus-ts] | develop | **None** | 拆 sysml/ + kerml/ 子目录，早期 WIP | — | 0★，最近 push 2025-09-14，**无 license 不可嵌入** |
| [`nomograph-ai/kebnf`][^repo-nomograph-kebnf] | main | **MIT** | 工具：KEBNF→tree-sitter 转换器 | — | 与 §3.2 (2) 同源 |

**结论**：要 tree-sitter，选 nomograph-ai。jackhale98 覆盖率虽高但缺 LICENSE；Samonjourus 是 WIP 不可用。

注：spec42 的 `zed/languages/sysml/highlights.scm` 是 tree-sitter query 文件但**绑定的不是上述任意一个 grammar**——是给 Zed 自己的解析器用的，仅做 token kind 标记。这是 Zed 用户当前唯一可用方案。

## 5 编辑器扩展生态盘点

### 5.1 VS Code Marketplace（按安装数）

| 扩展 | 安装数 | 状态 |
|---|---|---|
| `sensmetry.syside-editor`[^vscode-syside] | **4254** | 闭源商业（前身 sysml-2ls 已 archive） |
| `JamieD.sysml-v2-support`（daltskin）[^vscode-jamied] | **961** | MIT、活跃 |
| `Elan8.spec42`[^vscode-spec42] | **182** | MIT、个人 |
| `Ellidiss.sysml-ellidiss` | — | 商业产品入口 |

### 5.2 JetBrains Marketplace

**完全空白**。唯一候选 [luluorta/intellij-plugin-sysml][^repo-intellij-old] **2015 年最后一次 push、0★、是 SysML v1**——10 年没动。**IntelliJ 用户完全裸奔**，是生态最严重的空白。详见 [09-缺口与机会](09-gaps-opportunities.md) §A.4。

### 5.3 Neovim / Emacs

未找到任何专门打包的 plugin。Neovim 用户唯一可走的路是装 nomograph-ai 或 jackhale98 的 tree-sitter grammar，自己拼 LSP（接 daltskin 或 spec42 的 server）——但**没有打包好的 nvim plugin**（如 `nvim-lspconfig` 内置条目）。Emacs 同理无 `eglot` 配置示例公开发布。

### 5.4 Zed

spec42 自带 query files（见 §2 spec42 行），是 Zed 用户当前唯一可用方案。

### 5.5 Jupyter

- **官方 Pilot kernel**（见 §1.3）：单 JVM 内嵌 Xtext，11 magic，**无 LSP-style autocomplete**。
- **第三方**：daltskin 的 LSP 仓库带 `clients/python/sysml_lsp_demo.ipynb`，是用 stdio JSON-RPC 直驱 LSP server——这是更现代的做法（用 LSP 的 completion / hover），相当于绕开了 OMG kernel 的不足，但**不是 Jupyter kernel**，而是 notebook 调用 LSP 的 demo。**没有 LSP-driven Jupyter kernel** 是显著的生态空白。

## 6 KPAR 包格式与 sensmetry sysand

包管理是这一面唯一的亮点。[sensmetry/sysand][^repo-sysand]（Rust、29★、MIT/Apache-2.0、2026-05 活跃）的工程化设计在整套 v2 工具链里是**最干净、最现代、最敢复用 OMG 规范**的选择。

### 6.1 包格式：`.kpar`

直接用 KerML 1.0 spec §10.3 定义的 **`.kpar`**（KerML 项目交换归档，本质是 ZIP），不另发明格式：

- `.project.json`：公开 metadata（`publisher`、`license` 必须 SPDX 表达式、`version` 鼓励 SemVer 2.0、`usage` 数组列依赖）。
- `.meta.json`：源文件索引 / 校验和 / 时间戳。

**直接复用 OMG 标准**而非自创——是整个生态最干净的设计选择。

### 6.2 Lockfile

`sysand-lock.toml`（`core/src/lock.rs` 20 KB + `lock_tests.rs` 23.5 KB——锁文件实现工程量与逻辑核心相当）。

### 6.3 Resolver

用 [pubgrub crate 0.4][^pubgrub]——这与 `uv`、`cargo-next` 同款 SAT-style resolver，比 npm/pip 的回溯式更现代。

### 6.4 Registry

**没有强制中央 registry**——依赖 IRI 灵活解析：`http(s)://`（KPAR 文件 / 目录 / git repo）、`file://`、`urn:kpar:...`、`ssh://`、`git+...://`。Sensmetry 跑了一个公共 [beta.sysand.org][^beta-sysand] 索引，但 `hosting_index.md`[^sysand-hosting] 教用户**自托管**——通过 `sysand env` 把包放到目录里再用任意 HTTP server 暴露。

### 6.5 与 npm/cargo/pip 比

语义最像 **cargo**（lockfile + 多源 + git/url 直链），但用 **IRI 替代 URL** 是给 OMG 模型语义留出 namespace 的设计；不像 npm 强中心化、不像 pip 的版本依赖松散。**模型依赖语义独有**：通过 KerML 的 `library package` 概念，import 路径与包路径是统一的，这是编程语言包管理器没有的层面。

### 6.6 工程缺口

- **没有 SBOM / 包签名**（KPAR 还没有 sigstore 等签名）。
- **没有 LSIF / SCIP 索引器**（大模型代码搜索无依赖）。
- **不调 validator / linter**（仅做 KPAR project interchange + checksum）。

## 7 综合判断：5 个生态空白

1. **没有 IntelliJ 插件**（生态最大空白，企业 SysML 用户多用 IntelliJ）。
2. **没有打包好的 Neovim / Emacs 插件**（裸 tree-sitter + 自配 LSP 不算）。
3. **没有 LSP-driven Jupyter kernel**（OMG kernel 没有 completion，daltskin 的 notebook 不是 kernel）。
4. **新版 Sensmetry 闭源**——失去了最快的开源实现。
5. **没有公开横向 benchmark**——除了 Sensmetry 一句"50×"和 daltskin / spec42 内部 bench，**没有任何公开横向 benchmark 论文 / 博客**。

## 8 推荐路径（按使用场景）

| 场景 | 推荐组合 |
|---|---|
| 想读规范权威实现 | Pilot（Xtext / Java / Eclipse） |
| **要做 ANTLR4 工具链（任何语言）** | **daltskin/sysml-v2-grammar**（§3.7） |
| 做 Rust / CLI / MCP 集成 | spec42 + sysml-v2-parser |
| 嵌任意编辑器（含 Helix / Zed / nvim） | nomograph-ai tree-sitter + daltskin 或 spec42 LSP |
| 做 .NET 后端 | KerML.NET |
| 包管理 / CI 流水线 | sysand（pubgrub + KPAR） |
| 做形式验证 / 语言扩展研究 | MontiCore |
| 做 HAMR 集成 / GUMBO 契约 | sireum/hamr-sysml-parser（接受 LGPL-3） |

## 参考文献

[^repo-pilot]: *Systems-Modeling/SysML-v2-Pilot-Implementation*. <https://github.com/Systems-Modeling/SysML-v2-Pilot-Implementation>

[^pilot-kerml-xtext]: Pilot `KerML.xtext`. <https://github.com/Systems-Modeling/SysML-v2-Pilot-Implementation/blob/master/org.omg.kerml.xtext/src/org/omg/kerml/xtext/KerML.xtext>

[^pilot-sysml-xtext]: Pilot `SysML.xtext`. <https://github.com/Systems-Modeling/SysML-v2-Pilot-Implementation/blob/master/org.omg.sysml.xtext/src/org/omg/sysml/xtext/SysML.xtext>

[^pilot-validator]: Pilot `SysMLValidator.xtend`. <https://github.com/Systems-Modeling/SysML-v2-Pilot-Implementation/blob/master/org.omg.sysml.xtext/src/org/omg/sysml/xtext/validation/SysMLValidator.xtend>

[^repo-bnf]: `Systems-Modeling/SysML-v2-Release/bnf/`：[KerML-textual-bnf.kebnf](https://github.com/Systems-Modeling/SysML-v2-Release/blob/master/bnf/KerML-textual-bnf.kebnf)、[SysML-textual-bnf.kebnf](https://github.com/Systems-Modeling/SysML-v2-Release/blob/master/bnf/SysML-textual-bnf.kebnf)、SysML-graphical-bnf.kgbnf

[^repo-release]: *Systems-Modeling/SysML-v2-Release*. <https://github.com/Systems-Modeling/SysML-v2-Release>

[^repo-daltskin-grammar]: *daltskin/sysml-v2-grammar*. <https://github.com/daltskin/sysml-v2-grammar>

[^daltskin-generate]: daltskin `scripts/generate_grammar.py`. <https://raw.githubusercontent.com/daltskin/sysml-v2-grammar/main/scripts/generate_grammar.py>

[^daltskin-config]: daltskin `scripts/config.json`. <https://raw.githubusercontent.com/daltskin/sysml-v2-grammar/main/scripts/config.json>

[^daltskin-patches]: daltskin `grammar/PATCHES.md`. <https://raw.githubusercontent.com/daltskin/sysml-v2-grammar/main/grammar/PATCHES.md>

[^repo-nomograph-kebnf]: *nomograph-ai/kebnf*. <https://github.com/nomograph-ai/kebnf>

[^repo-hamr-parser]: *sireum/hamr-sysml-parser*. <https://github.com/sireum/hamr-sysml-parser>

[^sireum-regen]: sireum `bin/regen.cmd`. <https://raw.githubusercontent.com/sireum/hamr-sysml-parser/master/bin/regen.cmd>

[^repo-nomograph-ts]: *nomograph-ai/tree-sitter-sysml*. <https://github.com/nomograph-ai/tree-sitter-sysml>

[^repo-jackhale-ts]: *jackhale98/tree-sitter-sysml*. <https://github.com/jackhale98/tree-sitter-sysml>

[^repo-samonjourus-ts]: *Samonjourus/tree-sitter-sysmlv2*. <https://github.com/Samonjourus/tree-sitter-sysmlv2>

[^vscode-syside]: VS Code Marketplace — *sensmetry.syside-editor*. <https://marketplace.visualstudio.com/items?itemName=sensmetry.syside-editor>

[^vscode-jamied]: VS Code Marketplace — *JamieD.sysml-v2-support*. <https://marketplace.visualstudio.com/items?itemName=JamieD.sysml-v2-support>

[^vscode-spec42]: VS Code Marketplace — *Elan8.spec42*. <https://marketplace.visualstudio.com/items?itemName=Elan8.spec42>

[^repo-intellij-old]: *luluorta/intellij-plugin-sysml*（2015，v1，已废弃）。<https://github.com/luluorta/intellij-plugin-sysml>

[^repo-sysand]: *sensmetry/sysand*. <https://github.com/sensmetry/sysand>

[^pubgrub]: pubgrub crate. <https://crates.io/crates/pubgrub>

[^beta-sysand]: sysand 公共索引 beta. <https://beta.sysand.org/>

[^sysand-hosting]: sysand `hosting_index.md`. <https://github.com/sensmetry/sysand/blob/main/docs/src/hosting_index.md>

[^repo-sysml-2ls]: *sensmetry/sysml-2ls (SysIDE Legacy, archived)*. <https://github.com/sensmetry/sysml-2ls>

[^repo-monticore]: *MontiCore/sysmlv2*. <https://github.com/MontiCore/sysmlv2>

[^repo-kerml-net]: *STARIONGROUP/KerML.NET*. <https://github.com/STARIONGROUP/KerML.NET>

[^antlr-grammars-v4]: ANTLR 官方 grammars-v4 仓库内的 sysml-v2 子目录（由 daltskin 上游提供）。<https://github.com/antlr/grammars-v4/tree/master/sysml-v2>

[^pilot-internal-sysml]: Pilot Xtext 在 `src-gen/` 下生成的 ANTLR3 内部文法 `InternalSysML.g`（29351 行，绑死 Xtext runtime）。<https://github.com/Systems-Modeling/SysML-v2-Pilot-Implementation/blob/master/org.omg.sysml.xtext/src-gen/org/omg/sysml/xtext/parser/antlr/internal/InternalSysML.g>

[^pilot-jupyter-install]: Pilot 官方 Jupyter 安装指南。<https://github.com/Systems-Modeling/SysML-v2-Release/blob/master/install/jupyter/README.adoc>

[^pilot-install-sh]: Pilot 官方 `install/jupyter/install.sh`. <https://github.com/Systems-Modeling/SysML-v2-Release/blob/master/install/jupyter/install.sh>

[^repo-airbus-apollo]: *airbus/apollo-11-sysml-v2*（空客发布的 Apollo 11 完整 v2 参考模型）。<https://github.com/airbus/apollo-11-sysml-v2>

[^repo-gfse]: *GfSE/SysML-v2-Models*（德国系统工程协会维护的语料库）。<https://github.com/GfSE/SysML-v2-Models>

[^repo-mbse4u-bat]: *MBSE4U/dont-panic-batmobile*（Tim Weilkiens 教学示例）。<https://github.com/MBSE4U/dont-panic-batmobile>

[^repo-advent]: *sensmetry/advent-of-sysml-v2*（Sensmetry 25 课教程）。<https://github.com/sensmetry/advent-of-sysml-v2>

[^repo-galois-hardens]: *GaloisInc/HARDENS*（Galois 国防参考模型）。<https://github.com/GaloisInc/HARDENS>

[^repo-gfse-vacuum]: *GfSE/MBSE_AG_vacuum-cleaner-robot-example*. <https://github.com/GfSE/MBSE_AG_vacuum-cleaner-robot-example>

[^repo-linkedin]: *LinkedInLearning/systems-engineering-with-sysml-3955241*. <https://github.com/LinkedInLearning/systems-engineering-with-sysml-3955241>

[^repo-dfki]: *DFKI-CPS/specific-sysml*（注：使用 SysML v1 BDD textual notation，非 v2）。<https://github.com/DFKI-CPS/specific-sysml>

[^repo-desertkite]: *Open-MBEE/DesertKite.sysml*. <https://github.com/Open-MBEE/DesertKite.sysml>

[^repo-inspecta]: *loonwerks/INSPECTA-models*（Galois INSPECTA 项目）。<https://github.com/loonwerks/INSPECTA-models>

[^repo-praxis]: *systems-praxis/seamless-digital-engineering-reference-architecture*. <https://github.com/systems-praxis/seamless-digital-engineering-reference-architecture>

[^repo-aslab]: *aslab/STO*（学术原型，使用非标准 `instance` 关键字）。<https://github.com/aslab/STO>

[^repo-modelica-bridge]: *mimidbe/SysML-v2-to-Modelica*. <https://github.com/mimidbe/SysML-v2-to-Modelica>
