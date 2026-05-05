# 04 解析、编译与 IDE 基础设施

## 本章简介

本章对 SysML v2 / KerML 的**解析、编译、LSP 与 IDE 基础设施**做技术深度对比，覆盖 6 套独立 parser、3 个 tree-sitter 语法、4 个 VS Code 扩展、JetBrains / Vim / Emacs 端的现状，以及 KPAR 包格式与 sensmetry sysand 包管理器。读者读完本章应能回答：「我应该把哪个 parser/LSP 接入我的工具链？哪些 IDE 上没有 SysML v2 支持？KPAR 在工程组织上能不能信？」

## 1 OMG 官方参考实现：Pilot Implementation

[Systems-Modeling/SysML-v2-Pilot-Implementation][^repo-pilot]（LGPL-3.0、221★、最近 commit 2026-05-04）是**唯一的 OMG 规范官方实现**，技术栈 Eclipse Modeling Tools 2025-12 + Java 21 + Xtext + EMF + Tycho Maven。规模与结构：

### 1.1 文法与元模型

Xtext 文法物理上分两个 OSGi bundle：

| Bundle | 文件 | 体量 |
|---|---|---|
| `org.omg.kerml.xtext` | `KerML.xtext`[^pilot-kerml-xtext] | ~28.7 KB / 1124 行 |
| `org.omg.sysml.xtext` | `SysML.xtext`[^pilot-sysml-xtext] | ~61.9 KB / 2438 行（约 1261 个生产规则） |
| `org.omg.kerml.expressions.xtext` | `KerML.expressions.xtext` | 表达式子语法，被 SysML 通过 `import` 复用 |

层叠组织：通过 Xtext 的 `Grammar.with` 语法继承 + EMF 元模型继承；KerML 元模型在 `org.omg.sysml/model`；SysML 是 KerML 的语法 + 元模型 conservative extension。

### 1.2 校验

`SysMLValidator.xtend`[^pilot-validator]单文件 76.6 KB / 1464 行，`@Check` 注解共 **73 条**校验规则、约 569 处 `error/INVALID_*` 标识符——这是**规范 well-formedness 条件直接落地的事实参考实现**。所有第三方 LSP 都需要 reproduce 这一规则集，但截至 2026-05 没有任何第三方完全做到。

### 1.3 索引、作用域与解析

- 增量解析：依赖 Xtext 的 ANTLR3-based parser + Eclipse builder dirty-state model——**全文件重解析 + 全局索引增量更新**，不是 Tree-sitter 式 token-level 增量。
- 错误恢复：ANTLR3 默认 `BacktrackingParser` + Xtext `PartialParser`。
- 作用域：`SysMLScopeProvider.xtend` 仅 3 KB（瘦壳）+ `SysMLGlobalScopeProvider`，重活在 `org.omg.sysml` 内核（即把 SysML 的导入 / 继承 / 特化语义映射到 Xtext IScope 的代码不在 Xtext 模块内，而在元模型核心）——这是为什么所有 fork 都很难脱离它。

### 1.4 Jupyter Kernel

`org.omg.sysml.jupyter.kernel`[^pilot-jupyter] 基于 SpencerPark/jupyter-jvm-basekernel；进程模型为**单 JVM 内嵌 Xtext + EMF runtime**（Java 21）。Magic command 共 11 个：`Eval / Export / Help / Listing / Load / Projects / Publish / Repo / Show / View / Viz`。**没有 LSP-style autocomplete**——补全要靠 Eclipse Xtext UI 模块，不在 Jupyter 端。REPL 解析靠 `org.omg.sysml.interactive` 模块在内存里 incremental link 一棵 EMF 树。

### 1.5 PlantUML 可视化

`org.omg.sysml.plantuml/` 实现明确为 **Visitor 模式**：`Visitor.java` 26 KB + 30 个 `V*.java` 子类（`VStructure` / `VAction` / `VStateMachine` / `VSequence` / `VCase` / `VRequirement` / `VBehavior` / `VTree` / `VComment` / `VMetadata` / `VPath` 等）。配置由 `SysML2PlantUMLStyle.java`（20 KB）控制，magic `%viz --view=...` 暴露视图选择。覆盖度比 SysON 更广（含 Sequence、Use Case），但**只读**渲染、PlantUML 文本输出、布局靠 PlantUML 自动；定位是「Jupyter `%viz` magic 与 Eclipse 文本编辑器中的快速预览」，是 fallback 而非一线工具。

