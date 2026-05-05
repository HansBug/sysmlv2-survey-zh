# 00 总览

## 本章简介

本章给出 SysML v2 / KerML 生态在 2026-05 的全景速览，包括：调研动机与方法学、生态分层、关键时间节点、五维度基础设施速判、12 章导航地图、按角色阅读路径。希望读者在 10 分钟之内即可建立全局心智地图，再按需选择性深入后续章节。

## 1 调研动机

OMG SysML v2 自 2017 年立项[^seidewitz-2017]、2023 年起进入 Beta 公开评审、2025-06-30 完成 Final Adoption[^omg-final]、2025-09 进入 FTF 整理期、2026-03 以 `formal/2026-03-0x` 文档号正式出版以来，已正式进入"工业部署窗口"。但与 1990 年代末 UML、2000 年代初 SysML v1 类似，**语言规范 ready ≠ 工具链 ready**。本仓库的目的在于回答一组工程实务问题：

1. SysML v2 现在能拿来做什么？哪些事情还做不了？
2. 开源生态的厚度比起 UML / SysML v1 / AADL / Modelica 等同类语言达到了几成？
3. 从「学术 → 工业 → 生态」三层视角看，目前最有价值的开源四件套是什么？最大的隐忧是什么？
4. 中文学术圈，特别是北航，参与 SysML v2 标准化与研究的实情如何？
5. 如果要立项做 SysML v2 相关的开源工具或学术工作，最值得抓的机会窗口在哪里？
6. 现成的 ANTLR4 文法（daltskin/sysml-v2-grammar）作为基础设施靠不靠谱？怎么接进自家工程？
7. 公开能拿到多少真实 v2 代码语料？跑通解析率多少？

下文与各章共同回答这七组问题。

## 2 调研方法

本仓库的全部结论由**多轮并行 AI 调研代理 + 本地端到端实测 + 人工审校**产出，时间锚定 2026-05-05。各轮覆盖：

- **首轮 5 路代理（学术地图 + 开源生态 + 北航专项 + 基础设施四子线 + 同类语言基线）**：建立 12 章骨架。
- **第二轮（OMG 规范深读 + ANTLR 文法对应分析）**：本地装 Pilot Jupyter kernel（micromamba + `jupyter-sysml-kernel=0.58.0`）跑通官方 Subsetting Example 取得 UUID v5 输出；本地装 ANTLR 4.13.2 + JDK 21 用 daltskin 文法解析 OMG 训练库。
- **第三轮（daltskin 全仓审计 + Python/JS runtime 实测 + 失败根因分析）**：对 daltskin 36 个 commit、7 个 PR、PATCHES.md 全部 57 项做了逐项核查；Python/Java/JavaScript 三个 ANTLR4 runtime 解析 158 个官方 + 339 个真实世界 .sysml 验证一致性。
- **第四轮（真实世界语料广覆盖检索）**：再派代理找 60+ 个公开 GitHub 仓库；克隆 25 个新仓本地实测；总计 40 仓 / 3255 个 .sysml 文件实测 daltskin 通过率 94.7%；按工具签名识别 Pilot/sysand/Cameo/Jupyter 创作来源。
- **第五轮（学术文献扩充）**：对 2024–2026 年所有 SysML v2 相关论文做主题/方法/区域 9 维分类，扩展 [02-学术地图](02-academic-landscape.md)。

