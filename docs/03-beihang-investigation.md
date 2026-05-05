# 03 北航专项调研

## 本章简介

本章系统检验「北京航空航天大学（Beihang / BUAA）是否参与了 SysML v2 / KerML 标准化或研究」这一假设。结论：**确认存在直接、实锤的关联**——北航在 SysML v2 标准化与本土 LLM × SysML 研究两个方向上都有真实存在感，但具体落点与原始猜测几乎完全错位。本章给出实锤证据、邻近信号、否定证据、易混淆点勘误，并最后给出对国内合作或学术立项的建议。

## 1 调研动机与方法

请求方原始假设：北航在 SysML v2 上有相关学术或开源工作，但落点不明。本调研的输入约束包括一组候选名字（多为北航 / 北大 / 国防科大资深学者），但**这些候选并未经过预先核实**。本调研的任务是：

1. 核查每位候选的实际所属单位（很多 CN 学者跨机构、跨年度变更）；
2. 用中英双语 + 邮箱后缀 + OMG 工作组成员名单 + arXiv / ACM DL 全文检索交叉验证；
3. 区分三种关联强度：(a) 北航全员主导的纯 v2 工作；(b) 北航人参与的 v2 / OMG 标准化贡献；(c) 北航人涉及的更广义 MBSE / SysML v1 / 可靠性工作。

检索关键词清单包括：

- 中英双语：`"SysML v2" 北航`、`"SysML 2.0" 北航`、`"KerML" Beihang`、`"系统建模语言" 北航`、`"SysML" 北京航空航天大学`、`site:buaa.edu.cn SysML`、`"Beihang" "SysML v2"` (Google Scholar)、`北航 INCOSE`、`北航 MBSE SysML`。
- GitHub：`api.github.com/search/users` with email `@buaa.edu.cn` 在 SysML 相关仓库的贡献。
- OMG：标准化 SST 与 PSUM 工作组的成员名单。

## 2 实锤证据

### 2.1 岳涛（Tao Yue）—— 北航与 OMG 的中枢

岳涛教授现职**北京航空航天大学计算机学院全职教授**（2023 年从挪威 Simula Research Lab 加盟，邮箱 `yuetao@buaa.edu.cn`）[^buaa-scse-yue]。其个人主页[^yue-tao]明确列出三项 OMG 角色：

- *Contributor to the System Modeling Language (SysML) V.2 standardisation at OMG*
- *Initiator and Co-chair of the Precise Semantics for Uncertainty Modeling (PSUM) standard at OMG*
- *Contributor to UTP V.2 at OMG*

这是已检索到的**北航与 OMG SysML v2 / KerML 标准化**的唯一直接接口，信心**高**。

### 2.2 吴际（Ji Wu）—— 北航 OMG PSUM 工作组成员

吴际副教授现职北航计算机学院。OMG PSUM 工作组官方 wiki[^omg-psum]在 *Application/Domain Requirements Package* leadership team 列表中明确署名 *Ji Wu, Beihang University, China*。这是北航在 OMG 标准化层面**第二位**有官方 footprint 的研究者。信心**高**（OMG 官方页面署名）。

### 2.3 *Uncertainty Modeling for SysML v2*（2026-02 arXiv）

Zhang, M., Li, Y., & Yue, T. *Uncertainty Modeling for SysML v2*[^zhang-2026] 是检索到的**唯一一篇北航全员主导的纯 SysML v2 论文**：

- 三作者邮箱：`{manzhang, liyunyang, yuetao}@buaa.edu.cn`，已直接在 arXiv PDF 首页核对。
- 内容：把 OMG 新发布的 PSUM（Precise Semantics for Uncertainty Modeling）规范系统性地嵌入 SysML v2，提出 PSUM-SysMLv2 扩展，做了 7 个案例研究。
- 与 §2.1、§2.2 完全一致：岳涛主导 + 把 PSUM 工作落地到 SysML v2。

信心**高**。

### 2.4 北航软件学院 LLM × SysML 研究