### 1.6 构建

根 `pom.xml` + Tycho（Eclipse-flavored Maven），不是 Bazel；模块 50+ 个，子工程包括 `xpect.tests`（KerML/SysML 各一套）做 fixture 驱动的解析 + 校验回归。

## 2 第三方独立实现对比

截至 2026-05，存在 **6 套独立 parser**，覆盖 Java/Xtext、TS/Langium、TS/ANTLR4、Rust/nom、Java/MontiCore、C#/.NET 六种技术栈。重复造轮子的程度可以说"达到了学术研究级"。

### 2.1 sensmetry/sysml-2ls (SysIDE Editor Legacy)

[sensmetry/sysml-2ls][^repo-sysml-2ls] 在 2026-04 被官方 archive，README 顶部标 deprecated，被闭源商业 [Syside Editor][^syside-rebirth]替代（Sensmetry 公告称基于"Syside Pro Suite 三年开发的全新技术栈"，性能 50× legacy）。新版 VS Code marketplace 4254 安装[^vscode-syside]，是当前装机量最大的 SysML v2 扩展，**但闭源**——这是**整个开源生态的最大隐忧**。

Legacy 版技术内核：

- pnpm monorepo 6 个 package：`syside-base / cli / languageserver / languageclient / protocol / vscode`。
- 文法：[Langium][^langium]，自己写 `SysML.langium`（55 KB / 2174 行）+ `KerML.langium`（28 KB / 1197 行）+ 拆出 `*.interfaces.langium` 与 `KerML.expressions.langium`，**与 Pilot 同样三层结构**（重写而非端口）。
- `parser.ts` 还**手工 patch** 了 Langium 自动生成的 parser，绕开 `SelfReferenceExpression` 等左递归坑。
- 校验：`kerml-validator.ts`（56 KB）+ `sysml-validator.ts`（52 KB），合计 108 KB，比 Pilot 的 76 KB 单文件粒度更细。
- LSP feature 矩阵：hover、go-to-def（`linker.ts` 23 KB）、find-refs、rename、formatter、semantic tokens（`semantic-token-provider.ts` 13.7 KB）、completion（`completion-provider.ts` 20.5 KB）、`execute-command-handler.ts` 18.2 KB（含 S-expression dump）。**未见 inlay hints**，code actions 仅基本 quick-fix。

### 2.2 daltskin 三件套

[daltskin/sysml-v2-grammar][^repo-daltskin-grammar]（MIT、6★）、[daltskin/sysml-v2-lsp][^repo-daltskin-lsp]（MIT、12★、TypeScript 3 MB）、[daltskin/VSCode_SysML_Extension][^repo-daltskin-vscode]（VS Code marketplace `JamieD.sysml-v2-support`、961 安装[^vscode-jamied]）。

技术亮点：

- **ANTLR4 文法由 OMG KEBNF 自动生成**——`make generate` 流程从 [SysML-v2-Release][^repo-release] 拉规范 BNF 转 `.g4`，配合 26 KB 的 `PATCHES.md` 修补 KEBNF 与 ANTLR4 之间的 gap。
- 生成 `SysMLv2Lexer.g4`（5.6 KB）+ `SysMLv2Parser.g4`（46.7 KB）。
- `make sdk` 一键产出 10 种语言 SDK（Java / Cpp / CSharp / Dart / Go / JS / PHP / Python3 / Swift / TypeScript）。
- LSP server 跑在 Node.js 进程，提供 IPC（VS Code）、HTTP（浏览器）、stdio（Python/Jupyter）三种 client 适配。
- 包含 `analysis/` `mcp/`（**MCP server**！）、`model/`、`parser/`、`providers/`、`symbols/`，并附带一个 `sysml-mcp` CLI 给 LLM 用。
- LSP 矩阵：诊断、symbols、hover、go-to-def、find refs、completion、semantic tokens、folding、rename、code actions、复杂度分析、Mermaid 预览（6 种图）。

技术上比 sysml-2ls Legacy 更"OMG-orthodox"——KEBNF 自动同步避免了规范变更带来的人工劳动。