所有代理独立工作、互不干扰；最终结果由维护人（[@HansBug](https://github.com/HansBug)）合并、勘误、统一引用规范。引用规范见 [AGENTS.md §3](../AGENTS.md)。

## 3 12 章导航地图

| # | 文档 | 一句话定位 | 适合谁 |
|---|---|---|---|
| 00 | [总览](00-overview.md)（本章） | 调研动机、方法、12 章导航、阅读路径 | 所有人首读 |
| 01 | [标准状态](01-standard-status.md) | OMG 4 份规范深读、KerML/SysML v2 关系、文本/图形语法、API、UUID v5、KEBNF 元语法、SysML v1↔v2 转换的本地实测结论 | 想理解语言本身的人 |
| 02 | [学术地图](02-academic-landscape.md) | 9 维主题分类的论文索引（语义、桥接、LLM、工业、形式化、变种、综述、博士论文、区域研究）；主要研究团队；论文-代码对应表 | 学术研究者、立项调研 |
| 03 | [北航专项](03-beihang-investigation.md) | 实锤北航关联（岳涛 OMG SysML v2 contributor + 吴际 PSUM 工作组 + Internetware 2025 全员北航 LLM × SysML 论文）；候选人勘误 | 中文学术圈合作 |
| 04 | [解析 / IDE 基础设施](04-parsing-ide-infrastructure.md) | 9 套独立 parser、3 个 tree-sitter；ANTLR4 严格对应分析 + 858 文件本地实测 + 11 个失败根因；Pilot Jupyter kernel 实测；KPAR/sysand 包管理 | 工具链负责人、IDE 开发者 |
| 05 | [形式化与验证](05-formal-verification.md) | 11 路径深度评估：HAMR/SysMD/Lean 4 verified-mbse/Gamma/openCAESAR/Imandra/Monterey Phoenix/Living Blueprint 等 | 形式化工程师、安全关键系统 |
| 06 | [可视化与协作](06-visualization-collaboration.md) | SysON 架构详解；OMG SysML v2 API & Services 端点矩阵 + **规范级缺陷**（无 merge 端点）；Flexo MMS；OSLC；MCP 三家；diff/merge 现状 | 协作平台开发、Web 工具方 |
| 07 | [代码生成与执行](07-codegen-execution.md) | AADL/Modelica/OWL/HAMR codegen 路径状态评估；行为执行；CI/CD；linter | 转换工程、仿真桥接 |
| 08 | [基线对照](08-baseline-comparison.md) | UML/SysML v1/AADL/Modelica/Capella/BPMN/TLA+/Alloy 八语言成熟度五维评分矩阵 | 选型决策、横向对比 |
| 09 | [缺口与机会](09-gaps-opportunities.md) | 短期（1–3 月）/ 中期（3–12 月）/ 长期（1–3 年）机会窗口；按维度归类的开源空白 | 立项、研究方向选择 |
| 10 | [daltskin 深度审计](10-daltskin-deep-audit.md) | daltskin/sysml-v2-grammar 全仓 36 commits + 11 模块逐一摸过 + 跑通；vibe-coding 12 信号检测全过；上下游依赖图；接入策略 (A/B/C/D) 推荐 (C) git submodule 工具链全自建 | ANTLR 工具链负责人、立项 |
| 11 | [真实世界 v2 代码语料](11-real-world-corpora.md) | 40 公开仓 × 3255 .sysml 实测；daltskin 通过率 94.7%；工具签名分布；OTHER 类失败 Beta1/2 已弃语法清单；6 个旗舰语料 + 5 stress-test + 3 应剔除 | LLM 微调/评测、conformance baseline 选定 |
| 12 | [ModelCopilot / WSE-Lab 深度档案](12-modelcopilot-deep.md) | 北航 WSE-Lab + ModelCopilot 平台全景；8 仓库逐项深读（PSUM-SysMLv2 7 案例、LLM4MDE 254 篇 SLR、IsingBench 4 求解器+3 经典 SE 数据集等）；arXiv 2602.21641 PSUM 解剖；公众号现状；与 OMG/华望/Cameo/Loughborough 战略对位；6 月/1 年/3 年走向预测 | 跟踪国内 SysML v2 学术节点；与 BUAA 合作前必读 |
| ★ | [参考文献](references.md) | 全部 cite key 集中索引（标准 / 论文 / OSS / 商用 / 教程 / Baseline） | 找原文出处 |

## 4 生态分层

整体生态可分为五层：

| 层级 | 内容 | 关键产物 | 详见 |
|---|---|---|---|
| **标准层** | OMG 规范、KerML 元模型、API 规范、KEBNF 语法 | 4 份 `formal/2026-03-0x` 文档 + 训练样例 + 标准库 | [01](01-standard-status.md) |
| **参考实现层** | OMG 官方 Pilot Implementation 与配套 API/Cookbook/Java/Python client、AADL Library；Pilot Jupyter kernel | LGPL-3.0；本地实测可装可跑 | [04 §1](04-parsing-ide-infrastructure.md) |
| **第三方实现层** | sensmetry SysIDE、Eclipse SysON、daltskin、elan8 spec42、MontiCore、KerML.NET、Open-MBEE Flexo MMS、HAMR；ANTLR4 文法已合入 antlr/grammars-v4 | 9 套 parser、3 套 tree-sitter；解析率 96.9%（真 v2 子集） | [04](04-parsing-ide-infrastructure.md) / [05](05-formal-verification.md) / [06](06-visualization-collaboration.md) / [07](07-codegen-execution.md) / [10](10-daltskin-deep-audit.md) |
| **学术研究层** | 语义批评、形式化、桥接、LLM × SysML v2、工业 case study、博士论文 | 2024–2026 30+ 篇相关论文 | [02](02-academic-landscape.md) |
| **产业商用层** | Cameo / CATIA No Magic、IBM Rhapsody SE、Siemens System Modeler / Capital、PTC Windchill Modeler、Ansys SAM、Visual Paradigm | 2026 Q1 商用线全面进入 v2 | [README](../README.md#商用厂商支持速览) |
| **真实语料层** | GitHub 公开 SysML v2 代码（`.sysml` 文件） | 40 实测仓 + 30+ 待核仓 = 70+；3255+ .sysml 文件 | [11](11-real-world-corpora.md) |

## 5 关键时间节点

| 年份 | 事件 |
|---|---|
| 2017 | OMG SysML v2 RFP 发布；提交团队（SST）成立 |
| 2018–2022 | KerML 元模型与 SysML v2 抽象语法、文本表示与图形表示设计 |
| 2023 | Beta1 公开评审；商用厂商（PTC Windchill Modeler 10 等）开始首阶段集成[^vendor-ptc] |
| 2024-09 | Beta2；MODELS 2024 出现首批 v2 形式化论文[^molnar-2024] |
| 2024-10 | Almeida 等 ER 2024 发布 KerML 4D 时空语义批评[^almeida-2024] |
| 2024–2025 | LLM × SysML v2 论文涌现（SysTemp、SysMBench、Internetware 实证）[^bouamra-2025][^jin-2025][^wang-2025] |
| 2025-06-30 | OMG **Final Adoption**[^omg-final] |
| 2025-07-21 | OMG 公开发布公告 |
| 2025-09 | 进入 FTF 整理期 |
| 2026-02 | daltskin/sysml-v2-grammar 仓库初始化（v2025-12 tag），首版 ANTLR4 文法发布；Siemens Capital 2512 集成 SysML v2[^vendor-siemens-cap]；北航 *Uncertainty Modeling for SysML v2* arXiv 上线[^zhang-2026] |
| 2026-03 | OMG 4 份规范以 `formal/2026-03-0x` 正式出版；daltskin v2026.03.0 release |
| 2026-04 | Sensmetry 把 SysIDE 闭源升级到商业 Syside Editor[^syside-rebirth]；daltskin v2026.03.2 + PR #6 多 SDK target；空客 Apollo 11 v2 模型 latest commit |
| 2025-05-30 | **国家标准 GB/T 45803-2025**《系统与软件工程 基于模型的系统工程 统一架构建模语言》发布；起草单位含**北京航空航天大学**（鲁金直为核心起草人）+ 北京理工大学 + 中国电子技术标准化研究院 + 商飞 + 兵器 + 航天等 16 家[^gb-45803-overview] |
| 2025-09-14 | **杭州华望 M-Design v2 alpha 发布**——国内唯一公开商用化的 SysML v2 平台 |
| 2025-10 | 国内首部 SysML v2 中文专著《精华透视：SysML v2》（刘玉生 等，科学出版社） |
| 2025-12-01 | GB/T 45803-2025 正式实施 |
| 2026-05 | Eclipse SysON `v2026.3.0` 发布[^repo-syson]；HAMR + santoslab 真案例 4 平台 CI 工作流稳定[^repo-santoslab]；本仓库基准日期 |

## 6 五维度速判（详见各章）

| 维度 | 一句话定位 | 量化指标（2026-05） | 详见 |
|---|---|---|---|
| **解析 / IDE** | 9 套独立 parser、3 个 tree-sitter 语法并存；VS Code 较丰富但 IntelliJ 完全空白；Sensmetry 新版闭源是最大隐忧 | daltskin 文法对真 v2 corpus 通过率 **96.9%**（339/350）；对 Pilot 标准库 100%；对真实世界 40 仓 3255 文件 **94.7%** | [04](04-parsing-ide-infrastructure.md) / [10](10-daltskin-deep-audit.md) / [11](11-real-world-corpora.md) |
| **形式化 / 验证** | 端到端开源唯有 HAMR + GUMBO + Logika（限 v2 ∩ AADL 子集）；Imandra 闭源；Coq/Isabelle deep embedding 完全空白 | 11 路径中**1 路 Production-Beta**（HAMR）+ **1 路 RPTU SysMD 维护型 PoC** + **9 路论文/烂尾/闭源** | [05](05-formal-verification.md) |
| **可视化 / 协作** | SysON 是唯一接近生产级图形建模器，但 BDD/IBD/Sequence/Use Case 仍不全；API 标准本身没有 merge 端点 | SysON `v2026.3.0` 实现 6 类视图 / 共需 9 类；OMG API & Services 完全无 merge | [06](06-visualization-collaboration.md) |
| **代码生成 / 执行** | 多数转换（Modelica/Simulink）是 Vapor；OWL 转换 Beta；HAMR codegen 是唯一端到端真案例；行为执行几乎无开源 | 12 转换路径中 **4 PoC + 6 Vapor + 1 Beta（OWL）+ 1 Production（HAMR/AADL 子集）** | [07](07-codegen-execution.md) |
| **包管理 / CI** | sysand（Rust + pubgrub + KPAR）是整套生态最现代的设计选择；GitHub Action 没有 reusable 包，Linter 完全空白 | sysand 29★ 活跃；apollo/aadl-release/drkiettran/sysmini/sysmloc/webmodeler 共 6 个仓的根目录已含 `.project.json`（sysand 标志） | [04 §6](04-parsing-ide-infrastructure.md) / [07 §3](07-codegen-execution.md) |

## 7 中国语境

中文圈对 SysML v2 / MBSE 的参与已经形成**学术 + 国家标准 + 商用 + 社区**四位一体格局，详见 [docs/03-beihang-investigation.md](03-beihang-investigation.md)。摘要：

1. **北航**（5 学院横向矩阵）：岳涛 / 吴际（OMG SysML v2 + PSUM 标准化）、葛宁 / 胡春明（LLM × SysML 实证）、**鲁金直**（航空学院，KARMA 语言发明者 + 国家标准核心起草）、康锐（可靠性 MBSE 联盟）、刘继红（机械工程 MBSE 教学专著）。
2. **北大**：金芝团队 SysMBench 是首个公开的 NL → 系统模型基准[^jin-2025]。
3. **南航**（杨志斌 + 黄志球）：中文 SysML 自动生成 RNL2SysML 与岳涛长期合作。
4. **北理工**（王国新 + 阎艳 + Shouxuan Wu）：与北航鲁金直组合"KARMA 兵工方阵"。
5. **浙大 + 杭州华望**（刘玉生）：国内**唯一**商用 SysML v2 平台 M-Design v2（2025-09 alpha）。
6. **大连理工 Ruizhe-Yang**：开源贡献最丰，[SysMLine][^repo-sysmline] / [SysMini][^repo-sysmini] / [SysMLOC][^repo-sysmloc] / [CODES][^repo-codes] 系列。
7. **国家标准 GB/T 45803-2025**：2025-05 发布、2025-12 实施，**自研 KARMA 路径**与 OMG SysML v2 / KerML 平行；中国 MBSE 工具厂商面临"双轨合规"格局。
8. **个人贡献者 + 中文社区**：LnYo-Cly、cdfeih、hs1520、ypj0202 等 GitHub 个人贡献；UMLChina / 复杂装备 MBSE 联盟 / 杭州华望 MBSE 三大公众号 / CSDN / 知乎专栏；模型巴巴 modelbaba.com 中文门户。

国内**商用 MBSE 厂商**：杭州华望首推 M-Design v2；索为系统、安世亚太、山大华天截至 2026-05 仍以 SysML v1 为主。

## 8 阅读路径建议

按角色给出推荐阅读路径（**粗体**为必读章节）：

- **管理者 / 选型决策者**：**00 + 01 + 08 + 09**——理解语言现状、横向对标、机会缺口。耗时 ~30 分钟。
- **架构师 / 工具链负责人**：**00 + 04 + 10 + 11** + 选读 06/07——理解 ANTLR4 文法接入、daltskin 工程现状、真实语料覆盖度。耗时 ~1 小时。
- **形式化工程师 / 学术研究者**：**00 + 02 + 05** + 选读 01/09——文献索引 + 形式化路径评估。耗时 ~1 小时。
- **AI / LLM 工程师**：**00 + 02（LLM 维度）+ 06（MCP）+ 11（语料推荐）+ 09 §A**——LLM × SysML 论文 + 数据集 + 工具链空白。耗时 ~45 分钟。
- **中文学术圈合作 / 北航相关方**：**00 + 03 + 02**——把握北航实情 + 完整文献。耗时 ~30 分钟。
- **立项做 lint / IDE 工具**：**04 + 09 §A.1 + 11 §6.4**——明确语法边界 + 失败模式 → lint 规则。耗时 ~45 分钟。
- **立项做 KerML 形式化研究**：**01 + 05 + 02 §1 语义** + 09 §C.1——从语言结构到证明工程。耗时 ~1.5 小时。

## 9 维护策略

本仓库为快照式调研报告。规范层每年由 OMG 推出 RTF（Revision Task Force）小修订；开源生态变化更快，建议每 6–12 个月重检一次。Issue / PR 欢迎，但**不接受未提供新一手证据**（论文 URL、commit hash、规范页码、第三方实测）的修订请求。维护与协作约束见 [AGENTS.md](../AGENTS.md)。

## 参考文献

[^omg-final]: OMG. *Final Adoption: SysML v2.0, KerML v1.0, Systems Modeling API & Services v1.0*. 2025-07-21. <https://www.omg.org/news/releases/pr2025/07-21-25.htm>

[^seidewitz-2017]: Seidewitz, E. SysML v2 RFP 提交背景，OMG 历史归档。完整背景见 [omg.org/sysml/sysmlv2](https://www.omg.org/sysml/sysmlv2/)。

[^vendor-ptc]: PTC. *Windchill Modeler 10*. <https://www.ptc.com/en/blogs/alm/introducing-windchill-modeler-10-whats-new-and-noteworthy>

[^molnar-2024]: Molnár, V., & Graics, B. *Towards the Formal Verification of SysML v2 Models*. ACM/IEEE MODELS 2024. <https://dl.acm.org/doi/10.1145/3652620.3687820>

[^almeida-2024]: Almeida, J.P.A. 等. *An Analysis of the Semantic Foundation of KerML and SysML v2*. ER 2024. <https://link.springer.com/chapter/10.1007/978-3-031-75872-0_8>

[^bouamra-2025]: Bouamra, Y. 等. *SysTemp: A Multi-Agent System for Template-Based Generation of SysML v2*. arXiv:2506.21608. <https://arxiv.org/abs/2506.21608>

[^jin-2025]: Jin, D., Jin, Z. 等. *SysMBench: A System Model Generation Benchmark from Natural Language Requirements*. arXiv:2508.03215. <https://arxiv.org/abs/2508.03215>

[^wang-2025]: Wang, Y., Ge, N. 等. *Generating SysML Behavior Models via LLMs: An Empirical Study*. Internetware 2025. <https://dl.acm.org/doi/10.1145/3755881.3755926>

[^vendor-siemens-cap]: Siemens. *Capital 2512 Release Notes*. <https://blogs.sw.siemens.com/ee-systems/2026/02/27/whats-new-in-capital-2512/>

[^zhang-2026]: Zhang, M., Li, Y., & Yue, T. *Uncertainty Modeling for SysML v2*. arXiv:2602.21641. <https://arxiv.org/abs/2602.21641>

[^syside-rebirth]: Sensmetry. *Syside Editor Rebirth*. <https://sensmetry.com/syside-editor-rebirth-sysml-v2-0-50x-speed-up-license-change-free-as-before/>

[^repo-syson]: *eclipse-syson/syson*. <https://github.com/eclipse-syson/syson>

[^repo-santoslab]: *santoslab/sysmlv2-models*. <https://github.com/santoslab/sysmlv2-models>

[^repo-sysmline]: *Ruizhe-Yang/SysMLine*（大连理工 PoC）。<https://github.com/Ruizhe-Yang/SysMLine>

[^repo-sysmini]: *Ruizhe-Yang/SysMini*（大连理工，372 .sysml）。<https://github.com/Ruizhe-Yang/SysMini>

[^repo-sysmloc]: *Ruizhe-Yang/SysMLOC*（大连理工，322 .sysml）。<https://github.com/Ruizhe-Yang/SysMLOC>

[^repo-codes]: *Ruizhe-Yang/CODES*（大连理工 CubeSat 任务模型）。<https://github.com/Ruizhe-Yang/CODES>

[^gb-45803-overview]: GB/T 45803-2025《系统与软件工程 基于模型的系统工程 统一架构建模语言》。国家标准馆. <https://www.ndls.org.cn/standard/detail/28c9f842f8a22c6a6fee666390d8b1c0>
