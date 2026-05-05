# SysML v2 生态调研（中文）

> 一份面向资深工程师的、针对 OMG **SysML v2 / KerML** 标准、学术研究与开源基础设施的中文综述。覆盖从规范状态、学术地图、北航专项调研，到解析 / IDE、形式化验证、可视化与协作、代码生成与执行五个基础设施维度的深度评估，并以 UML、SysML v1、AADL、Modelica、Capella、BPMN、TLA+、Alloy 八个建模语言生态作为成熟度对照。

**调研基准日期**：2026-05-05。所有 GitHub star 数、commit 时间、市场安装量均为该日实测；标准状态以 OMG 2025-07 Final Adoption 公告[^omg-final]为准。

---

## 太长不看

1. **OMG 已于 2025-06-30 完成 Final Adoption**，2025-09 出版 SysML v2.0、KerML v1.0 与 Systems Modeling API & Services v1.0[^omg-final]；规范层稳定，**实现仍以 "Pilot" 命名**——本身就是对成熟度的诚实自承。
2. 五维度基础设施**全面落后于 UML / SysML v1 / AADL / Modelica / Capella / BPMN**：图形 diff/merge 接近 0、Coq/Isabelle 形式化 0、IDE 插件丰度 < UML 1/10、code generator 个位数；与 TLA+ / Alloy 的「文本 + CLI 验证」范式相比也落后**至少一代验证基础设施**。
3. **真正胜过前辈的四点**：文本一等公民、KerML 分层架构、REST/HTTP API 入标准、KPAR 包格式 + sysand cargo 范式包管理[^repo-sysand]。这四点构成 v2 web/CI/LLM 集成的代际飞跃，未来 3–5 年生态会快速补齐。
4. **最有价值的开源四件套**（覆盖 ~80% 用例）= [Eclipse SysON][^repo-syson] 图形建模 + [Open-MBEE Flexo MMS][^repo-flexo] 图存储/REST + [daltskin LSP][^repo-daltskin-lsp] / [elan8 spec42][^repo-spec42] 编辑器 + [sensmetry sysand][^repo-sysand] 包管理。
5. **生态最大隐忧**：Sensmetry 把 SysIDE 闭源升级到商业 Syside Editor[^syside-rebirth]（VS Code 4254 安装、性能 50× legacy）；Gamma 的 SysML v2 verification 前端代码没释放[^molnar-2024]；Living SysML Blueprint[^teodorov-2025] 与 Imandra[^imandra] 都是论文 / 闭源。
6. **北航专项 + 中文 MBSE 生态**（已扩展为全国全景）：北航 SysML/MBSE 力量横跨 5 个学院（计算机 / 软件 / 航空 / 可靠性 / 机械）。除已知岳涛[^yue-tao] + 吴际[^omg-psum] + 葛宁 + 胡春明 四人外，第二轮深挖发现**鲁金直**（航空学院，KARMA 语言发明者，国家标准 GB/T 45803-2025 核心起草人）+ 康锐（中国 MBSE 联盟可靠性专委会主任）+ 刘继红（北航 MBSE 教学专著主编）。**国家标准 GB/T 45803-2025**（2025-05 发布、2025-12 实施）选择**自研 KARMA 路径**与 OMG SysML v2 平行——这是中国 MBSE 工具厂商接下来面临的"双轨合规"格局。**杭州华望 M-Design v2**（2025-09-14 alpha）是国内**唯一**公开商用化的 SysML v2 平台；2025-10 出版国内首部 SysML v2 中文专著《精华透视：SysML v2》（科学出版社）。详见 [docs/03-beihang-investigation.md](docs/03-beihang-investigation.md)。

---

## 五维度成熟度矩阵

> 评分 1=学术原型 / 3=工业可用 / 5=行业标杆。

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

判据来源详见 [docs/08-baseline-comparison.md](docs/08-baseline-comparison.md)。

---

## SysML v2 开源 Top 10