### 2.3 elan8/spec42 + sysml-v2-parser（Rust 栈）

[elan8/sysml-v2-parser][^repo-sysml-v2-parser]（v0.9.0、MIT、2★）：

- Rust **手写 nom 8 + nom_locate** 解析器组合子（不是 chumsky / pest / lalrpop / tree-sitter）。
- `src/ast.rs` 单文件 83 KB——巨型 AST 定义。
- `src/parser/` 按语义 split 成 28 个文件（`part.rs` 48 KB、`package.rs` 33 KB、`action.rs` 28 KB、`requirement.rs` 23 KB、`lex.rs` 21 KB...）。
- 提供 `parse()` 严格模式 + `parse_for_editor()` **resilient 模式**（部分 AST + diagnostics）——这是为编辑器设计的关键功能，比 Pilot 的 ANTLR3 错误恢复更现代。
- CI 跑 SysML-v2-Release fixture 做 conformance gating，criterion 跑 bench。

[elan8/spec42][^repo-spec42]（v0.22.0、MIT、6★、2026-05-04）：

- Workspace 三 crate：`kernel`（语义 / 索引 / lsp_runtime）、`plugins`、`server`（**tower-lsp 0.20** + `clap`）。
- VS Code 扩展 `Elan8.spec42`（182 安装[^vscode-spec42]），LSP feature 含诊断、completion、hover、navigation、document symbols、Model Explorer、Model Visualizer（4 视图：General / Interconnection / Action Flow / State Transition）。
- Zed 集成靠 `zed/languages/sysml/` 下手写的 `highlights.scm`（10 KB）+ `folds.scm` + `indents.scm`（用 spec42 自己的 grammar 别名调度）。

注：`kernel/Cargo.toml` 里 `tree-sitter` 依赖只是给生成的 Rust 代码做 token 处理，**SysML parsing 本身不走 tree-sitter，走 nom**。

### 2.4 MontiCore/sysmlv2

[MontiCore/sysmlv2][^repo-monticore]（33★、Java、Gradle）：

- 12 个 `.mc4` 文法文件模块化拆分：`SysMLBasis / SysMLActions / SysMLCases / SysMLConnections / SysMLConstraints / SysMLExpressions / SysMLImportsAndPackages / SysMLOccurrences / SysMLParts / SysMLStates / SysMLViews / SysMLv2.mc4`——这是 MontiCore "language composition" 哲学的体现，**Pilot 单文件 2438 行的等价物在这里被拆 12 块、可独立 conservative extension**。
- 价值定位（README 自述）：(a) 第二个独立 parser 给 Pilot 做 cross-check；(b) MontiCore 的 **symbol management** 基础设施（compiler-grade）解耦"模型符号"与"目标语言映射"，比 EMF 灵活；(c) 形式化验证后端的扩展点。
- LSP 自动从语言生成（MontiCore MCLSG），但 `language-server/README` 自己列了一堆已知 bug——VS Code client 需要 hack `SYSMLV2_LSP_PORT` env var 才能启动。可以看出：**学术严谨、工程粗糙**。

### 2.5 STARIONGROUP/KerML.NET

[STARIONGROUP/KerML.NET][^repo-kerml-net]（Apache-2.0、1★、最近 push 2025-03、NuGet 包 `KerML.NET`）：

- 范围明确：**只有 KerML 的内存模型 + JSON 序列化器 + 代码生成器**（`KerML.NET.CodeGenerator/`、`KerML.NET.Serializer.Json/`），**没有 parser**。
- 即「读 KerML AST → .NET 对象」的库，对应 SysML v2 工具链中 .NET 那一侧，给 [CDP4-COMET][^repo-cdp4]之类的 ECSS-E-TM-10-25 工具用。
- 是目前**唯一的 .NET 端 KerML 实现**。

### 2.6 六套对比矩阵

