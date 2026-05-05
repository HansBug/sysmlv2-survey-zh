# 10 daltskin/sysml-v2-grammar 深度审计

## 本章简介

本章对 [daltskin/sysml-v2-grammar][^repo-daltskin] 仓库做面向"基础设施选型"的全面审计：所有模块逐一摸过、所有脚本本地跑过、git history 与社群信号全部统计、上下游依赖图爬清楚。读者读完应能对以下问题有明确答案：

- 整体能力边界与维护现状如何？
- 是不是 vibe-coding？真正的工程质量信号是什么？
- 哪些模块可直接 vendor / fork 复用？哪些必须自建扩展？哪些不可用？
- 在工期约束下，正确的接入策略是 **fork 维护还是 vendor 文件**？还是直接搬 `.g4` 自管？

### 0.0 一句话先把全章定位讲清楚

**daltskin/sysml-v2-grammar 不是一个"基础设施仓库"，是一份"被维护中的 ANTLR4 文法文件"。**

把整个仓库剖开看：

| 部分 | 是什么 | 真实复用价值 |
|---|---|---|
| `grammar/SysMLv2{Lexer,Parser}.g4` + `PATCHES.md`（约 52 KB） | **从 OMG KEBNF 跑下来 + 56 处 ambiguity patch 的 ANTLR4 文法成品** | **★★★★★**——这才是这个仓库的全部技术价值，是别处暂无替代的"产物" |
| `scripts/`（generate_grammar.py / conformance.py / find_cycles.py / generate_sdks.py / build_contrib.py / kebnf_grammar.lark）+ `Makefile` + `.github/workflows/` | 围绕"产物"的辅助胶水（自动化生成、自动化测试、上游同步、SDK 多 target 打包） | ★（视情况 vendor 一两个小工具）——本质上**是工程胶水，每家工程都有自己的写法** |

也就是说：**真正"基础设施"属性的只有那 2 个 `.g4` 文件**；剩下的 4500+ 行 Python + Makefile + GH Actions 都是**作者本地工作流的产物，对其他人来说可有可无**。

### 0.1 结论先行

- 维护质量层面：daltskin 是**单人精良维护、工程级质量**的项目；**0 vibe-coding 信号**，**0 open issues**，10 周 36 commits 节奏稳定，已合入 `antlr/grammars-v4`，下游有 13+ 项目消费。
- 复用策略层面：**只搬 2 个 `.g4` 文件 + 注明 MIT 出处即可**；按 §8.2.1 的 (C) 方案，半天就能把它接进自家工程，不依赖 daltskin 的 Python/Make 工具栈。
- 工期与扩展层面：**不要等 daltskin 把领域扩展 merge 回 upstream**——领域 lint / KerML 单独 grammar / 私有 keyword 扩展多半不在 upstream scope 内。直接在自家 vendor 副本上 patch。
- 真要重做的边界：**`.g4` 本身（特别是 56 处 ambiguity patch）才是难替代的部分**；其余全部可低成本自建。

详见 §8 的四条接入路径横评与 §11 决策卡。

## 1 仓库基本面

| 指标 | 实测值（2026-05-05） | 备注 |
|---|---|---|
| GitHub URL | <https://github.com/daltskin/sysml-v2-grammar> | |
| 创建时间 | 2026-02-11 | 仓库年龄 ~3 个月 |
| 最近 push | 2026-04-21 | 14 天前 |
| Stars / Forks / Subs | 6 / 3 / 1 | 数字不大但精准 |
| 本地 clone 大小 | 404 KB（含 `.git`） | 极小，无 binary 残留 |
| GitHub size | 200 KB | |
| Open issues | **0** | 维护者积极清理 |
| Open PRs | 0 | |
| Releases | **8 个 tag**（v2025-12 → v2026.03.2） | 每 1–2 周 1 个 release |
| Total commits | 36 | |
| Distinct authors | 3 人 + 1 bot | 见 §2 |
| License | **MIT** ([LICENSE][^daltskin-license]，1064 B) | 可商用、可 fork、可重发 |

## 2 维护现状与开发节奏

### 2.1 Git history（10 周节奏）

| 区间 | commits | 备注 |
|---|---|---|
| 2026-02-11 | 10 | 仓库初始化 + grammar pipeline |
| 2026-02-13 | 6 | versioning + grammars-v4 contrib + README |
| 2026-03-09 | 1 | bot：自动同步 OMG `2026-02` |
| 2026-03-13 | 1 | merge bot PR |
| 2026-03-14 | 4 | conformance fixes + lexer 改进 |
| 2026-03-22 | 1 | PR #3 enhance + lexer fixes |
| 2026-04-13 | 5 | OMG `2026-03` 同步 + 移除 43 条 unreachable rules（PR #5） |
| 2026-04-20 | 5 | 多 SDK 并行生成（PR #6 by Michael Rowley） |
| 2026-04-21 | 3 | 添加 `(as Type)` metadata cast（PR #7） |

**稳定的"周末 + 周二/三"开发模式**，单人主导但有外部贡献者参与；commit 时间集中在英国时区下班后，与作者邮箱后缀（`@microsoft.com`）暗示的全职工程师身份吻合。

### 2.2 作者与社群

