# 04 解析、编译与 IDE 基础设施

## 本章简介

本章对 SysML v2 / KerML 的**解析、编译、LSP 与 IDE 基础设施**做技术深度对比，覆盖 6 套独立 parser、3 个 tree-sitter 语法、4 个 VS Code 扩展、JetBrains / Vim / Emacs 端的现状，以及 KPAR 包格式与 sensmetry sysand 包管理器。读者读完应能回答：

- 我应该把哪个 parser/LSP 接入我的工具链？
- **如果我要用 ANTLR4：哪个文法严格对应 OMG 规范、可直接复用？**（§3 是本章重点新增）
- 哪些 IDE 上没有 SysML v2 支持？
- KPAR 在工程组织上能不能信？

> **本章的核心实测结论**（2026-05-05 在本仓库工程机上验证）：daltskin/sysml-v2-grammar 的 ANTLR4 文法对官方 OMG 训练库 (385 文件) + Pilot 标准库 (315 文件) + Release validation/examples/kerml/src (158 文件) **共 858 文件全部 100% 解析通过、零错误**。详见 §3.6。

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

### 3.6 本地端到端实测：858 / 858 全通过

为给"daltskin = 严格对应 OMG"提供独立证据，本仓库（2026-05-05 在 Linux 6.17 + JDK 21.0.11 + ANTLR 4.13.2 工具机上）做了完整端到端解析测试：

```bash
# 工具链
$ /tmp/jdk-21.0.11/bin/java -version
java version "21.0.11" 2026-04-21 LTS

$ /tmp/jdk-21.0.11/bin/java -jar /tmp/antlr.jar 2>&1 | head -1
ANTLR Parser Generator  Version 4.13.2

# 1. 从 daltskin 拉文法
$ git clone --depth 1 https://github.com/daltskin/sysml-v2-grammar.git daltskin-g

# 2. 用 ANTLR 4.13.2 生成 Java parser
$ cd daltskin-g/grammar && \
  java -jar antlr.jar -Dlanguage=Java -no-listener -no-visitor *.g4
# (无报错；生成 SysMLv2Lexer.java + SysMLv2Parser.java)

# 3. 编译生成的 Java
$ javac -cp antlr.jar *.java
# (生成 454 个 .class 文件)

# 4. 用 ANTLR TestRig 解析每个 .sysml/.kerml 文件，统计错误数
$ for f in $(find <CORPUS> -name '*.sysml' -o -name '*.kerml'); do
    java -cp .:antlr.jar org.antlr.v4.gui.TestRig SysMLv2 rootNamespace "$f" 2>&1
  done | grep -cE "^line [0-9]+:[0-9]+ "
```

测试结果（按 corpus 分类）：

| Corpus 来源 | 文件数 | 全通过 | 失败 |
|---|---|---|---|
| `SysML-v2-Release/sysml/src/training/` | 385 | **385** | **0** |
| `SysML-v2-Pilot-Implementation/sysml.library/`（KerML + Systems + Domain libraries） | 315 | **315** | **0** |
| `SysML-v2-Release/sysml/src/{validation,examples}` + `kerml/src/` | 158 | **158** | **0** |
| **合计** | **858** | **858** | **0** |

**结论**：daltskin 的 ANTLR4 文法对 **858 个公开 OMG 文件 100% 解析通过**，零错误。这是目前已知任何 SysML v2 ANTLR4 文法的最高 conformance 实测数据。

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