| 实现 | 文法体量 | 错误恢复 | 增量 | 校验规则 | 性能 | License | 状态 |
|---|---|---|---|---|---|---|---|
| Pilot | KerML 1124 行 + SysML 2438 行 | ANTLR3 backtrack | 全文件 + 索引增量 | 73 条 `@Check` | 大模型慢 | LGPL-3 | 规范权威 |
| SysIDE Legacy | 2174 + 1197 行 `.langium` | 全文件 | 否 | KerML+SysML 108 KB | 慢 | (闭源新版替代) | archive |
| 新版 Syside Editor | 闭源 | 闭源 | 闭源 | 闭源 | 50× legacy | 闭源 | 商用 |
| daltskin ANTLR4 | KEBNF 自动生成 | ANTLR4 | 否 | 基本 | n/a | MIT | 活跃 |
| spec42 / parser | nom 28 文件 / AST 83 KB | resilient | nom 友好 | 规范级 | 快 | MIT | 个人 |
| MontiCore | 12 个 .mc4 | MontiCore | 否 | 规范级 | n/a | BSD-3 派生 | 学术 |
| KerML.NET | 无 parser | n/a | n/a | n/a | n/a | Apache-2 | .NET 端孤本 |

## 3 Tree-sitter 三家并存

GitHub 上并存 **3 个 tree-sitter 语法** + 1 个 KEBNF 转换器：

| 仓库 | grammar.js | 状态 | 测试覆盖 |
|---|---|---|---|
| [nomograph-ai/tree-sitter-sysml][^repo-nomograph-ts] | **71 KB** | 最完整、有 CI parse-coverage | 覆盖 OMG training / examples / validation / std-library + Sensmetry advent；发布到 crates.io / PyPI / npm；自带 highlights / tags / locals / folds / indents queries |
| [jackhale98/tree-sitter-sysml][^repo-jackhale-ts] | 51 KB | 单人维护，README 描述详尽 | 213 OMG 文件 + 92/94 标准库零错误 |
| [Samonjourus/tree-sitter-sysmlv2][^repo-samonjourus-ts] | 1.3 KB（拆 sysml/kerml 子目录） | **WIP，25 章只完成 3** | 早期 |
| [nomograph-ai/kebnf][^repo-nomograph-kebnf] | — | OMG KEBNF → ANTLR4 / tree-sitter 转换器 | 声明覆盖全部 KerML / SysML v2 规则 |

**Nomograph 的策略**最值得关注：**KEBNF 自动转换**与 daltskin 的 ANTLR4 自动生成思路同源，是规范同步的可持续路径。

注：spec42 的 `zed/languages/sysml/highlights.scm` 是 tree-sitter query 文件但**绑定的不是上述任意一个 grammar**——是给 Zed 自己的解析器用的，标记 token kind。这是 Zed 用户当前唯一可用方案。

## 4 编辑器扩展生态盘点

### 4.1 VS Code Marketplace（按安装数）

| 扩展 | 安装数 | 状态 |
|---|---|---|
| `sensmetry.syside-editor`[^vscode-syside] | **4254** | 闭源商业（前身 sysml-2ls 已 archive） |
| `JamieD.sysml-v2-support`（daltskin）[^vscode-jamied] | **961** | MIT、活跃 |
| `Elan8.spec42`[^vscode-spec42] | **182** | MIT、个人 |
| `Ellidiss.sysml-ellidiss` | — | 商业产品入口 |

### 4.2 JetBrains Marketplace

**完全空白**。唯一候选 [luluorta/intellij-plugin-sysml][^repo-intellij-old] **2015 年最后一次 push、0★、是 SysML v1**——10 年没动。**IntelliJ 用户完全裸奔**，是生态最严重的空白。详见 [09-缺口与机会](09-gaps-opportunities.md) §A.4。

### 4.3 Neovim / Emacs

未找到任何专门打包的 plugin。Neovim 用户唯一可走的路是装 nomograph-ai 或 jackhale98 的 tree-sitter grammar，自己拼 LSP（接 daltskin 或 spec42 的 server）——但**没有打包好的 nvim plugin**（如 `nvim-lspconfig` 内置条目）。Emacs 同理无 `eglot` 配置示例公开发布。

### 4.4 Zed

spec42 自带 query files（见 §2.3），是 Zed 用户当前唯一可用方案。

### 4.5 Jupyter

- **官方 Pilot kernel**（见 §1.4）：单 JVM 内嵌 Xtext，11 magic，**无 LSP-style autocomplete**。
- **第三方**：daltskin 的 LSP 仓库带 `clients/python/sysml_lsp_demo.ipynb`，是用 stdio JSON-RPC 直驱 LSP server——这是更现代的做法（用 LSP 的 completion / hover），相当于绕开了 OMG kernel 的不足，但**不是 Jupyter kernel**，而是 notebook 调用 LSP 的 demo。**没有 LSP-driven Jupyter kernel** 是显著的生态空白。