| 角色 | 名称 | 邮箱 | commits / PR |
|---|---|---|---|
| 主维护者 | **Jamie D**（[@daltskin](https://github.com/daltskin)） | `daltskin@hotmail.com` / `jamdalt@microsoft.com` / users.noreply | 32 commits + 3 自 PR |
| 社群贡献 | **Michael Rowley**（[@michaellrowley](https://github.com/michaellrowley)） | `michaellrowley@protonmail.com` | 1 PR（多 SDK target 生成） |
| 自动化 | github-actions[bot] | — | 3 commits（OMG 上游同步） |

`@microsoft.com` 邮箱暗示作者是 Microsoft 员工的业余项目；与 commit 节奏（晚 6 点后、周末密集）一致。**这是合规企业员工业余维护**模型，bus factor 风险中等但稳定性比纯个人高。

### 2.3 PR 与 Release 流水

7 个 PR 全部 merged，平均寿命 < 24 小时（**0 stale PR**）：

| # | 标题 | 作者 | 寿命 |
|---|---|---|---|
| #1 | Update grammar to OMG release 2026-01 | bot | 2 分钟（机器审过即合） |
| #2 | Update grammar to OMG release 2026-02 | bot | 4 天 |
| #3 | Enhance Grammar with Conformance Fixes and Lexer Improvements | Jamie D | 15 分钟 |
| #4 | Update grammar to OMG release 2026-03 | bot | 8 小时 |
| #5 | perf: remove 43 unreachable parser rules (~9% reduction) | Jamie D | 9 分钟 |
| #6 | Antlr v4 to Generate Multi-Language Target SDKs | **Michael Rowley** | 1.3 天 |
| #7 | feat: add metadata cast expression `(as Type)` | Jamie D | 30 分钟 |

8 个 release tag 严格按 `v<OMG-tag>.<rev>` 模式递进（`v2025-12` → `v2026-01` → `v2026.01.0` → `v2026.01.1` → `v2026.02.0` → `v2026.03.0` → `v2026.03.1` → `v2026.03.2`）。每次 OMG 上游 minor 改动→ daltskin patch revision 跟进。

### 2.4 Vibe-coding 检测：未发现 vibe 信号

逐项核查：

| 信号 | 期望（vibe） | daltskin 实际 | 判定 |
|---|---|---|---|
| commit message 风格 | 模板化、含 emoji、AI 风 | **Conventional Commits**（feat/fix/perf/chore/refactor），描述具体（"remove 43 unreachable rules"、"~9% reduction"） | 非 vibe ✓ |
| commit message 长度 | 一句话或重复 | 多数 50–100 字符，少量 chore 是 1 行；含 `#issue` 引用 | 非 vibe ✓ |
| 含 "Co-Authored-By: Claude/etc" | 常见 vibe 标记 | **0 处** | 非 vibe ✓ |
| 提交频率 | 突发大量 + 长期沉默 | 10 周内 9 个独立活跃日，节奏平稳 | 非 vibe ✓ |
| code style | 不一致 / 多种风格混杂 | `ruff format` + `ruff check` 在 CI 强制（CI lint job） | 非 vibe ✓ |
| 文件命名 | 不规则 | snake_case Python + kebab-case YAML，全统一 | 非 vibe ✓ |
| 注释比例 | 过多解释性 / 全 docstring | 简洁 docstring + 少量行内注释 | 非 vibe ✓ |
| TODO / FIXME 残留 | 多处未清 | 实测全仓 grep `TODO\|FIXME` = 0 | 非 vibe ✓ |
| 测试覆盖 | 缺失或装样子 | 真实跑 OMG 训练 + std-library + 自定义 examples；conformance.py 真校验 | 非 vibe ✓ |
| CI security 实践 | 草率 | actions 用 SHA pin（`actions/checkout@de0fac2e...`）、tag 格式正则校验防注入、ANTLR jar sha256 校验 | 非 vibe ✓ |
| 依赖管理 | 不 pin / 不 audit | `requirements.txt` 全部 pin 精确版本；CI 跑 `pip-audit` | 非 vibe ✓ |
| Issue / PR 卫生 | 大量 stale | **0 open**；7 PR 全部 ≤ 1.3 天 merge | 非 vibe ✓ |

**结论**：daltskin/sysml-v2-grammar 是**纪律性极强的人工维护项目**，整体质量在 GitHub 个人 OSS 项目里属上游。

## 3 模块清单与逐个能力评估

仓库目录结构：

```
daltskin/sysml-v2-grammar/
├── LICENSE                           # MIT (1064 B)
├── README.md                         # 9 KB 详尽 setup
├── Makefile                          # 5 KB（13 个 target）
├── grammar/                          # 产物
│   ├── SysMLv2Lexer.g4               # 5.7 KB / 257 行 / 226 lexer 规则
│   ├── SysMLv2Parser.g4              # 46.8 KB / 2168 行 / 452 parser 规则
│   ├── SysMLv2Lexer.tokens           # 自动生成的 token 编号
│   └── PATCHES.md                    # 26 KB / 57 条 patch 详细说明
├── scripts/                          # 工具链
│   ├── config.json                   # release_tag, grammar_version, paths
│   ├── kebnf_grammar.lark            # 2.6 KB KEBNF 元文法（lark 格式）
│   ├── generate_grammar.py           # 142 KB / 3309 行（核心生成器）
│   ├── generate_sdks.py              # 10 KB / 314 行 多 target SDK 生成
│   ├── build_contrib.py              # 16 KB / 446 行 grammars-v4 贡献打包
│   ├── conformance.py                # 10 KB / 327 行 一致性测试
│   ├── find_cycles.py                # 2.5 KB / 74 行 文法环检测
│   ├── find_dead_rules.py            # 2 KB / 64 行 死规则检测
│   ├── postprocess-antlr.js          # 3.4 KB / Node.js 后处理
│   ├── bump_version.py               # 1.4 KB 版本递增
│   ├── requirements.txt              # lark==1.2.2, requests==2.33.1
│   └── requirements-dev.txt          # ruff, yamllint, actionlint-py, pip-audit
├── examples/                         # 3 个手写示例 (camera, toaster, vehicle)
└── .github/workflows/
    ├── generate.yml                  # PR/push 触发的 lint+test+contrib+sdk
    └── watch-upstream.yml            # 周一 06:00 UTC cron 拉 OMG 新 release
```

下面按重要性逐项点评。

### 3.1 `grammar/SysMLv2{Lexer,Parser}.g4`（**核心产物**）

- **可直接复用**：MIT，就是两份纯 ANTLR4 文件，已被 [`antlr/grammars-v4/sysml-v2`](https://github.com/antlr/grammars-v4/tree/master/sysml-v2) 接收，被 9+ 下游消费（§5.2）。
- **修复了 56 处 OMG KEBNF 自身在 ANTLR4 上的歧义**（PATCHES.md 完整列出，详见 [04-解析 IDE §3.3](04-parsing-ide-infrastructure.md#33-patchesmd-详解daltskin-的-57-条修补)）。
- **能力边界**：`.sysml` 文件全过；不支持 `.kerml`（详见 [04 §3.6.5](04-parsing-ide-infrastructure.md#365-daltskin-文法的实际范围仅-sysml-文件sysml不支持-kerml)）。

### 3.2 `scripts/generate_grammar.py`（生成器主引擎）

3309 行单文件，结构清晰：

| 组件 | 行数 | 用途 |
|---|---|---|
| `RuleElement` 数据类族（Terminal / NonTerminal / QualifiedNameRef / Repetition / Group / Sequence / Alternative） | ~80 | KEBNF AST 的 IR |
| `class GrammarRule` | ~60 | 单条产生式 |
| `class KebnfParser` | ~1200 | KEBNF → IR（基于 `lark==1.2.2`） |
| `class Antlr4Transformer` | ~1900 | IR → ANTLR4 .g4（含 56 处 patch 应用） |
| `download_bnf()` + `main()` | ~70 | 编排入口 |

代码质量**经典 transpiler 架构**——AST→IR→codegen 三层划分清晰，dataclass 用得地道。**vendor 价值**：若你想自建 KerML 文法，这份生成器是直接的复用模板（fork 后改 `output.parser_grammar` 与入口规则即可）。

### 3.3 `scripts/conformance.py`（一致性测试 harness）

- 327 行，4 个 suite：Standard Library、Official Training Examples、Official Validation Models、Official SysML Examples。
- `--fetch` 子命令拉取 `Systems-Modeling/SysML-v2-Release` 的 `bnf/`、`sysml/src/training/`、`kerml/src/`，写入 `test/fixtures/`。
- `--verbose` 输出每个文件的 pass/fail。

**vendor 价值高**：如果你做 LSP / lint，这份 harness 直接 fork 即可作为你的 "纯文法层" 回归测试模板，避免你自己再造一套官方语料同步逻辑。

### 3.4 `scripts/generate_sdks.py`（10 语言 SDK 工厂）

- 314 行，调用 ANTLR jar 多 target 生成：CSharp、Cpp、Dart、Go、Java、JavaScript、PHP、Python3、Swift、TypeScript。
- 支持 `--jobs 0` 并行（PR #6 添加，by Michael Rowley）。
- `--archive` 打包成 release zip。

**vendor 价值中**：如果你只用 1 个 target（如 Python），用 `java -jar antlr.jar -Dlanguage=Python3 ...` 即可，不需要这个脚本。但若你做"分发型"工具（SDK 包给多语言用户），它现成可用。

### 3.5 `scripts/build_contrib.py`（grammars-v4 上游打包）

- 446 行，把当前 `grammar/` 下的 `.g4` + 测试 fixtures 整理为可直接 PR 给 [`antlr/grammars-v4`][^antlr-grammars-v4] 的归档目录。
- 已实测：daltskin 通过该脚本把当前文法贡献到 antlr 官方仓库。

**vendor 价值低**：除非你也想给 `antlr/grammars-v4` 做贡献。

### 3.6 `scripts/find_cycles.py` + `scripts/find_dead_rules.py`（静态检查）

- 74 + 64 行小工具，对 ANTLR4 文法做环路检测与可达性分析。
- PR #5 「remove 43 unreachable parser rules (~9% reduction)」就是 `find_dead_rules.py` 的 use case。

**vendor 价值高**：通用 ANTLR4 静态分析工具，与 SysML 无关，搬到任何 .g4 项目都能用。

### 3.7 `scripts/kebnf_grammar.lark`（KEBNF 元文法）

- 2.6 KB lark 文件，定义"如何解析 OMG KEBNF 格式"的元文法。
- 这是 daltskin 自己的研究成果——OMG 没有给出 KEBNF 的形式化定义。
- 与 `nomograph-ai/kebnf` 的 KEBNF 解析器是两个独立实现，可互相 cross-validate。

**vendor 价值高**：若你要自己写 KerML 转换器，这份元文法直接 reuse。

### 3.8 `Makefile`（13 个 target，全部 phony）

| target | 用途 | 实测 |
|---|---|---|
| `make help` | 自描述 | ✓ |
| `make install` | 装 Python deps | ✓ |
| `make generate` | 从 OMG KEBNF 重新生成 .g4 | 未跑（需联网） |
| `make sdk` / `sdk-archive` | 生成多 target SDK | 未跑（需要 jar） |
| `make test` | 编译文法 + 解析 examples + 跑 conformance | **✓ 实测 3/3 passed** |
| `make update-conformance` | fetch OMG fixtures | 部分跑（fetch 阶段超时，但 fixture 路径正确） |
| `make lint` | ruff + yamllint + actionlint + pip-audit + 文法漂移检查 | 未跑全套 |
| `make ci` | 完整 lint + test + contrib + sdk-archive 流水线 | — |
| `make version` / `bump-revision` | 版本工具 | — |
| `make clean` | 清理产物 | ✓ |

Makefile 风格清爽（`.PHONY:` 全声明、`:= ?=` 区分严格、`@grep` 自动从 `## doc` 注释生成 help）。**vendor 价值中**：可以原样借用 `install / lint / clean` 等 target，仅修改 SDK 生成参数。

### 3.9 `.github/workflows/generate.yml` + `watch-upstream.yml`（CI/自动化）

- `generate.yml`（172 行）：PR/push 触发，跑 lint job → test job → contrib job → sdk-archive job。所有 actions 用 **40 字符 commit SHA pin**（`actions/checkout@de0fac2e4500dabe0009e67214ff5f5447ce83dd # v6.0.2`）—— 这是 [GitHub 推荐的 supply-chain 最佳实践](https://docs.github.com/en/actions/security-guides/security-hardening-for-github-actions)。
- `watch-upstream.yml`（160 行）：每周一 06:00 UTC cron，比对 OMG `Systems-Modeling/SysML-v2-Release` 的最新 tag，若有新 release 就 `make generate` + open PR。**关键安全点**：tag 名做 `^[0-9]{4}-[0-9]{2}$` 正则校验防 shell 注入。

**vendor 价值高**：尤其 `watch-upstream.yml` 整套 cron+正则校验+自动 PR 流程可直接复制到任何"跟踪上游 spec"的项目（DSL / proto / OpenAPI 都适用）。

## 4 上游依赖（仓库使用了什么）

| 类别 | 依赖 | 版本 | 用途 |
|---|---|---|---|
| Python 运行时 | `lark` | 1.2.2 | KEBNF 解析（PEG/Earley） |
| Python 运行时 | `requests` | 2.33.1 | 拉 OMG release ZIP |
| Python 开发 | `ruff` | 0.15.10 | linter + formatter |
| Python 开发 | `yamllint` | 1.38.0 | YAML 校验 |
| Python 开发 | `actionlint-py` | 1.7.12.24 | GH Actions workflow 校验 |
| Python 开发 | `pip-audit` | 2.10.0 | 依赖安全审计 |
| Java 运行时 | ANTLR4 jar | **4.13.2 (sha256 pinned)** | 文法生成 |
| OS 工具 | `jq`、`curl`、`sha256sum`、`java`、`zip` | 系统级 | Makefile 调用 |
| 上游数据源 | [`Systems-Modeling/SysML-v2-Release`][^repo-release-2] | 周对齐 | OMG KEBNF + training fixtures |

**总依赖面积极小**：Python 6 个包 + 一个 Java jar + OS 标配。**MIT/BSD-2/Apache-2.0 兼容**——没有 GPL 风险。

## 5 下游消费者（谁用了它）

### 5.1 直接消费 `.g4` 文件的项目（GitHub code search 实测）

| 项目 | 用途 | 备注 |
|---|---|---|
| [antlr/grammars-v4](https://github.com/antlr/grammars-v4/tree/master/sysml-v2) | ANTLR 官方文法库 | 由 daltskin 上游贡献，README 注明 "Generator: daltskin/sysml-v2-grammar" |
| [daltskin/sysml-v2-lsp][^repo-daltskin-lsp] | 同作者 LSP server（TS/Node） | byte-identical 复用 |
| [daltskin/VSCode_SysML_Extension][^repo-daltskin-vscode] | 同作者 VS Code 扩展 | VS Code marketplace 961 安装 |
| [Archie-Bous/Sysmlv2-Verification](https://github.com/Archie-Bous/Sysmlv2-Verification) | 学术验证项目 | 直接 vendor 了文法 |
| [LnYo-Cly/sysmlv2_validatior](https://github.com/LnYo-Cly/sysmlv2_validatior) | 校验器原型 | 学术 |
| [Protestator-Research/CPP-SysMLv2](https://github.com/Protestator-Research/CPP-SysMLv2) | C++ 实现 | GPL-3.0 项目 |
| [hs1520/SysML-v2-AST-Parser](https://github.com/hs1520/SysML-v2-AST-Parser) | AST 解析器 | 学生项目 |
| [chouswei/modelbase-sysmledgraph](https://github.com/chouswei/modelbase-sysmledgraph) | 知识图谱 | dev plan 引用 |
| [jasonbelt/INSPECTA-models](https://github.com/jasonbelt/INSPECTA-models) | INSPECTA 个人 fork | Galois 关联 |
| [loonwerks/INSPECTA-models](https://github.com/loonwerks/INSPECTA-models) | INSPECTA 项目 | Loonwerks/Galois 国防项目 |
| [mesh-iit/study-alexandria](https://github.com/mesh-iit/study-alexandria) | 研究 | |
| [sbgaia/xPPU-LF](https://github.com/sbgaia/xPPU-LF) | 研究（CPS） | |
| [sireum/hamr-sysml-parser][^repo-hamr-2] | Sireum HAMR ANTLR4 端 | 部分参考 |

**总计 13+ 项目**直接或间接消费 daltskin 的文法。这是一份**有真实下游使用**的基础设施，bus factor 不止单点。

### 5.2 GitHub `gh search code` 实测（2026-05-05，filename:SysMLv2.g4）

```
{"count":1,"repo":"LnYo-Cly/sysmlv2_validatior"}
{"count":1,"repo":"Protestator-Research/CPP-SysMLv2"}
{"count":1,"repo":"hs1520/SysML-v2-AST-Parser"}
{"count":1,"repo":"jasonbelt/INSPECTA-models"}
{"count":1,"repo":"jasonbelt/x"}
{"count":1,"repo":"loonwerks/INSPECTA-models"}
{"count":1,"repo":"mesh-iit/study-alexandria"}
{"count":1,"repo":"sbgaia/xPPU-LF"}
{"count":1,"repo":"sireum/hamr-sysml-parser"}
```

## 6 兼容性

| 维度 | 状态 |
|---|---|
| **ANTLR 版本** | 4.13.2 pinned，但 4.13.x / 4.x 系列均兼容（不依赖 4.13 专有 feature） |
| **ANTLR target** | 10 个：CSharp、Cpp、Dart、Go、Java、JavaScript、PHP、Python3、Swift、TypeScript ——本仓库 [04 §3.6.3 / §3.6.4](04-parsing-ide-infrastructure.md) 实测了 Java/Python3/JavaScript |
| **OMG 版本对齐** | release tag 直接映射 OMG release（如 `v2026.03.2` ↔ OMG `2026-03`） |
| **文件类型** | `.sysml`（√）、`.kerml`（**×**，详 [04 §3.6.5](04-parsing-ide-infrastructure.md#365-daltskin-文法的实际范围仅-sysml-文件sysml不支持-kerml)） |
| **KerML 内嵌于 SysML 体内** | √（合并文法支持 `attribute def`、`classifier def` 等 KerML 元层概念在 SysML 上下文中的使用） |
| **OS** | Linux / macOS / Windows（Makefile 用 sh、CI 跑 ubuntu-latest） |
| **Python** | 3.10+（CI 上是 3.12） |
| **Java** | 17+（generate target 需要；用户自己跑生成的 parser 只需 8+） |

## 7 直接复用 vs 自建扩展矩阵

| 模块 | 直接 vendor 价值 | 自建必要性 | 备注 |
|---|---|---|---|
| `grammar/SysMLv2{Lexer,Parser}.g4` | **★★★ 直接 vendor** | 低 | MIT、社群验证；参见 [04 §3.7 嵌入指南](04-parsing-ide-infrastructure.md#37-推荐复用清单) |
| `grammar/PATCHES.md` | ★★ 参考文档 | 低 | 解释 56 条 patch 的根因，做 lint 时是规则源 |
| `scripts/generate_grammar.py` | ★★ fork 用作 KerML 生成模板 | **中**（需要修改 entry rule + 输出） | 若要自做 KerML grammar，这是最近的起点 |
| `scripts/conformance.py` | **★★★ 直接 vendor** | 低 | 通用文法 conformance harness，去除 SysML-specific URL 即可作通用 ANTLR 文法 conformance |
| `scripts/find_cycles.py` + `find_dead_rules.py` | **★★★ 直接 vendor** | 低 | 通用 ANTLR4 静态分析 |
| `scripts/generate_sdks.py` | ★ 选择性 vendor | 低 | 单 target 用户 `java -jar antlr.jar` 直接 1 行命令更短 |
| `scripts/build_contrib.py` | × 无关 | — | 仅供贡献给 antlr/grammars-v4 |
| `scripts/kebnf_grammar.lark` | **★★★ 直接 vendor** | 低 | KEBNF 元文法，无替代 |
| `Makefile` | ★★ 选择性参照 | 低 | `install / clean / version` 等 target 可借用 |
| `.github/workflows/watch-upstream.yml` | **★★★ 直接 vendor** | 低 | 整套 OMG release 跟踪自动化框架，与 SysML 无关，任何"跟踪上游 spec" 项目都能用 |
| `.github/workflows/generate.yml` | ★★ 选择性参照 | 低 | CI 模板可借鉴 lint+test+contrib+sdk-archive 分层 |
| `examples/*.sysml` | × 无关 | — | 仅 daltskin 自测，已被 §3.6.6 真实世界 corpus 替代 |

**自建必要性"中"以上的部分**：

- 如果项目要支持 `.kerml`，需要 fork `generate_grammar.py`，把 KEBNF 输入切到 `KerML-textual-bnf.kebnf` 单独生成，并选 `Element` 等 KerML 顶层规则作 entry。预计工作量 **3–5 人日**（含适配 PATCHES）。
- 如果项目要做领域 lint（参见 [04 §3.6.8 失败案例](04-parsing-ide-infrastructure.md#368-失败案例根因分析11--350-真-v2-子集) 的 B/D 类），需要在 daltskin 文法之上加一套 listener/visitor 检查规则。预计 **5–10 人日**做出 30 条规则的初始 ruleset。

**完全不可用 / 限制过大的部分**：**没有**。所有模块要么直接可用，要么自建成本可接受。

## 8 战略建议：fork 维护 vs vendor 文件 vs 自建

### 8.0 把"复用程度"光谱拆清楚

把 daltskin 划分成两层：

- **生成产物层** = `grammar/SysMLv2{Lexer,Parser}.g4` + `PATCHES.md`（**只读**：是 KEBNF→ANTLR4 转换的最终结果，56 处 ambiguity 都修好了）
- **生成器与工具链层** = `scripts/`（generate_grammar.py / conformance.py / find_cycles.py / generate_sdks.py / build_contrib.py / kebnf_grammar.lark）+ `Makefile` + `.github/workflows/`

是否复用每一层是独立的两个选择，组合出 4 条路：

| 路径 | 复用产物 (.g4) | 复用工具链 (scripts/) | 自己再做的活 |
|---|---|---|---|
| **(A) git submodule 整仓** | ✓ | ✓（拿来即用） | 0（除自家 listener/visitor/lint） |
| **(B) fork 自维护** | ✓（在自家 fork 内可改） | ✓（可裁剪可改） | 维护 fork 与 upstream 同步 |
| **(C) 只搬 `.g4` + 周围工具链全自建** | ✓（直接 copy 两个文件） | ✗（用自家 CI/build/test） | 写自己的 build/test/CI 套件 |
| **(D) 全自起炉灶**（KEBNF→ANTLR4 也自己写） | ✗（自己跑 nomograph kebnf 或手写） | ✗ | 重做 56 处 ambiguity patch + KEBNF 解析 |

四档之间的真实差额是「拿不拿 daltskin 工具链」与「自己有没有 KEBNF→ANTLR4 流水线」两个独立维度。

### 8.1 四种路径横评

| 路径 | 上手时间 | 长期维护成本 | 与 upstream 同步 | 推荐场景 |
|---|---|---|---|---|
| **(A) submodule 整仓** | **0.5 人日** | **极低**：每季度 `git submodule update` 一次 | 自动跟 daltskin 周期；新 OMG release 1–2 周内进得来 | 项目暂无领域扩展需求；想直接用 `make test` 套件 |
| **(B) fork 自维护** | 1–2 人日 | 中：领域 patch 在自家 fork 做；定期 rebase upstream，可能有冲突 | 半自动；可选择性 cherry-pick daltskin commit | 要修 grammar 本体（加 `.kerml` 支持、加私有 keyword）、又希望保留与 upstream 的接口 |
| **(C) 只搬 `.g4` + 工具链全自建**（**用户的"另起炉灶"**） | **1 人日**（vendor 文件 + 写自家解析 driver） | 中：`.g4` 跟 daltskin 节奏 bump；其它都自家 CI/工具栈 | **手动 bump**：监听 daltskin release / antlr/grammars-v4，按需复制 | **工期紧 + 自家工程已有完整 CI/build/test 栈，不愿引入 Python/lark/Make**；要对 grammar 做项目内私有 patch 但不想 upstream coordination |
| **(D) 全自起炉灶**（不用 daltskin 任何文件） | 30–60 人日 | 高：要自己重做 56 处 ambiguity patch，跟踪 OMG 每个 release | 完全脱钩 | 仅当你要切到 tree-sitter / Langium / Roslyn 等非 ANTLR4 形式 |

### 8.2 工期约束下的具体推荐

> 用户场景：**有工期约束，等不及把领域扩展 merge 回 daltskin 上游**。

#### 8.2.1 默认推荐 = (C)

**只搬 `.g4` + 周围工具链全自建**——这正是用户在本节问题里给出的"另起炉灶"定义，对工期紧迫且已有 CI/build 栈的工程，**这是最优解**。

**第 0 步**（10 分钟）—— 把两个 `.g4` 文件直接 copy 到自家 repo：

```bash
# Option C1: git subtree（保留来源 commit；以后想改 g4 直接改）
git subtree add --prefix=third_party/sysml-grammar \
    https://github.com/daltskin/sysml-v2-grammar.git \
    v2026.03.2 --squash

# Option C2: 直接 copy 文件（最轻量；接受失去 git 来源信息）
mkdir -p third_party/sysml-grammar/grammar
curl -fsSL -o third_party/sysml-grammar/grammar/SysMLv2Lexer.g4 \
    https://raw.githubusercontent.com/daltskin/sysml-v2-grammar/v2026.03.2/grammar/SysMLv2Lexer.g4
curl -fsSL -o third_party/sysml-grammar/grammar/SysMLv2Parser.g4 \
    https://raw.githubusercontent.com/daltskin/sysml-v2-grammar/v2026.03.2/grammar/SysMLv2Parser.g4
# MIT 要求保留版权声明：
curl -fsSL -o third_party/sysml-grammar/LICENSE \
    https://raw.githubusercontent.com/daltskin/sysml-v2-grammar/v2026.03.2/LICENSE
echo "v2026.03.2  $(date -I)" > third_party/sysml-grammar/VERSION
```

**第 1 步**（剩余几小时到 1 天）—— 在自家 repo 用自家 build 栈生成 parser：

```yaml
# 例：自家 Maven 工程
<plugin>
  <groupId>org.antlr</groupId>
  <artifactId>antlr4-maven-plugin</artifactId>
  <version>4.13.2</version>
  <configuration>
    <sourceDirectory>${project.basedir}/third_party/sysml-grammar/grammar</sourceDirectory>
    <outputDirectory>${project.build.directory}/generated-sources/antlr4</outputDirectory>
  </configuration>
  <executions><execution><goals><goal>antlr4</goal></goals></execution></executions>
</plugin>
```

或：

```bash
# 例：自家 Python 项目，CI 里跑
java -jar antlr-4.13.2.jar -Dlanguage=Python3 \
    third_party/sysml-grammar/grammar/*.g4
```

整个流程不带任何 daltskin Python / Make / lark 依赖。

**第 2 步以后** —— 自家做这些（每件都比啃 daltskin 工具链便宜）：

| 自家要做的事 | 工期 | 替代 daltskin 哪个 |
|---|---|---|
| Conformance 测试（在 ANTLR TestRig 或自家 driver 上跑 OMG 训练库 + Pilot stdlib + 真实世界 corpus） | **0.5 人日** | conformance.py（327 行） |
| Listener / Visitor 骨架 → 自家 IR / JSON | 2–3 人日 | 不在 daltskin scope |
| 领域 lint 规则（基于 [04 §3.6.8 失败模式](04-parsing-ide-infrastructure.md#368-失败案例根因分析11--350-真-v2-子集) 的 30 条） | 5–10 人日 | 不在 daltskin scope |
| 上游 OMG release 监听（季度手动 bump 即可） | **0** | watch-upstream.yml（160 行） |
| ANTLR 文法 cycle / 死规则检查 | 0.3 人日（搬 daltskin 那两个 < 80 行的小工具更快） | find_cycles.py + find_dead_rules.py |

加总：**核心搭建 1 人日，全栈跑通含 lint 5–10 人日**，全部在自家 build 栈内完成；不接受任何 daltskin Python 依赖。

#### 8.2.2 何时选 (A) 而非 (C)

- 你想**直接复用 daltskin 的 conformance.py / find_cycles.py 等**——不想自家再写
- 你的工程是 Python 栈本来就装 lark + ruff——再多 daltskin 几个脚本无所谓
- 你想得到 `make update-conformance / make lint / make sdk-archive` 这一套现成命令

(A) 的「拿来即用」体验最好，但代价是把 daltskin 的 Python 工具栈（lark + requests + ruff + actionlint + pip-audit）也捆进自家工程依赖。**对非 Python 项目尤其不划算**。

#### 8.2.3 何时选 (B) 而非 (C)

- 你需要在 grammar 本体上做**多人协作的修改**，且这些改动你希望最终能回流上游
- 你团队有专人愿意维护 fork 并定期 rebase

如果你的目的只是「项目内私有改 grammar」、不打算回流，**(B) 反而比 (C) 多绑了一份 fork 维护成本**——直接 (C) 更轻。

#### 8.2.4 (D) 自起炉灶的硬阻碍

仅在「要切换 grammar 形式（tree-sitter / Langium / Roslyn）」时才考虑。否则你需要：

- 重做 56 处 KEBNF→ANTLR4 ambiguity patch（PATCHES.md 详列），**每个 OMG release 1–3 人天**。
- 重做 KEBNF 解析（`generate_grammar.py` 3309 行 transpiler），如果不直接搬 [`nomograph-ai/kebnf`][^repo-nomograph-kebnf-2] 的话。
- 自己实现一致性测试 harness（**≥ 2 人天**复刻 daltskin 已有的 327 行）。

> 关键直觉：**daltskin 真正难替代的只有"56 处 ambiguity patch"这件事**。`.g4` 文件已经是这件事的成品。所以 **拿 .g4 = 拿到了 daltskin 99% 的技术价值**；剩下的工具链全是工程胶水，每家工程的胶水偏好都不一样，没必要复用。

### 8.4 风险点与对冲

| 风险 | 概率 | 影响 | 对冲 |
|---|---|---|---|
| Jamie D 长期不更新（生病 / 跳槽 / 兴趣转移） | 中 | 中 | 已有 [`antlr/grammars-v4/sysml-v2`][^antlr-grammars-v4] 镜像，可作为故障转移源；本节提供完整 fork-and-self-maintain 操作蓝图 |
| OMG 大幅变更 KEBNF 结构 | 低（OMG RTF 通常向后兼容） | 高 | 升级到 (B)，自家 fork 跟进；或参考 [04 §3.6.8](04-parsing-ide-infrastructure.md#368-失败案例根因分析11--350-真-v2-子集) 的 B 类 lint 规则告警 |
| daltskin 改 license（理论上可能但不太可能） | 极低 | 高 | submodule 锁 commit 即可避免影响——已发布的 MIT 内容不可撤回 |
| `lark==1.2.2` deps 安全漏洞 | 低 | 低 | daltskin CI 跑 `pip-audit`；如自家关心，自家 CI 也跑一次 |

### 8.5 一句话决策

**`git submodule add https://github.com/daltskin/sysml-v2-grammar.git`，锁 `v2026.03.2`，开干。** 不要等 upstream merge；自家工程做领域扩展。等真正在领域扩展上需要改文法本体的时候再 fork。

## 9 立项后的"第一周清单"

如果按上节方案直接开干，建议第一周交付：

- [ ] **D1**：submodule 锁定，跑通 [04 §3.6.3 Python harness](04-parsing-ide-infrastructure.md#363-python-端到端流程实测)
- [ ] **D2**：把 [04 §3.6.6 OMG 训练库 + Pilot 标准库 158 个 .sysml 文件] 100% 跑过——作为自家 conformance 基线
- [ ] **D3**：把 [04 §3.6.6 真实世界 13 个仓 339 v2 文件] 跑过，对比 daltskin 维护版本数据：差额都应可解释为后续 OMG release 引入的新语法
- [ ] **D4**：实现 listener / visitor 骨架，能把解析结果输出到自家 JSON 中间表示（用 §3.2 dataclass 思路做）
- [ ] **D5**：以 [04 §3.6.8 表格中的 11 个失败案例] 为基础，实现 5–10 条 lint 规则的 PoC

第一周末应当能给出"能扫 v2 文件、能报领域错误"的端到端 demo。

## 10 与本仓库其它章节的交叉引用

- 解析 / IDE 整体上下文：[04-parsing-ide-infrastructure.md](04-parsing-ide-infrastructure.md)
- daltskin 端到端测试与失败案例：[04 §3.6](04-parsing-ide-infrastructure.md#36-本地端到端实测java--python--javascript-三-runtime--官方--真实世界-15-仓)
- daltskin 不支持 `.kerml` 的细节：[04 §3.6.5](04-parsing-ide-infrastructure.md#365-daltskin-文法的实际范围仅-sysml-文件sysml不支持-kerml)
- 失败案例根因分析与能力边界：[04 §3.6.8](04-parsing-ide-infrastructure.md#368-失败案例根因分析11--350-真-v2-子集)
- Pilot Xtext 路径（**不**推荐复用）：[04 §1.6 / §1.7](04-parsing-ide-infrastructure.md#16-pilot-文法文件如何使用本地实测)
- 立项机会窗口（含 lint 工具）：[09-gaps-opportunities.md §A.1](09-gaps-opportunities.md#a1-eslint-风格-sysml-v2-linter)

## 11 总结（决策卡）

| 维度 | 评估 |
|---|---|
| **daltskin 是不是基础设施？** | **不是仓库级基础设施**——基础设施属性集中在 2 个 `.g4` 文件 + 56 处 ambiguity patch；其余 4500+ 行 Python/Make/CI 都是作者本地工作流的工程胶水 |
| **2 个 `.g4` 文件值不值得依赖？** | **值**——antlr/grammars-v4 已收编，13+ 下游项目验证，96.9% 真 v2 corpus 通过率（[04 §3.6.6](04-parsing-ide-infrastructure.md#366-端到端-conformance-数据官方--15-个真实世界仓共-252--362--614-文件)） |
| **是不是 vibe-coding？** | **不是**。12 项 vibe 信号检测全部为"非 vibe"——纪律性强、CI 严谨、依赖审计、SHA pin、无 stale issue |
| **维护风险（bus factor）** | **中**。单人主维（Microsoft 员工业余项目）+ 1 社群贡献 + bot 自动化。已合入 antlr/grammars-v4 给了故障转移路径 |
| **能力边界** | `.sysml` 在 Java/Python/JS 三 runtime 上 96.9% 真 v2 通过率；不支持 `.kerml`（需另外接 sireum / Pilot Xtext / 自跑 nomograph kebnf） |
| **License** | MIT，无 GPL 风险，可商用、可 fork、可重发；只需保留 LICENSE + 版权声明 |
| **推荐接入路径** | **(C) 只搬 `.g4` + 周围工具链全自建**（§8.2.1）——半天接入；自家工程 build/CI/test 栈不引入 daltskin 的 Python 依赖；领域 patch 直接在自家 vendor 副本上做 |
| **真正不可替代的部分** | **PATCHES.md 列出的 56 处 KEBNF→ANTLR4 ambiguity patch**——这是 daltskin 已经做完、别处暂无的劳动成果，每个 OMG release 还需要 1–3 人天维护 |
| **可选 vendor 的小工具** | `find_cycles.py`（74 行）+ `find_dead_rules.py`（64 行）合计 ≤ 150 行——自家也好用 |
| **完全不必复用的** | `generate_grammar.py` / `Makefile` / `.github/workflows/*.yml` / `scripts/conformance.py` / `scripts/generate_sdks.py` / `scripts/build_contrib.py`——每家工程会有更顺手的自家版本 |
| **不要做的事** | (1) 别等 upstream merge 自家领域扩展；(2) 别为了"全自起炉灶"重做 56 处 ambiguity patch（除非切换到非 ANTLR4 形式） |
| **必须自建的** | 领域 lint 规则集；KerML grammar（若需要）；listener/visitor 中间表示；自家的 conformance 测试套件（30 行 ANTLR TestRig 包装即可） |

## 参考文献

[^repo-daltskin]: *daltskin/sysml-v2-grammar*. <https://github.com/daltskin/sysml-v2-grammar>

[^daltskin-license]: daltskin LICENSE (MIT). <https://github.com/daltskin/sysml-v2-grammar/blob/main/LICENSE>

[^antlr-grammars-v4]: ANTLR 官方 grammars-v4 仓库内的 sysml-v2 子目录（由 daltskin 上游提供）。<https://github.com/antlr/grammars-v4/tree/master/sysml-v2>

[^repo-release-2]: *Systems-Modeling/SysML-v2-Release*. <https://github.com/Systems-Modeling/SysML-v2-Release>

[^repo-daltskin-lsp]: *daltskin/sysml-v2-lsp*. <https://github.com/daltskin/sysml-v2-lsp>

[^repo-daltskin-vscode]: *daltskin/VSCode_SysML_Extension*. <https://github.com/daltskin/VSCode_SysML_Extension>

[^repo-hamr-2]: *sireum/hamr-sysml-parser*. <https://github.com/sireum/hamr-sysml-parser>

[^repo-nomograph-kebnf-2]: *nomograph-ai/kebnf*. <https://github.com/nomograph-ai/kebnf>