| # | 仓库 | 一句话 | 信号 |
|---|---|---|---|
| 1 | SysML-v2-Release[^repo-release] | OMG 官方发行 / 规范聚合 | 824★ · 2026-04 · LGPL-3.0 |
| 2 | eclipse-syson/syson[^repo-syson] | Obeo 牵头的 Web 图形建模器，基于 Sirius Web | 278★ · 2026-05-04 · EPL-2.0 · Java |
| 3 | SysML-v2-Pilot-Implementation[^repo-pilot] | Xtext 参考实现 + Jupyter kernel + PlantUML | 221★ · 2026-05-04 · LGPL-3.0 · Java/Xtext |
| 4 | SysML-v2-API-Services[^repo-api] | Play + PG 参考 REST 服务器 | 84★ · 2025-06 · LGPL-3.0 · Java |
| 5 | GfSE/SysML-v2-Models[^repo-gfse] | 德国系统工程协会维护的语料库 | 65★ · Python |
| 6 | sensmetry/sysml-2ls[^repo-sysml-2ls] | SysIDE 旧版 LSP（**已 archive**），新版闭源 | 52★ · 2025-10 · TS |
| 7 | airbus/apollo-11-sysml-v2[^repo-airbus] | 空客发布 Apollo 11 v2 完整参考模型 | 50★ · 2026-04 · Python |
| 8 | tukcps/SysMD[^repo-sysmd] | RPTU Notebook 工具 + AADD 求解器 | 38★ · 2026-04 · Apache-2.0 · Kotlin |
| 9 | MontiCore/sysmlv2[^repo-monticore] | RWTH MontiCore 独立 Java 实现 | 33★ · 2026-05 · Java |
| 10 | sensmetry/sysand[^repo-sysand] | KerML/SysML v2 包管理器（pubgrub + KPAR） | 29★ · 2026-05 · Rust |

完整列表与技术深度见 [docs/04-parsing-ide-infrastructure.md](docs/04-parsing-ide-infrastructure.md) 与 [docs/06-visualization-collaboration.md](docs/06-visualization-collaboration.md)。

---

## 商用厂商支持速览

| 厂商 / 产品 | v2 支持状态 |
|---|---|
| Dassault — Cameo / CATIA No Magic 2026x[^vendor-cameo] | 已发布，号称 100% 规范覆盖；2026-01 起涨价 ~20% |
| IBM — Rhapsody Systems Engineering[^vendor-ibm] | 云原生、Web、v2-first |
| Siemens — System Modeler for SysML v2[^vendor-siemens-sm] / Capital 2512[^vendor-siemens-cap] | 2026 H1 推出，与 IBM 合作；Capital 集成 |
| PTC — Windchill Modeler 10[^vendor-ptc] | "首阶段" 支持（2023-08 起） |
| Ansys — SCADE One 2026 R1 / SAM[^vendor-ansys] | v2 import + Python customization |
| Visual Paradigm — SysML v2 Studio[^vendor-vp] | 内嵌 LLM 助手 |
| Sensmetry — Syside Editor[^syside-rebirth] | **闭源商业**；前身 sysml-2ls 已 archive |

---

## 北航专项一表

| 主张 | 证据 | 信心 |
|---|---|---|
| 北航有人在 OMG 直接参与 SysML v2 标准化 | 岳涛主页明示 *Contributor to the System Modeling Language (SysML) V.2 standardisation at OMG*；2023 年从 Simula 全职加盟北航计算机学院[^yue-tao][^buaa-scse-yue] | **高** |
| 北航有人在 OMG PSUM 工作组 | 吴际在 OMG PSUM Wiki 上署名 PSUM Application/Domain Requirements Package leader[^omg-psum] | **高** |
| 北航全员主导的纯 SysML v2 论文 | *Uncertainty Modeling for SysML v2*，三作者邮箱全部 `@buaa.edu.cn`[^zhang-2026] | **高** |
| 北航本土 LLM × SysML 团队 | 葛宁 / 胡春明团队 Internetware 2025 论文[^wang-2025] | **高** |
| 北航可靠性与系统工程学院涉足 v2 | 未找到任何相关论文 | **否定** |
| 北航主导的 SysML v2 国军标 / 行业标准 | 未找到公开证据 | **否定** |

注：金芝（[北大教授][^jin-pku]）、周伯生、黄罡、谢冰均**为北大系**，请勿混入北航条目；王怀民在国防科大。

---

## 文档索引