## 5 KPAR 包格式与 sensmetry sysand

包管理是这一面唯一的亮点。[sensmetry/sysand][^repo-sysand]（Rust、29★、MIT/Apache-2.0、2026-05 活跃）的工程化设计在整套 v2 工具链里是**最干净、最现代、最敢复用 OMG 规范**的选择。

### 5.1 包格式：`.kpar`

直接用 KerML 1.0 spec §10.3 定义的 **`.kpar`**（KerML 项目交换归档，本质是 ZIP），不另发明格式：

- `.project.json`：公开 metadata（`publisher`、`license` 必须 SPDX 表达式、`version` 鼓励 SemVer 2.0、`usage` 数组列依赖）。
- `.meta.json`：源文件索引 / 校验和 / 时间戳。

**直接复用 OMG 标准**而非自创——是整个生态最干净的设计选择。

### 5.2 Lockfile

`sysand-lock.toml`（`core/src/lock.rs` 20 KB + `lock_tests.rs` 23.5 KB——锁文件实现工程量与逻辑核心相当）。

### 5.3 Resolver

用 [pubgrub crate 0.4][^pubgrub]——这与 `uv`、`cargo-next` 同款 SAT-style resolver，比 npm/pip 的回溯式更现代。

### 5.4 Registry

**没有强制中央 registry**——依赖 IRI 灵活解析：`http(s)://`（KPAR 文件 / 目录 / git repo）、`file://`、`urn:kpar:...`、`ssh://`、`git+...://`。Sensmetry 跑了一个公共 [beta.sysand.org][^beta-sysand] 索引，但 [hosting_index.md][^sysand-hosting] 教用户**自托管**——通过 `sysand env` 把包放到目录里再用任意 HTTP server 暴露。

### 5.5 与 npm/cargo/pip 比

语义最像 **cargo**（lockfile + 多源 + git/url 直链），但用 **IRI 替代 URL** 是给 OMG 模型语义留出 namespace 的设计；不像 npm 强中心化、不像 pip 的版本依赖松散。**模型依赖语义独有**：通过 KerML 的 `library package` 概念，import 路径与包路径是统一的，这是编程语言包管理器没有的层面。

### 5.6 工程缺口

- **没有 SBOM / 包签名**（KPAR 还没有 sigstore 等签名）。
- **没有 LSIF / SCIP 索引器**（大模型代码搜索无依赖）。
- **不调 validator / linter**（仅做 KPAR project interchange + checksum）。

## 6 综合判断：5 个生态空白

1. **没有 IntelliJ 插件**（生态最大空白，企业 SysML 用户多用 IntelliJ）。
2. **没有打包好的 Neovim / Emacs 插件**（裸 tree-sitter + 自配 LSP 不算）。
3. **没有 LSP-driven Jupyter kernel**（OMG kernel 没有 completion，daltskin 的 notebook 不是 kernel）。
4. **新版 Sensmetry 闭源**——失去了最快的开源实现。
5. **没有公开横向 benchmark**——除了 Sensmetry 一句"50×"和 daltskin / spec42 内部 bench，**没有任何公开横向 benchmark 论文 / 博客**。

## 7 推荐路径（按使用场景）

| 场景 | 推荐组合 |
|---|---|
| 想读规范权威实现 | Pilot（Xtext / Java / Eclipse） |
| 做 Rust / CLI / MCP 集成 | spec42 + sysml-v2-parser |
| 嵌任意编辑器（含 Helix / Zed / nvim） | nomograph-ai tree-sitter + daltskin 或 spec42 LSP |
| 做 .NET 后端 | KerML.NET |
| 包管理 / CI 流水线 | sysand（pubgrub + KPAR） |
| 做形式验证 / 语言扩展研究 | MontiCore |

## 参考文献

[^repo-pilot]: *Systems-Modeling/SysML-v2-Pilot-Implementation*. <https://github.com/Systems-Modeling/SysML-v2-Pilot-Implementation>

[^pilot-kerml-xtext]: *KerML.xtext* in Pilot. <https://github.com/Systems-Modeling/SysML-v2-Pilot-Implementation/blob/master/org.omg.kerml.xtext/src/org/omg/kerml/xtext/KerML.xtext>