Wang, Y., Ge, N., Liu, J., Cao, Z., Chen, Z., & Hu, C. *Generating SysML Behavior Models via Large Language Models: An Empirical Study*. **Internetware 2025**[^wang-2025]。从 Internetware 2025 大会页面核实，**全部六位作者**均署名 *School of Software, Beihang University*。论文构建 107 个 SysML 行为模型数据集（活动图、状态机、序列图，PlantUML 格式），用 17 个 LLM 评测生成质量与幻觉。论文标题用 SysML 而非严格 v2，但议题、数据集、模型检查规则与 v2 生态紧密相关。

第二作者葛宁（Ning Ge）的 Google Scholar Profile[^ge-scholar]显示研究方向为「形式化方法 + 模型驱动软件工程」，是值得继续跟踪的本土 SysML / 形式化方向研究者。信心**高**。

### 2.5 历史背景：SysML v1 时代的北航 footprint

2014 年 ACM TOSEM 论文 *Traceability and SysML Design Slices to Support Safety Inspection*[^yue-traceability-2014]把 Yue, T. 列为 Beihang 合著者，这与 §2.1 一致——岳涛在 Simula 期间已有与北航的合作发表。属历史 SysML v1 工作，记录于此以完整呈现轨迹。

## 3 候选人核查结果（含勘误）

请求方原始候选人列表与实际所属对照如下。**请勿混淆机构**——本调研发现至少四位候选人被错误归入北航。

| 候选人 | 实际所在 | 与 SysML v2 关系 | 勘误 |
|---|---|---|---|
| **金芝**（Zhi Jin） | **北京大学**[^jin-pku] | SysMBench 论文署名北大 + 华中 + 华东师大 + 北京控制工程研究所，**无北航**[^jin-2025] | **不是北航** |
| **周伯生** | 北大系 | 未检索到 SysML v2 工作 | **不是北航** |
| **黄罡** | 北大系 | 未检索到 SysML v2 工作 | **不是北航** |
| **谢冰** | 北大系 | 未检索到 SysML v2 工作 | **不是北航** |
| **王怀民** | 国防科大（NUDT） | 未检索到 SysML v2 工作 | **不是北航** |
| **胡春明** | 北航软件学院 | 是 §2.4 Internetware 2025 论文合著者；主业是分布式系统 | 北航，但非 SysML 主导 |
| **李未、张莉、孙海龙、马世龙、刘超、王青** | 多为北航 | 未检索到 SysML v2 相关产出 | 北航，但与 v2 无直接关系 |
| **康锐、姜同敏、黄宁、王自力**（可靠性学院） | 北航可靠性与系统工程学院 | 未检索到任何与 SysML v2 / KerML 直接相关的论文 | 北航，但与 v2 无直接关系 |

## 4 邻近信号

- **MBSE 文献计量**：北航在更宽口径的 MBSE 文献计量中**排名世界第二**，仅次于 RWTH Aachen，共 68 篇 MBSE 论文[^mbse-bibliometric]。绝大多数是 SysML v1 / 可靠性 / 航空领域应用，并非 v2 本体研究——但显示北航在 MBSE 整体范围内有深厚积累，未来转 v2 的潜力较大。
- **国内 SysML v2 中文解读**：知乎 / CSDN 出现一些署名「北航毕业生」的入门博文。这些不是学术成果，但反映 v2 在北航学生群体中的传播。
- **CNKI 学位论文**：受限于本次调研的网络访问条件，未对 CNKI 进行账号检索。建议有学校 IP 的研究者用 *机构 = 北京航空航天大学 + 主题词 = SysML* 复检 2024–2026 学位论文。

## 5 否定证据

**已搜过但没找到**的项目，列在此以避免读者重复同一检索：