| # | 文档 | 概述 |
|---|---|---|
| 00 | [总览](docs/00-overview.md) | 整体生态全景、关键时间节点、调研方法 |
| 01 | [标准状态](docs/01-standard-status.md) | OMG Final Adoption、KerML 分层、规范结构、API & Services |
| 02 | [学术地图](docs/02-academic-landscape.md) | 13 篇核心论文、9 个主要研究团队、工业案例 |
| 03 | [北航专项 + 中文 MBSE 生态](docs/03-beihang-investigation.md) | 北航 5 学院横向矩阵（计算机/软件/航空/可靠性/机械）+ 鲁金直 KARMA + 国标 GB/T 45803-2025 + 华望 M-Design v2（国内首个 v2 商用平台）+ 17 篇 CNKI 中文文献 + 微信公众号生态 |
| 04 | [解析 / IDE 基础设施](docs/04-parsing-ide-infrastructure.md) | 6 套独立 parser、Tree-sitter 三家、编辑器扩展、KPAR/sysand |
| 05 | [形式化与验证](docs/05-formal-verification.md) | 11 路径深度评估、HAMR/SysMD/verified-mbse/Gamma 等 |
| 06 | [可视化与协作](docs/06-visualization-collaboration.md) | SysON 架构、API 端点矩阵、Flexo MMS、OSLC、MCP、diff/merge |
| 07 | [代码生成与执行](docs/07-codegen-execution.md) | AADL/Modelica/OWL/HAMR 转换、行为执行、CI/CD、linter |
| 08 | [基线对照](docs/08-baseline-comparison.md) | UML/v1/AADL/Modelica/Capella/BPMN/TLA+/Alloy 八语言成熟度 |
| 09 | [缺口与机会](docs/09-gaps-opportunities.md) | 短中长机会窗口、按维度归类的开源空白 |
| 10 | [daltskin 深度审计](docs/10-daltskin-deep-audit.md) | daltskin/sysml-v2-grammar 全仓审计——核心结论：基础设施属性只集中在 2 个 `.g4` 文件 + 56 处 ambiguity patch，其余皆为可替换工程胶水。**推荐路径 (C) 只搬 `.g4` + 工具链全自建** |
| 11 | [真实世界 v2 代码语料调研](docs/11-real-world-corpora.md) | 40 个公开 GitHub 仓 × 3255 个 .sysml 文件实测；daltskin 通过率 94.7% (3083/3255)；工具链分布 + 失败模式 + 旗舰语料推荐；含 30+ 待核仓清单 |
| 12 | [ModelCopilot / WSE Laboratory 深度档案](docs/12-modelcopilot-deep.md) | 北航 WSE-Lab + ModelCopilot 全景：8 仓库逐项深读、arXiv 2602.21641 PSUM 论文解剖（含 7 案例 + stereotype 完整集）、平台技术规格、公众号现状、与 OMG/华望/Cameo/Loughborough 战略对位、6 月/1 年/3 年前瞻轨迹 |
| ★ | [参考文献](docs/references.md) | 全部标准、论文、仓库、商用产品、教程，含 cite key |

---

## 适用与不适用

**适合**：评估 SysML v2 是否值得引入项目 / 选型工具链 / 立项基础设施 / 立项学术工作 / 在中国语境下做选型分析。

**不适合**：作为 SysML v2 入门教程（请阅读 Sensmetry *Advent of SysML v2* 25 课系列[^sensmetry-advent]、OOSE Learning Club[^oose] 或 Tim Weilkiens 的 *SysML v2 Book*[^repo-mbse4u]）；作为商用工具操作手册（请查 Cameo/Rhapsody/Capital 厂商文档）。

---

## 引用本仓库

```
HansBug. SysML v2 生态调研（中文）. GitHub, 2026.
https://github.com/HansBug/sysmlv2-survey-zh
```

## 许可

文档内容采用 **CC-BY-4.0**。引用与转载请保留出处。本仓库非商业、非 OMG 官方。

## 维护与更新

本仓库为快照式调研报告。规范层每年由 OMG 推出 RTF（Revision Task Force）小修订；开源生态变化更快，建议每 6–12 个月重检一次。Issue / PR 欢迎，但**不接受未提供新一手证据**（论文 URL、commit hash、规范页码、第三方实测）的修订请求。维护与协作约束见 [AGENTS.md](AGENTS.md)。

---

## 参考文献

[^omg-final]: OMG. *Final Adoption: SysML v2.0, KerML v1.0, Systems Modeling API & Services v1.0*. 2025-07-21. <https://www.omg.org/news/releases/pr2025/07-21-25.htm>。完整书目见 [docs/references.md](docs/references.md#omg-sysmlv2-final-2025)。

[^repo-pilot]: *Systems-Modeling/SysML-v2-Pilot-Implementation*. <https://github.com/Systems-Modeling/SysML-v2-Pilot-Implementation>。

[^repo-release]: *Systems-Modeling/SysML-v2-Release*. <https://github.com/Systems-Modeling/SysML-v2-Release>。

[^repo-api]: *Systems-Modeling/SysML-v2-API-Services*. <https://github.com/Systems-Modeling/SysML-v2-API-Services>。

[^repo-syson]: *eclipse-syson/syson*. <https://github.com/eclipse-syson/syson>。

[^repo-sysml-2ls]: *sensmetry/sysml-2ls*（已 archive）。<https://github.com/sensmetry/sysml-2ls>。

[^repo-airbus]: *airbus/apollo-11-sysml-v2*. <https://github.com/airbus/apollo-11-sysml-v2>。