[^pilot-sysml-xtext]: *SysML.xtext* in Pilot. <https://github.com/Systems-Modeling/SysML-v2-Pilot-Implementation/blob/master/org.omg.sysml.xtext/src/org/omg/sysml/xtext/SysML.xtext>

[^pilot-validator]: *SysMLValidator.xtend* in Pilot. <https://github.com/Systems-Modeling/SysML-v2-Pilot-Implementation/blob/master/org.omg.sysml.xtext/src/org/omg/sysml/xtext/validation/SysMLValidator.xtend>

[^pilot-jupyter]: Pilot Jupyter kernel directory. <https://github.com/Systems-Modeling/SysML-v2-Pilot-Implementation/tree/master/org.omg.sysml.jupyter.kernel>

[^repo-sysml-2ls]: *sensmetry/sysml-2ls* (archived). <https://github.com/sensmetry/sysml-2ls>

[^syside-rebirth]: Sensmetry. *Syside Editor Rebirth*. <https://sensmetry.com/syside-editor-rebirth-sysml-v2-0-50x-speed-up-license-change-free-as-before/>

[^vscode-syside]: VS Code Marketplace — *sensmetry.syside-editor*. <https://marketplace.visualstudio.com/items?itemName=sensmetry.syside-editor>

[^langium]: Langium 主页. <https://langium.org/>

[^repo-daltskin-grammar]: *daltskin/sysml-v2-grammar*. <https://github.com/daltskin/sysml-v2-grammar>

[^repo-daltskin-lsp]: *daltskin/sysml-v2-lsp*. <https://github.com/daltskin/sysml-v2-lsp>

[^repo-daltskin-vscode]: *daltskin/VSCode_SysML_Extension*. <https://github.com/daltskin/VSCode_SysML_Extension>

[^vscode-jamied]: VS Code Marketplace — *JamieD.sysml-v2-support*. <https://marketplace.visualstudio.com/items?itemName=JamieD.sysml-v2-support>

[^repo-spec42]: *elan8/spec42*. <https://github.com/elan8/spec42>

[^repo-sysml-v2-parser]: *elan8/sysml-v2-parser*. <https://github.com/elan8/sysml-v2-parser>

[^vscode-spec42]: VS Code Marketplace — *Elan8.spec42*. <https://marketplace.visualstudio.com/items?itemName=Elan8.spec42>

[^repo-monticore]: *MontiCore/sysmlv2*. <https://github.com/MontiCore/sysmlv2>

[^repo-kerml-net]: *STARIONGROUP/KerML.NET*. <https://github.com/STARIONGROUP/KerML.NET>

[^repo-cdp4]: *STARIONGROUP/COMET-IME-Community-Edition*. <https://github.com/STARIONGROUP/COMET-IME-Community-Edition>

[^repo-nomograph-ts]: *nomograph-ai/tree-sitter-sysml*. <https://github.com/nomograph-ai/tree-sitter-sysml>

[^repo-jackhale-ts]: *jackhale98/tree-sitter-sysml*. <https://github.com/jackhale98/tree-sitter-sysml>

[^repo-samonjourus-ts]: *Samonjourus/tree-sitter-sysmlv2*. <https://github.com/Samonjourus/tree-sitter-sysmlv2>

[^repo-nomograph-kebnf]: *nomograph-ai/kebnf*. <https://github.com/nomograph-ai/kebnf>

[^repo-intellij-old]: *luluorta/intellij-plugin-sysml*（已废弃，2015 年 v1 工程）。<https://github.com/luluorta/intellij-plugin-sysml>

[^repo-sysand]: *sensmetry/sysand*. <https://github.com/sensmetry/sysand>

[^pubgrub]: pubgrub crate（Rust SAT-style version solver）。<https://crates.io/crates/pubgrub>

[^beta-sysand]: sysand 公共索引 beta. <https://beta.sysand.org/>

[^sysand-hosting]: sysand `hosting_index.md`. <https://github.com/sensmetry/sysand/blob/main/docs/src/hosting_index.md>

[^repo-release]: *Systems-Modeling/SysML-v2-Release*. <https://github.com/Systems-Modeling/SysML-v2-Release>