- 北航**可靠性与系统工程学院**（康锐 / 姜同敏 / 王自力 / 黄宁等）发表 SysML v2 论文：未找到。该院主要做可靠性建模与 PHM。
- 北航**自动化学院**或**国家空管新航行系统重点实验室**发表 SysML v2：未找到。
- 北航参与 OMG SysML v2 SST（Submission Team）的官方组织名单：OMG 仅列 80+ 组织，未能完整核对北航是否在内（待查）。
- 北航主导的 SysML v2 国军标 / 行业标准：未找到公开证据。
- GitHub 上 `buaa.edu.cn` 邮箱贡献官方 SysML v2 仓库：`api.github.com/search/users` 返回 0 条。

## 6 信心总评

| 主张 | 信心 |
|---|---|
| 北航有人做 SysML v2 直接研究 | **高**（实锤一篇 arXiv 全员北航） |
| 岳涛是北航 SysML v2 / OMG 接口人 | **高** |
| 吴际是 OMG PSUM 工作组北航代表 | **高**（OMG 官方 wiki 列名） |
| 北航软件学院 葛宁 / 胡春明团队做 SysML × LLM | **高**（Internetware 2025 已发表） |
| 可靠性学院涉足 SysML v2 | **未发现** |
| 北航主导 v2 国军标 / 行业标准 | **未发现** |
| 北航在 OMG SST 名单 | **待查** |

## 7 给国内合作 / 立项建议

如果接下来要在北航上下文里做 SysML v2 相关工作，三条具体可行路径：

1. **联系岳涛 / 吴际**：他们是北航与 OMG 的现成接口，对项目立项、对接标准、合作投稿都有杠杆。岳涛的完整论文清单见 Google Scholar `zTDRGDcAAAAJ`。
2. **跟葛宁 / 胡春明组合作**：本土 LLM × SysML 方向有真实积累，且 SysMBench 这种基准刚发布、表现差，正是发力做工具 / 方法的窗口。
3. **CNKI 复检**：用 *机构 = 北京航空航天大学 + 主题词 = SysML* 检索 2024–2026 学位论文（需要校园网或 VPN）。

国内整体生态：详见 [00-overview.md §6 中国语境](00-overview.md#6-中国语境) 与 [02-学术地图 §2 主要研究团队](02-academic-landscape.md#2-主要研究团队)。

## 参考文献

[^yue-tao]: Yue, Tao. *Personal Homepage*. <https://yue-tao.github.io/>

[^buaa-scse-yue]: 北京航空航天大学计算机学院. *岳涛教授信息页*. <https://scse.buaa.edu.cn/info/1387/10998.htm>

[^omg-psum]: OMG PSUM Working Group Wiki. <https://www.omgwiki.org/uncertainty/doku.php?id=start>

[^zhang-2026]: Zhang, M., Li, Y., & Yue, T. *Uncertainty Modeling for SysML v2*. arXiv:2602.21641. <https://arxiv.org/abs/2602.21641>

[^wang-2025]: Wang, Y., Ge, N., Liu, J., Cao, Z., Chen, Z., & Hu, C. *Generating SysML Behavior Models via Large Language Models: An Empirical Study*. Internetware 2025. <https://dl.acm.org/doi/10.1145/3755881.3755926>

[^ge-scholar]: Ge, Ning. *Google Scholar Profile*. <https://scholar.google.com/citations?user=66slC9EAAAAJ>

[^yue-traceability-2014]: Yue, T. 等. *Traceability and SysML Design Slices to Support Safety Inspection*. ACM TOSEM, 2014. <https://research.buaa.edu.cn/en/publications/traceability-and-sysml-design-slices-to-support-safety-inspection/>

[^jin-pku]: Jin, Zhi. *PKU Faculty Page*. <https://faculty.pku.edu.cn/zhijin/>

[^jin-2025]: Jin, D., Jin, Z., Li, L., Fang, Z., Li, J., & Chen, X. *SysMBench*. arXiv:2508.03215. <https://arxiv.org/abs/2508.03215>

[^mbse-bibliometric]: *MBSE 文献计量综述（2026）*。ScienceDirect. <https://www.sciencedirect.com/science/article/pii/S2950550X26000014>