[^repo-sysmd]: *tukcps/SysMD*. <https://github.com/tukcps/SysMD>。

[^repo-monticore]: *MontiCore/sysmlv2*. <https://github.com/MontiCore/sysmlv2>。

[^repo-sysand]: *sensmetry/sysand*. <https://github.com/sensmetry/sysand>。

[^repo-gfse]: *GfSE/SysML-v2-Models*. <https://github.com/GfSE/SysML-v2-Models>。

[^repo-flexo]: *Open-MBEE/flexo-mms-sysmlv2*. <https://github.com/Open-MBEE/flexo-mms-sysmlv2>。

[^repo-daltskin-lsp]: *daltskin/sysml-v2-lsp*. <https://github.com/daltskin/sysml-v2-lsp>。

[^repo-spec42]: *elan8/spec42*. <https://github.com/elan8/spec42>。

[^repo-mbse4u]: *MBSE4U/SysMLv2JupyterBook*. <https://github.com/MBSE4U/SysMLv2JupyterBook>。

[^syside-rebirth]: Sensmetry. *Syside Editor Rebirth: SysML v2.0, 50× speed-up, license change*. <https://sensmetry.com/syside-editor-rebirth-sysml-v2-0-50x-speed-up-license-change-free-as-before/>。

[^molnar-2024]: Molnár, V., & Graics, B. *Towards the Formal Verification of SysML v2 Models*. ACM/IEEE MODELS 2024. <https://dl.acm.org/doi/10.1145/3652620.3687820>。

[^teodorov-2025]: Teodorov, C. 等. *A Research Agenda for the Living SysML V2 Blueprint*. SBMF 2025. <https://link.springer.com/chapter/10.1007/978-3-032-12086-1_4>。

[^imandra]: Imandra. *SysML v2 Solution*（闭源）。<https://www.imandra.ai/sysml>。

[^yue-tao]: Yue, Tao. *Personal Homepage*. <https://yue-tao.github.io/>。

[^buaa-scse-yue]: 北京航空航天大学计算机学院. *岳涛教授信息页*. <https://scse.buaa.edu.cn/info/1387/10998.htm>。

[^omg-psum]: OMG PSUM Working Group Wiki. <https://www.omgwiki.org/uncertainty/doku.php?id=start>。

[^zhang-2026]: Zhang, M., Li, Y., & Yue, T. *Uncertainty Modeling for SysML v2*. arXiv:2602.21641. <https://arxiv.org/abs/2602.21641>。

[^wang-2025]: Wang, Y., Ge, N., Liu, J. 等. *Generating SysML Behavior Models via Large Language Models: An Empirical Study*. Internetware 2025. <https://dl.acm.org/doi/10.1145/3755881.3755926>。

[^jin-pku]: Jin, Zhi. *PKU Faculty Page*（用于勘误：北大而非北航）。<https://faculty.pku.edu.cn/zhijin/>。

[^vendor-cameo]: Dassault. *Cameo / CATIA No Magic 2026x SysMLv2 Plugin*. <https://docs.nomagic.com/spaces/CATIA/pages/261619716/CATIA+SysML+v2+Solution>。

[^vendor-ibm]: IBM. *Rhapsody Systems Engineering*. <https://www.ibm.com/products/rhapsody-systems-engineering>。

[^vendor-siemens-sm]: Siemens. *System Modeler for SysML v2 (合作 IBM)*. <https://news.siemens.com/en-us/siemens-system-modeler-for-sysml/>。

[^vendor-siemens-cap]: Siemens. *Capital 2512 Release Notes*. <https://blogs.sw.siemens.com/ee-systems/2026/02/27/whats-new-in-capital-2512/>。

[^vendor-ptc]: PTC. *Windchill Modeler 10 — Introducing SysML v2 First Phase*. <https://www.ptc.com/en/blogs/alm/introducing-windchill-modeler-10-whats-new-and-noteworthy>。

[^vendor-ansys]: Ansys. *SAM 2026 R1 — Advancing MBSE*. <https://www.ansys.com/blog/advancing-mbse-ansys-sam-2026-r1>。

[^vendor-vp]: Visual Paradigm. *SysML v2 Studio*. <https://updates.visual-paradigm.com/releases/sysml-v2-studio-competitive-advantages-launch/>。

[^sensmetry-advent]: Sensmetry. *Advent of SysML v2 — 25 Lesson Series*. <https://sensmetry.com/advent-of-sysml-v2/>。

[^oose]: OOSE. *SysML v2 Learning Club*. <https://clubs.oose.com/courses/sysmlv2/>。
