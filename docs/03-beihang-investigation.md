# 03 北航专项调研 + 中文 MBSE 生态全景

## 本章简介

本章原以"北航是否参与了 SysML v2 标准化与研究"为切入点，经反复深挖后扩展为**中文 SysML v2 / MBSE 生态全景调研**。读者读完应能回答：

- 北航在哪些学院、哪些人、哪些方向上参与 SysML v2 / MBSE？除已知 4 人（岳涛、吴际、葛宁、胡春明）外还有谁？
- 北航之外的国内主要研究力量分布如何（南航、北理工、浙大、中科院、大连理工等）？
- **GB/T 45803-2025 国家标准**对中国 MBSE 生态意味着什么？为何关键？
- 中文学术文献、微信公众号 / 知乎 / CSDN 等中文社区生态长什么样？
- 国内商用 MBSE 厂商（杭州华望、索为系统、安世亚太等）目前到了什么程度？谁是国内**唯一**已上线的 SysML v2 商业平台？

> **本章核心新发现**：
> 1. **鲁金直**（北航航空学院副教授、2023 入职）是中国 MBSE 国家标准 [GB/T 45803-2025][^gb-45803] 的核心起草人之一，也是 KARMA 语言发明者 + 中国 MBSE 联盟对外合作主任委员——这位在原版调研中被遗漏的关键人物，把"北航 SysML/MBSE 力量"从 4 人扩到 5 人，且是**国家级**层面而非论文层面。
> 2. **国家标准 GB/T 45803-2025**（2025-05 发布、2025-12 实施）选择了**自研 KARMA 路径**作为中国 MBSE 国家标准底座，与 OMG SysML v2 / KerML **平行而非简单跟随**——这是中国 MBSE 工具厂商接下来要应对的"双轨合规"格局。
> 3. **杭州华望 M-Design v2**（2025-09-14 首发 alpha）是**国内唯一**公开商业化的 SysML v2 平台，背后是浙大刘玉生团队孵化；同期出版的[《精华透视：SysML v2》][^huawang-book]（科学出版社 ISBN 9787030838780）是**国内首部** SysML v2 中文专著。
> 4. **北航 SysML/MBSE 力量横跨 5 个学院**：计算机学院（吴际、岳涛、张莉、胡春明）+ 软件学院（葛宁、任磊、陶飞）+ 航空学院（鲁金直）+ 可靠性与系统工程学院（康锐、王自力）+ 机械工程学院（刘继红）。这种横向矩阵让北航成为中国 MBSE 联盟里**人数最多、覆盖最全**的高校节点。

## 1 调研动机与方法

请求方原始假设：北航在 SysML v2 上有相关学术或开源工作，但落点不明。本调研经历两轮：

- **第一轮（2026-05 早期）**：以候选人列表 + 邮箱后缀（`@buaa.edu.cn`）+ OMG 工作组成员名单交叉核验，确认 4 个北航 SysML v2 直接关联人。
- **第二轮（2026-05 末）**：扩展到全中国 SysML v2 / MBSE 生态——CNKI 中文期刊检索 + 微信公众号检索（site:mp.weixin.qq.com）+ 中文博客检索（CSDN/知乎专栏）+ 国家标准库（NDLS / SAMR）+ 商用厂商主页 + 国内开源 GitHub 贡献者。

## 2 北航 SysML v2 / MBSE 团队全景（5 学院横向矩阵）

### 2.1 计算机学院 — SysML v2 标准化与不确定性建模主线

| 姓名 | 职务 | 与 SysML v2 / MBSE 关系 | 邮箱 |
|---|---|---|---|
| **岳涛 Tao Yue** | 全职教授（2023-，前 Simula） | **OMG SysML v2 提交团队 contributor**；**OMG PSUM 标准联合主席**；研究兴趣覆盖软件测试、量子软件、数字孪生、不确定性建模、MBSE、搜索式软件工程；Google Scholar `zTDRGDcAAAAJ` | yuetao@buaa.edu.cn |
| **吴际 Ji Wu** | 软件工程研究所**副所长**、副教授、博导 | OMG PSUM Application/Domain Requirements Package leader[^omg-psum]；安全关键软件建模/验证/测试；与航空、航天、船舶院所合作 | wuji@buaa.edu.cn |
| **胡春明 Chunming Hu** | 副教授 | 主攻分布式系统/虚拟化/大数据；作为合作者出现在 Internetware 2025 / FASE 2026 SysML/形式化论文 | hucm@buaa.edu.cn |
| **张莉 Zhang Li** | 软件学院副院长 + 计算机学院软件工程研究所所长 | 软件工程国家级一流专业建设点负责人；研究方向"软件工程"；**未在公开渠道找到 v2 直接产出**（TODO：待核实） | lily@buaa.edu.cn |

岳涛主页[^yue-tao]明确列出三项 OMG 角色：

- *Contributor to the System Modeling Language (SysML) V.2 standardisation at OMG*
- *Initiator and Co-chair of the Precise Semantics for Uncertainty Modeling (PSUM) standard at OMG*
- *Contributor to UTP V.2 at OMG*

吴际办公室在 G918 楼。岳涛与吴际同属计算机学院软件工程研究所，是北航 OMG 标准化的双核。

### 2.2 软件学院 — LLM × SysML 实证主力

葛宁（**先进工业软件研究所所长**）是北航软件学院 SysML/形式化方向的核心：

| 姓名 | 角色 | 主线 |
|---|---|---|
| **葛宁 Ning Ge** | 教授、博导，先进工业软件研究所所长；CCF 形式化方法与软件工程委员；前法国图卢兹国立理工博士 + IRT-Saint Exupéry 研究员 + 日本 NII 访问学者 | 形式化方法、安全可信软件、智能化软件工程；Internetware 2025 + FASE 2026 SysML 论文核心作者 |
| **王远 Yuan Wang** | 葛宁团队 | Internetware 2025 + FASE 2026 共同作者 |
| **任磊 Ren Lei** | 教授 | 工业软件、智能制造方向 |
| **陶飞 Fei Tao** | 教授 | 数字孪生、智能制造、工业软件；Nature Computational Science 2024 |
| **史晓华、潘海侠、殷永峰、郑征、石琳、王自力** 等 | 教授 | 复杂工业软件 + 模型驱动 + 可靠性大圈，潜在合作者 |

### 2.3 航空学院 — KARMA 语言 + 国家标准核心人物（**关键新发现**）

**鲁金直 Jinzhi Lu** 是这次第二轮调研最重要的发现。

- 单位：北航航空学院副教授、博导，校企/校地合作办公室主任。
- 履历：武汉理工 → 华中科大 → 瑞典 KTH 博士 (2014-2019) → 洛桑联邦理工 EPFL ICT4SM 博士后 → 2023.9 入职北航。
- Google Scholar：`6Vmt95UAAAAJ`，引用 1500+。
- 主页：[shi.buaa.edu.cn/lujinzhi][^lu-buaa]。

**社会任职密度极高**：

- 中国系统工程学会**应用及咨询工作委员会副主任委员**
- **基于模型系统工程联盟（中国 MBSE 联盟）对外合作委员会主任委员**[^chinambse]
- IEEE SMC MBSE 技术委员会 (TC) 成员
- IoF (Industrial Ontology Foundry) 系统工程分委会主任委员
- **中文 MBSEWiki 联合创始人**

**标志性贡献**：发明 [KARMA 语言][^lu-dsm-2021]（GOPPRR-E 元模型方法的文本实现），并被纳入 [GB/T 45803-2025][^gb-45803] 国家标准（详见 §6）。

代表论文：

| 年份 | 论文 | 备注 |
|---|---|---|
| 2019 | *Cognitive Twins for Supporting Decision-Makings of Internet of Things Systems* (Springer) | 101 引用 |
| 2019 | *Ontology Supporting Model-Based Systems Engineering Based on a GOPPRR Approach* (WorldCist) | 41 引用 |
| 2021 | *Integration of Modeling and Verification for System Model Based on KARMA Language*[^lu-dsm-2021] (DSM @ SPLASH) | KARMA 工程化基石 |
| 2022 | *The Emergence of Cognitive Digital Twin* (Zheng / Lu / Kiritsis) | 448 引用 |
| 2022 | *Systematic Literature Review of MBSE Tool-Chains* (Ma / Wang / Lu et al.) | 109 引用 |
| 2023 | *An Ontology-Based Engineering System to Support Aircraft Manufacturing System Design* | 113 引用 |
| 2024 | *KARMA Approach Supporting Development Process Reconstruction in MBSE* (CSD&M) | — |
| 2025 | *Application of Multi-Architecture Modelling Method in Intelligent Electric-Vehicle Design* (Int. J. Production Research) | — |
| 2025 | *Semantic Model-Based Systems Engineering Based on KARMA: A Research and Practice Roadmap* (INCOSE IS 2025) | — |
| 2025 | *Towards Intelligent MBSE: Constructing an MBSE Model Dataset for Generative AI* (Springer) | — |
| 2025 | *Cognitive Digital Thread Tool-Chain for Model Versioning in MBSE* (SSRN，与 Shouxuan Wu / Guoxin Wang / Yan Yan 合作) | — |

### 2.4 可靠性与系统工程学院 — MBSE × 可靠性

| 姓名 | 角色 | 与 MBSE 关系 |
|---|---|---|
| **康锐 Kang Rui** | 工程系统工程系教授、博导，国家重大基础研究项目首席科学家（2010） | **中国 MBSE 联盟可靠性系统工程专委会主任委员**；中国指挥控制学会可靠性系统科学与工程专委会主任委员；创立**确信可靠性 (Belief Reliability)** 理论；将系统思维 + 系统工程方法论与可靠性结合，是中国可靠性 + MBSE 交叉的代表[^kang-buaa] |
| **王自力 Wang Zili** | 教授 | 可靠性系统工程、PHM；列于软件学院 |
| **黄宁、姜同敏** 等 | 教授 | 可靠性建模、MBSE 工程方向（**未在公开渠道找到 v2 直接产出**） |

### 2.5 机械工程及自动化学院 — MBSE 工程教材

**刘继红 Liu Jihong** 教授、博导，工业与制造系统工程方向，计算机辅助设计与图形学学报副主编，国家科技部"十二五"制造业信息化总体专家组成员。

标志性产出：2025-01 出版《**基于 MBSE 的复杂装备系统设计：理论与实践**》（电子工业出版社，ISBN 9787121488344）[^liu-mbse-book]，配套自主"**蕴象软件**"。北航主导的 MBSE 工程化教学专著。

### 2.6 校际合作

北航航空学院 + 中国空间技术研究院（航天五院）合作发表《**基于模型的载人航天器研制方法研究与实践**》[^zhang-2020-aircraft]——张柏楠（通讯）、**戚发轫（中国工程院院士）**、邢涛、刘洋、王为，航空学报 2020 41(7) 023967。

## 3 北航团队 SysML / MBSE 论文清单（2018–2026）

### 3.1 葛宁 + 胡春明 + 王远（北航软件学院）

- **[wang-2025-internetware]** Wang Y., Liu J., Cao Z., Chen Z., **Ge N.**, **Hu C.** (2025). *Generating SysML Behavior Models via Large Language Models: An Empirical Study*. Internetware 2025[^wang-2025-internetware]。107 SysML 行为模型数据集 + 17 LLM 评测幻觉与生成质量；语义 F1 在序列图最低 50%。
- **[jiang-2026-fase]** Jiang Y., Yan Z., **Ge N.**, Wang Y., Weng J., **Hu C.** (2026). *LusGen: Leveraging LLMs for Safety-Critical Lustre Design and Requirements Traceability*. FASE 2026 pp. 64–85[^jiang-2026-fase]。形式化语言 + LLM 追踪 + 安全关键设计；北航与 ETAPS 体系内的形式化方法旗舰会。

### 3.2 岳涛（北航计算机学院）

按 [岳涛主页][^yue-tao]整理（节选与 SysML / MBSE / 不确定性最强相关）：

- **[zhang-2026-uncertainty]** Zhang M., Li Y., **Yue T.** *Uncertainty Modeling for SysML v2*. arXiv:2602.21641（2026）[^zhang-2026-uncertainty]。**唯一一篇北航全员主导的纯 SysML v2 论文**——三作者邮箱均为 `@buaa.edu.cn`。把 OMG PSUM 元模型注入 SysML v2/KerML，提出 PSUM-SysMLv2 stereotype 库；7 个案例研究验证。
- *LLMs for Model-driven Engineering: A Survey* (2026)。
- *Evolve the Model Universe of a System Universe*. ASE 2023。
- *Simplexity Testbed: A Model-Based Digital Twin Testbed*. Computers in Industry 2023。
- *Uncertainty-wise Requirements Prioritization with Search*. TOSEM 2021。
- *Uncertainty-aware Robustness Assessment of Industrial Elevator Systems*. TOSEM 2023。
- *Pretrain, Prompt, and Transfer: Evolving Digital Twins for Time-to-Event Analysis in CPS*. TSE 2024。
- *EvoCLINICAL*. ESEC/FSE 2023（与挪威癌症登记系统的产业落地）。
- *Quantum Software Engineering: Roadmap and Challenges Ahead*（2025）。

岳涛公开论文 80+ 篇，主线在 ESEC/FSE / ICSE / TSE / TOSEM 弹性测试、数字孪生、量子软件。SysML v2 是 2024 加入北航后的新分支。

### 3.3 鲁金直 + 北理工合作（中国 MBSE 联盟主轴）

见 §2.3 鲁金直代表论文清单。该合作链上的关键合作者：

- 北理工机械工程学院 / 工业与智能系统工程研究所：**王国新 Guoxin Wang**（教授，KARMA 共同发起人）、**阎艳 Yan Yan**（教授）、**Shouxuan Wu** 吴绶玄（博士生，主页 [wushouxuan.github.io][^shouxuan]）、**马君达**、**Jiawei Li**。
- 这构成"北航 + 北理工 + EPFL/KTH"国际化 KARMA 阵营，是中国 GB/T 45803 国家标准的核心起草力量。

### 3.4 早期历史性工作（v1 时代）

- **[yue-2014-tosem]** Yue T. *et al.* (2014). *Traceability and SysML Design Slices to Support Safety Inspections*. ACM TOSEM[^yue-2014-tosem]。BUAA + Simula 合作，岳涛在 Simula 期间已有与北航的合作发表。

## 4 候选人核查（含勘误，更新版）

请求方原始候选人列表与实际所属对照（沿用第一轮结论 + 第二轮新增确认）：

| 候选人 | 实际所在 | 与 SysML v2 关系 | 勘误 |
|---|---|---|---|
| **金芝** Zhi Jin | **北京大学**[^jin-pku] | 团队 SysMBench 论文署名北大 + 华中 + 华东师大 + 北京控制工程研究所，**无北航**[^jin-2025-sysmbench] | **不是北航** |
| **周伯生 / 黄罡 / 谢冰** | 北大系 | 未检索到 SysML v2 工作 | **不是北航** |
| **王怀民** | 国防科大（NUDT） | 未检索到 SysML v2 工作 | **不是北航** |
| **胡春明** | 北航软件学院 | Internetware 2025 + FASE 2026 合著者；主业分布式系统 | 北航，但非 SysML 主导 |
| **李未 / 张莉 / 孙海龙 / 马世龙 / 刘超 / 王青** | 多为北航 | 未检索到 SysML v2 相关产出 | 北航，但与 v2 无直接关系 |
| **康锐 / 姜同敏 / 黄宁 / 王自力** | 北航可靠性与系统工程学院 | 康锐是中国 MBSE 联盟可靠性专委会主任委员，但**未检索到 v2 直接论文**；其他三位也未发现 v2 直接产出 | 北航，但 MBSE 间接相关 |

第二轮新增确认：**鲁金直**（航空学院）、**刘继红**（机械工程及自动化学院）虽然不在第一轮候选人列表，但是中国 MBSE 联盟与国家标准的核心人物，必须独立列入。

## 5 北航之外的中国 SysML v2 / MBSE 力量

### 5.1 南京航空航天大学（NUAA）— 中文 SysML 论文产量第一

南航高安全系统的软件开发与验证技术工信部重点实验室是国内 SysML 中文论文最活跃的群体：

- **杨志斌 Zhibin Yang**：通讯作者；与黄志球、岳涛深度合作。主页 [faculty.nuaa.edu.cn/yangzhibin][^yang-nuaa]。
- **黄志球 Zhiqiu Huang**：南航计算机学院教授，AADL/SysML/形式化方法。
- **鲍阳、杨永强、谢健、周勇** 等。

代表论文 **[bao-2021-rnl2sysml]**：鲍阳、杨志斌、杨永强、谢健、周勇、岳涛、黄志球、郭鹏（航空工业计算所）*基于限定中文自然语言需求的 SysML 模型自动生成方法 (RNL2SysML)*. **计算机研究与发展** 2021, 706–730[^bao-2021-rnl2sysml]。资助：NSFC 62072233 + 航空科学基金 201919052002。这是**中文学界 SysML 自动生成方向最被引用的论文**，且与岳涛长期合作——形成"南航 NUAA + 北航 BUAA + 中航工业"的中文 SysML 自动化联盟。

### 5.2 北京理工大学 — KARMA 兵工方阵

北理工机械工程学院 / 工业与智能系统工程研究所是中国 MBSE 联盟的另一极：

- 王国新（教授，KARMA 共同发起人）
- 阎艳（教授）
- Shouxuan Wu（博士生）
- 马君达、Jiawei Li 等

与北航鲁金直组合形成"北航 + 北理工 + EPFL/KTH"国际化 KARMA 阵营；GB/T 45803 国家标准核心起草力量。

### 5.3 浙江大学 — 华望系统科技母团队

- **刘玉生**：浙大 CAD&CG 国家重点实验室教授，浙大山东工业技术研究院复杂装备创新设计中心主任；**杭州华望系统科技董事长**（华望前期技术全部源自其团队）。承担 NSFC 4 项 + 863 子课题 3 项。主页 [person.zju.edu.cn/0003172][^liu-yusheng-zju]。
- 2025-11 浙大先研院与华望共建"AI+MBSE 与数智工程联合实验室"。
- 2025-10 主编《精华透视：SysML v2》专著（科学出版社）[^huawang-book]——**国内首部** SysML v2 中文系统化专著。

### 5.4 中科院体系

- **金鑫**（国科大）+ **贺宇峰**（中国科学院空间应用工程与技术中心）：*基于 SysML 的空间有效载荷系统故障诊断方法*. **空间科学学报** 2024, 44(6) 1120-1133[^jin-2024-spatial]。
- 同方向：*基于 SysML 的空间有效载荷测试路径自动生成方法*. 系统工程与电子技术 2024。

### 5.5 大连理工大学 — Ruizhe-Yang

详见 [11 §3.6 中文社区贡献](11-real-world-corpora.md#36-中文社区贡献小节) 与 [11 §3.2 教学/培训](11-real-world-corpora.md#32-教学--培训--大型样例集)。Ruizhe-Yang 团队的 GitHub 多产（[SysMLine][^repo-sysmline-3] / [SysMini][^repo-sysmini-3] / [SysMLOC][^repo-sysmloc-3] / [CODES][^repo-codes-3]），是国内唯一公开多仓库的 SysML v2 工具与语料团队。CubeSat 任务模型（CODES）尤其值得参考。

### 5.6 上海交大 / 西工大 / 北邮等

- **上海交大航空航天学院**（梅芊、黄丹、卢艺）：北航学报 2019 *基于 MBSE 的民用飞机功能架构设计方法*[^mei-2019-civilavia]。
- **西北工业大学**（王乾、郑党党、佟瑞庭、韩冰、杨小辉）+ 中航工业第一飞机设计研究院：系统工程与电子技术 2024 *基于 MBSE 的民机飞行控制系统架构设计*[^wang-2024-fcs]。
- 北邮 / 哈工大 / 华中等：分布有零散 MBSE 论文，但未发现专门 v2 主线。

## 6 国家标准 GB/T 45803-2025 — 战略级事件

> **这是中文 SysML v2 综述里目前几乎没人提到、却最具战略意义的事件**。

### 6.1 标准基本信息[^gb-45803]

- **标准号**：GB/T 45803-2025
- **标准名**：《系统与软件工程 基于模型的系统工程 统一架构建模语言》
- **计划号**：20230680-T-469；下达 2023-08-06
- **发布日**：2025-05-30
- **实施日**：2025-12-01
- **归口**：全国信息技术标准化技术委员会（TC28）软件与系统工程分会（TC28SC7）

### 6.2 起草单位（16 家）

按重要性排序：

1. 中国电子技术标准化研究院（电标院）——主导
2. **北京航空航天大学**——鲁金直为核心起草人
3. 北京理工大学——王国新、阎艳为核心起草人
4. 中国商用飞机有限责任公司北京民用飞机技术研究中心（商飞研究中心）
5. 中国兵器装备集团兵团装备研究所
6. 中国航天科技集团信息中心
7. ……（共 16 家，详见 [SAMR 标准库][^gb-45803-samr]）

### 6.3 起草人节选（共 50+ 人）

范科峰、**鲁金直（北航）**、李文鹏、苏伟、**王国新（北理工）**、张旸旸、马君达、汪澔、于冬梅、**阎艳（北理工）**等。

### 6.4 战略含义

- **国家选择了 KARMA 路径，而非简单跟随 OMG SysML v2 / KerML**——KARMA 是鲁金直发明的 GOPPRR-E 元建模文本语言，与 SysML v2 / KerML **平行而非派生**。
- **中国 MBSE 工具厂商接下来面临"双轨合规"压力**：
  1. **国际互操作面**：与 OMG SysML v2 / API & Services 标准对齐（导致华望 M-Design 推出 v2 版本）。
  2. **国家标准合规面**：与 GB/T 45803 KARMA 标准对齐（兵工 / 航天 / 商飞等国央企采购可能强制要求）。
- **学术影响**：北航 + 北理工的 KARMA 阵营在国家标准下游可能形成"标准 + 工具 + 教材"完整生态，与岳涛团队的"OMG 国际标准 + PSUM"路径形成北航内部双路线。

### 6.5 与 OMG SysML v2 的对比

| 维度 | OMG SysML v2 | GB/T 45803 (KARMA) |
|---|---|---|
| 标准发起 | OMG 国际组织 | 全国信标委 TC28SC7 |
| 元建模方法 | KerML 4D 时空语义[^almeida-2024-er] | GOPPRR-E（鲁金直）[^lu-dsm-2021] |
| 文本语法 | KEBNF（OMG 元语法） | KARMA 文本语言 |
| 工具生态 | Pilot Implementation + 商用 Cameo / Rhapsody / Capital | 中科蜂巢科技 KARMA 工具链 + 国内厂商配套 |
| 国际互操作 | 强（OMG 全球认证） | 弱（中国主体） |
| 国央企采购合规 | 间接（通过 Cameo 等） | 强（国标） |

**结论**：在中国语境内做 SysML v2 工具，要同时考虑两套标准生态。

## 7 中文学术文献清单（CNKI / 中文期刊，按年份倒序）

| 年份 | 标题 | 作者（首位 / 通讯） | 期刊 | 引用 |
|---|---|---|---|---|
| 2025-12 | 基于大语言模型的系统架构视图智能建模方法 | 潘如江、陈炯毅、王剑波、方哲梅 | 系统工程与电子技术 47(12) | [DOI 10.12305/...][^pan-2025-llm-arch] |
| 2025-12 | AI 赋能基于模型的系统工程研究现状与展望 | （TODO 待核实作者） | 系统工程与电子技术 47(12) | [DOI 10.12305/...](https://doi.org/10.12305/j.issn.1001-506X.2025.12.21) |
| 2025-09 | 第 46 卷 9 期航天器/宇航学报 MBSE 专题（多篇） | 中国航天科技集团等 | 宇航学报 2025 | spacejournal.cn |
| 2025-04 | 数字线程中需求与架构模型协同设计与一致性分析方法研究 | （TODO 待核实） | 空天防御 8(2) | spacejournal/ktfy |
| 2025 | 基于 MBSE 的新一代航空发动机总体—部件多学科协同模式 | （多人） | 航空动力学报 | [DOI 10.13224/...][^aircraft-engine-2025] |
| 2025 | MBSE 在载人航天在轨物资补给任务中的应用 | （TODO 待核实） | 系统工程与电子技术 47(5) 1551 | sys-ele.com |
| 2024-09 | 基于 MBSE 的民机飞行控制系统架构设计 | 王乾、郑党党、佟瑞庭、韩冰、杨小辉 | 系统工程与电子技术 46(9) 3050-3059 | [DOI 10.12305/...][^wang-2024-fcs] |
| 2024-10 | 基于 SysML 的空间有效载荷测试路径自动生成方法 | 中国空间应用中心 | 系统工程与电子技术 | [DOI 10.12305/...][^spatial-2024-test] |
| 2024-06 | **基于 SysML 的空间有效载荷系统故障诊断方法** | 金鑫、贺宇峰 | 空间科学学报 44(6) 1120-1133 | [DOI 10.11728/...][^jin-2024-spatial] |
| 2022-12 | 面向复杂产品研制的 MBSE 体系架构及其发展趋势研究 | （TODO） | 控制与决策 / 类似 | kzyjc 2022.12 |
| 2022-01 | 民用飞机高度控制系统 MBSE 建模方法 | （TODO） | 系统工程与电子技术 | [DOI 10.12305/...](https://doi.org/10.12305/j.issn.1001-506X.2022.01.21) |
| 2021-10 | 基于 MBSE 的民机系统功能建模方法 | （TODO） | 系统工程与电子技术 | [DOI 10.12305/...](https://doi.org/10.12305/j.issn.1001-506X.2021.10.23) |
| 2021-09 | 基于 MBSE 的航天器系统建模分析与设计研制方法探索 | （TODO） | 系统工程与电子技术 43(9) 2516-2525 | [DOI 10.12305/...](https://doi.org/10.12305/j.issn.1001-506X.2021.09.19) |
| 2021-04 | **基于限定中文自然语言需求的 SysML 模型自动生成方法 (RNL2SysML)** | 鲍阳、杨志斌（通讯）、岳涛、黄志球等 | **计算机研究与发展** 706-730 | [crad.ict.ac.cn][^bao-2021-rnl2sysml] |
| 2020-07 | **基于模型的载人航天器研制方法研究与实践** | 张柏楠（通讯）、戚发轫（院士）等 | 航空学报 41(7) 023967 | [DOI 10.7527/...][^zhang-2020-aircraft] |
| 2019-05 | 基于 MBSE 的民用飞机功能架构设计方法 | 梅芊、黄丹、卢艺 | 北航学报 45(5) 1042-1051 | [DOI 10.13700/...][^mei-2019-civilavia] |

> **重要观察**：直接以 "**SysML v2**" 命名的中文期刊学术论文，截至 2026-05 仅有岳涛团队 *Uncertainty Modeling for SysML v2*（arXiv，英文）、华望团队的中文专著《精华透视：SysML v2》，以及散落的"SysML 2.0/v2"普及/解读文章。**国内对 v2 的中文期刊学术化滞后于工程产业化**——专著与厂商博客先行，CNKI 学术圈尚在跟进。

## 8 微信公众号 / 知乎 / CSDN 中文社区生态

| 渠道 | 名称 / ID | 运营方 | 覆盖度 | 链接 |
|---|---|---|---|---|
| 微信公众号 | **UMLChina** (umlchina2) | 潘加宇（创始人，前清华教师）；2002 年成立 | UML / SysML / MBSE 培训类，长期更新；潘加宇还在知乎写《SysML v2 规范逐段解读》系列（2025 起） | [umlchina.com][^umlchina] |
| 微信公众号 | **复杂装备 MBSE 生态** | 复杂装备 MBSE 联盟（北航 + 北理工 + 商飞 + 上海宇航研究所） | 启航杯赛事、联盟动态、国标进展 | [mbse-alliance.com][^chinambse] |
| CSDN / 知乎机构号 | **杭州华望 MBSE** (`HZHW_MBSE`) | 杭州华望系统科技 | 截至 2026-04 共 77+ 篇原创，560 粉丝，访问量 8.8 万；SysML v2 / v1→v2 迁移、M-Design v2、AI+MBSE 系列 | [blog.csdn.net/HZHW_MBSE][^huawang-csdn]；[zhihu.com/org/...][^huawang-zhihu] |
| 中文门户 | **模型巴巴 modelbaba.com** | 独立 MBSE 中文知识平台（运营方未公开署名） | MBSE / SysML / DoDAF / UPDM / UAF / PLM / ALM / AADL 全谱涵盖；活跃发文 | [modelbaba.com/posts][^modelbaba] |
| 中文门户 | **建模者 / UMLChina 系列** uml.org.cn / sysml.org.cn / modeler.org.cn / 火龙果软件 | 同源系列（UMLChina 旗下） | SysML v2 建模元素解析、UAF 培训等 | [uml.org.cn][^uml-china-zh] |
| 微信公众号 | **MBSE 联盟** 官方号 | 中国 MBSE 联盟 | 联盟通知、启航杯、对外培训 | mbse-alliance.com |
| 微信公众号 | **科学出版社专业图书** (sciencepress-cspm) | 科学出版社 | 周边推广华望专著 | — |
| 知乎专栏 | 潘加宇《SysML v2 规范逐段解读》、华望团队《SysML V2 对 V1 的改进》《SysML V2 的元数据》《SysML V2 的模型组织》 | 潘加宇 + 华望 | 截至 2026-05 至少有 8 篇 v2 中文长文 | zhihu.com 多个 zhuanlan |

> **观察**：没找到独立、纯学术、专攻 SysML v2 的国内微信公众号；公众号生态以**厂商 + 联盟**驱动为主。学术圈传播主要靠知乎、CSDN、官方期刊。

## 9 中国开源贡献者（GitHub）

| 用户 | 仓库 | 单位 / 信息 | 活跃度 |
|---|---|---|---|
| **Ruizhe-Yang** | [SysMLine][^repo-sysmline-3] / [SysMini][^repo-sysmini-3] / [SysMLOC][^repo-sysmloc-3] / [CODES][^repo-codes-3]（CubeSat-Oriented Design & Engineering using SysML v2）/ Intellinker-capella / EchoCubeSat-Capella | 大连理工大学 | 18 个公开 repo，2023–2026 持续更新 |
| **cdfeih** | [emf-sysmlv2][^repo-emf-sysmlv2]（"学习 SysML v2，基于 EMF 框架的 v2 元素管理"）+ ot-java（OT 算法用于建模协同）+ yjs-java（YJS 协同方案调研） | 中文学习 / 调研账号；三个仓库都聚焦"协同 + SysML v2"，方向像华望或国内厂商内部研发；2023.3 注册 | 私下持续学习状态 |
| **hs1520** | [SysML-v2-AST-Parser][^repo-hs1520]（"基于官方 SysML v2 AST/API 的解析与转换 + Docker 一键部署，Python+Java 双侧"） | 中文 issue/README，疑似某 MBSE 原型项目；2023.7 注册 | 单仓 2026.4 更新 |
| **ypj0202** Ken Yeh | [SysML2PetriNet][^repo-petri-2]（"将 SysML v2 activity 模型转 Petri Nets 并导出 PNML"） | 名称背景偏港台/海外，但仓库 README 中文夹英文 — TODO: 待核实是否大陆 | 2025–2026 更新 |

未找到的：**索为系统、安世亚太、山大华天、杭州华望** 在 GitHub 上**没有公开的 SysML v2 工具仓库**。华望的 M-Design v2 闭源、商业销售。

## 10 中国 MBSE 联盟 + 启航杯赛事 + 学会

### 10.1 中国 MBSE 联盟（"基于模型系统工程联盟"）

- 网址：[mbse-alliance.com][^chinambse]、chinambse.com
- 关键人物：鲁金直（对外合作主任委员）、康锐（可靠性专委会）、王国新 / 阎艳（北理工）等。

### 10.2 启航杯 MBSE 建模大赛

- 2020 由上海航天技术研究院上海宇航系统工程研究所 + 中国商飞北京民用飞机技术研究中心联合发起。
- 已办 6 届（2025 是第六届），由"复杂装备 MBSE 联盟"领衔主办。
- 第六届承办方：中国商飞北京民用飞机技术研究中心 + 杭州市北京航空航天大学国际创新研究院。
- 分企业组、高校组两个赛道。

### 10.3 中国系统工程学会（CCOSE）

鲁金直为应用及咨询工作委员会副主任委员；与全国信标委软件与系统工程分技术委员会、IoF 系统工程组、复杂装备 MBSE 联盟联合形成"联合编委会"。

### 10.4 INCOSE 中国分会

公开搜索仅命中 INCOSE 国际侧的 SysML v2 活动；INCOSE 中国分会的独立 v2 活动信息不丰富 — TODO：待核实是否有专门论坛。

## 11 中国商用 MBSE 厂商动态

| 厂商 | 产品 | SysML v2 进展 | 核心证据 |
|---|---|---|---|
| **杭州华望系统科技** | M-Core / M-Design / M-Arch / M-Require / M-DT / M-ICD / M-InteG | **国内首个 SysML v2 平台 M-Design v2**（v0.0.0.1-alpha 2025-09-14 首发，60-180 天试用，支持文本+图形+功能计算+v1↔v2 迁移，覆盖 SysML v2 多视图）；2026-04-25 举办"第二届 AI+MBSE 与数智工程研讨会"；2025-12 出版国内首部 SysML v2 中文专著 | [mbse.com.cn][^huawang-home]；[blog.csdn.net/HZHW_MBSE][^huawang-csdn] 77+ 篇 |
| **索为系统（SOWAY）** | Modelook | **支持 SysML v1 全 9 图 131 元模型**，未公开 v2 路线图；S-MASP 平台 | [sysware.com.cn][^sysware] |
| **安世亚太（PERA Global）** | PERA SIM IRDP / **PERA SIM AutoMBSE** | "智能化 MBSE 模型闭环映射系统"；继续以 SysML v1 描述模型与仿真集成；未见 v2 特性 | [peraglobal.com][^peraglobal] |
| **山大华天 (CrownCAD)** | CrownCAD（CAD 主线） | 未见公开 SysML v2 工具 | [hoteamsoft.com][^hoteamsoft] |
| **广州智睿思维** | MBSES | 国产 MBSE 工具，模型巴巴有专文介绍，未公开 v2 状态 — TODO：待核实 | [modelbaba.com/mbse/2895.html][^zhirui] |
| **中科蜂巢科技 (Beijing Zhongke Honeycomb)** | KARMA / MetaGraph 工具链 | 鲁金直、Guoxin Wang 关联公司；与北航 / 北理工合作主要发表载体；对接国标 GB/T 45803 | linked from sciprofiles 1223475 |
| **北京安怀信** | 某 CAE 工具 | 与华望签订战略合作 | [anwiseglobal.com][^anwise] |

> **总判断**：华望 M-Design v2 是当前（2026-05）国内**唯一**公开宣称商业化、且支持 SysML v2 文本+图形混合标识的工具；其他厂商仍主要在 v1 + 中国自主 KARMA。

### 11.1 国内首部 SysML v2 中文专著

[《精华透视：SysML v2》][^huawang-book]——刘玉生 等（华望团队），科学出版社 2025-10，ISBN 9787030838780，定价 178 元，14 章，对标具有 v1 基础读者；**国内首部** SysML v2 系统化中文专著。

[《基于 MBSE 的复杂装备系统设计：理论与实践》][^liu-mbse-book]——刘继红、解士昆、陈建江、王佐旭（北航团队），电子工业出版社 2025-01，ISBN 9787121488344。

## 12 国家级项目 / 政策文件

- **NSFC**：已确认资助过 SysML / MBSE 相关项目编号包括 62072233（南航杨志斌团队）、61903305、62073267（民机 MBSE 团队）。NSFC 申报指南未将 SysML v2 单列为重点方向 — TODO：待核实是否有 2024/2025 SysML v2 专项条目。
- **航空科学基金**：201919052002 资助 RNL2SysML（南航 + 中航工业计算所）。
- **国家重点研发计划**："十四五"重点研发项目中没有直接以 "SysML v2" 命名，但已知存在**大型工业软件**（含 MBSE）的相关专项；西工大 2022 年已开设"基于模型的系统工程"课程作为大型工业软件课程体系。
- **MIIT 工信部**：工信部 + 国标委印发《国家智能制造标准体系建设指南（2024 版）》《国家人工智能产业综合标准化体系建设指南（2024 版）》——在体系框架内提及"基于模型的研发""模型驱动数字化"，但未单独点名 SysML v2。
- **GB/T 45803-2025**：见 §6（最关键的政策事件）。
- **国军标 (GJB)**：现有 GJB 集中在软件文档（438C-2021）、能力成熟度模型（9773）、安全分析（102A）等"传统软件工程"层；目前未发现专门 GJB 直接命名 SysML / MBSE / 模型驱动；但 MBSE 已经在载人月球探测、空间站、嫦娥五号等重大工程中"工程化"应用。

## 13 应用场景：国央企落地现状

| 单位 | 应用 | 工具栈 |
|---|---|---|
| **中国商飞 (COMAC)** | 长期与 IBM Harmony-SE 合作；启航杯主办方之一 | 目前仍以 SysML v1 为主 |
| **中国航天科技集团 / 五院 / 八院** | 载人航天器研制论文（2020）+ 航天器工程多篇 2025 论文 | MagicDraw、华望 M-Design 工程化应用 |
| **中航工业** | 第一飞机设计研究院使用 MBSE | 西工大合作论文证实 |
| **中船集团** | 船舶动力工程总体设计、船型与水动力性能 | 自研 V-Dats 平台 + SysML 语言 |
| **兵器集团** | 参与国标起草；华望博文显示"兵器重工"案例（滑翔炸弹设计、组件环境适应性建模） | 华望 M-Design |

## 14 信心总评（更新版）

| 主张 | 信心 |
|---|---|
| 北航有人在 OMG 直接参与 SysML v2 标准化 | **高** |
| 岳涛是北航 SysML v2 / OMG 接口人 | **高** |
| 吴际是 OMG PSUM 工作组北航代表 | **高** |
| 葛宁 / 胡春明组在北航软件学院做 SysML × LLM 实证 | **高** |
| **鲁金直是中国 MBSE 国家标准 GB/T 45803 起草核心 + KARMA 发明者**（**第二轮新发现**） | **高** |
| 康锐是中国 MBSE 联盟可靠性专委会主任委员（**第二轮新发现**） | **高** |
| 刘继红主编北航 MBSE 工程化教学专著（**第二轮新发现**） | **高** |
| **杭州华望 M-Design v2 是国内唯一公开的商用 SysML v2 平台**（**第二轮新发现**） | **高** |
| 国家标准 GB/T 45803-2025 是中国 MBSE 国家级背书（**第二轮新发现**） | **高** |
| 北航主导 v2 国军标 / 行业标准 | **GB/T 45803 已有北航主导贡献，是参与而非主导** |
| 北航在 OMG SST 名单 | **待查** |
| 是否有专门 v2 相关国军标 | **未发现** |

## 15 给国内合作 / 立项建议（更新版）

如果接下来要在中国语境下做 SysML v2 / MBSE 相关工作，五条路径：

1. **联系岳涛 / 吴际**：他们是北航与 OMG SysML v2 / PSUM 标准化的接口，对项目立项、对接国际标准、合作投稿都有杠杆。岳涛完整论文清单见 Google Scholar `zTDRGDcAAAAJ`。
2. **联系葛宁 / 胡春明（北航软件学院）**：本土 LLM × SysML 方向有真实积累（Internetware 2025 + FASE 2026），且 SysMBench 这种基准刚发布、表现差，正是发力做工具 / 方法的窗口。
3. **联系鲁金直（北航航空学院）+ 北理工王国新 / 阎艳团队**：如果要对接 GB/T 45803 国家标准、KARMA 工具链、中国 MBSE 联盟资源，他们是核心节点。
4. **联系刘玉生（浙大 / 华望）**：如果要研究国内首个商用 SysML v2 平台 M-Design v2 或参与"AI+MBSE 与数智工程联合实验室"。
5. **联系南航杨志斌 / 黄志球**：如果重点是中文 SysML 自动生成（RNL2SysML 方向），他们与岳涛长期合作。

CNKI 复检：用 *机构 = 北京航空航天大学 + 主题词 = SysML* 检索 2024–2026 学位论文（需要校园网或 VPN）。

## 16 后续待核实清单

| 缺口 | 说明 |
|---|---|
| 北航软件学院张莉、任磊、陶飞、史晓华、潘海侠等是否有 v2 直接论文 | 仅根据研究方向推断，未在公开渠道找到 v2 直接产出 |
| 北航 SRSE（可靠性与系统工程学院）官网响应失败 | 需后续直接访问校园网或学院别的入口确认 |
| INCOSE 中国分会的具体活动 | 公开搜索结果稀疏，建议直接联系 INCOSE Asia-Oceania 节区 |
| 模型巴巴 modelbaba.com 实际运营公司 | TODO：待核实是否归属于某商业公司 |
| "复杂装备 MBSE 生态" 公众号 vs. MBSE 联盟官号 实际名称对应 | 联盟自办公众号有但全名 TODO 待核实 |
| 是否存在 GJB 草案直接对接 SysML v2 / KARMA | 官方信息只到 GB/T 国标，军标层 TODO |
| NSFC 2024 / 2025 是否有以 SysML v2 / KerML 命名的指南条目 | 需要查 NSFC 项目指南原文 |
| ypj0202（SysML2PetriNet）是否大陆作者 | 仓库 README 双语，国别不明确 |
| 北航相关 PhD 学位论文（CNKI） | 需要学校 IP 复检 *机构 = 北航 + 主题词 = SysML* |

## 参考文献

[^yue-tao]: Yue, Tao. *Personal Homepage*. <https://yue-tao.github.io/>

[^omg-psum]: OMG PSUM Working Group Wiki. <https://www.omgwiki.org/uncertainty/doku.php?id=start>

[^lu-buaa]: 鲁金直 北航主页. <https://shi.buaa.edu.cn/lujinzhi/zh_CN/index.htm>

[^chinambse]: 中国 MBSE 联盟. <http://www.mbse-alliance.com/>

[^lu-dsm-2021]: Lu, J. 等. *Integration of Modeling and Verification for System Model Based on KARMA Language*. DSM @ SPLASH 2021. <https://dl.acm.org/doi/10.1145/3486603.3486775>

[^kang-buaa]: 康锐 北航主页. <https://shi.buaa.edu.cn/kangrui/zh_CN/index.htm>

[^liu-mbse-book]: 刘继红、解士昆、陈建江、王佐旭. 《基于 MBSE 的复杂装备系统设计：理论与实践》. 电子工业出版社 2025-01, ISBN 9787121488344.

[^zhang-2020-aircraft]: 张柏楠、戚发轫、邢涛、刘洋、王为. *基于模型的载人航天器研制方法研究与实践*. 航空学报 2020 41(7) 023967. DOI 10.7527/S1000-6893.2020.23967.

[^wang-2025-internetware]: Wang, Y., Liu, J., Cao, Z., Chen, Z., Ge, N., Hu, C. *Generating SysML Behavior Models via Large Language Models: An Empirical Study*. Internetware 2025. <https://dl.acm.org/doi/10.1145/3755881.3755926>

[^jiang-2026-fase]: Jiang, Y., Yan, Z., Ge, N., Wang, Y., Weng, J., Hu, C. *LusGen: Leveraging LLMs for Safety-Critical Lustre Design and Requirements Traceability*. FASE 2026. （ETAPS 2026 proceedings 待入 Springer LNCS）.

[^zhang-2026-uncertainty]: Zhang, M., Li, Y., Yue, T. *Uncertainty Modeling for SysML v2*. arXiv:2602.21641. <https://arxiv.org/abs/2602.21641>

[^yue-2014-tosem]: Yue, T. 等. *Traceability and SysML Design Slices to Support Safety Inspections*. ACM TOSEM 2014. <https://research.buaa.edu.cn/en/publications/traceability-and-sysml-design-slices-to-support-safety-inspection/>

[^jin-pku]: Jin, Zhi. *PKU Faculty Page*. <https://faculty.pku.edu.cn/zhijin/>

[^jin-2025-sysmbench]: Jin, D., Jin, Z. 等. *SysMBench*. arXiv:2508.03215. <https://arxiv.org/abs/2508.03215>

[^shouxuan]: Shouxuan Wu 主页. <https://wushouxuan.github.io/>

[^yang-nuaa]: 杨志斌 南航主页. <https://faculty.nuaa.edu.cn/yangzhibin/zh_CN/index.htm>

[^bao-2021-rnl2sysml]: 鲍阳、杨志斌（通讯）、岳涛、黄志球等. *基于限定中文自然语言需求的 SysML 模型自动生成方法 (RNL2SysML)*. 计算机研究与发展 2021 706-730. <https://crad.ict.ac.cn/fileJSJYJYFZ/journal/article/jsjyjyfz/HTML/2021-04-706.shtml>

[^liu-yusheng-zju]: 刘玉生 浙大主页. <https://person.zju.edu.cn/0003172>

[^huawang-book]: 刘玉生 等. 《精华透视：SysML v2》. 科学出版社 2025-10. ISBN 9787030838780.

[^huawang-home]: 杭州华望系统科技. <http://www.mbse.com.cn/>

[^huawang-csdn]: 杭州华望 MBSE CSDN 博客. <https://blog.csdn.net/HZHW_MBSE>

[^huawang-zhihu]: 杭州华望 MBSE 知乎机构号. <https://www.zhihu.com/org/hang-zhou-hua-wang-mbse>

[^jin-2024-spatial]: 金鑫、贺宇峰. *基于 SysML 的空间有效载荷系统故障诊断方法*. 空间科学学报 2024 44(6) 1120-1133. DOI 10.11728/cjss2024.06.2023-0109.

[^mei-2019-civilavia]: 梅芊、黄丹、卢艺. *基于 MBSE 的民用飞机功能架构设计方法*. 北航学报 2019 45(5) 1042-1051. DOI 10.13700/j.bh.1001-5965.2018.0494.

[^wang-2024-fcs]: 王乾、郑党党、佟瑞庭、韩冰、杨小辉. *基于 MBSE 的民机飞行控制系统架构设计*. 系统工程与电子技术 2024 46(9) 3050-3059. DOI 10.12305/j.issn.1001-506X.2024.09.17.

[^pan-2025-llm-arch]: 潘如江、陈炯毅、王剑波、方哲梅. *基于大语言模型的系统架构视图智能建模方法*. 系统工程与电子技术 2025 47(12). DOI 10.12305/j.issn.1001-506X.2025.12.17.

[^aircraft-engine-2025]: *基于 MBSE 的新一代航空发动机总体—部件多学科协同模式*. 航空动力学报 2025. DOI 10.13224/j.cnki.jasp.20240587.

[^spatial-2024-test]: *基于 SysML 的空间有效载荷测试路径自动生成方法*. 系统工程与电子技术 2024. DOI 10.12305/j.issn.1001-506X.2024.10.19.

[^gb-45803]: GB/T 45803-2025《系统与软件工程 基于模型的系统工程 统一架构建模语言》. 国家标准馆. <https://www.ndls.org.cn/standard/detail/28c9f842f8a22c6a6fee666390d8b1c0>

[^gb-45803-samr]: SAMR 国家标准库 GB/T 45803-2025 项目页. <https://std.samr.gov.cn/gb/search/gbDetailed?id=DF55C2967EADD24BE05397BE0A0A5C25>

[^almeida-2024-er]: Almeida, J.P.A. 等. *An Analysis of the Semantic Foundation of KerML and SysML v2*. ER 2024. <https://link.springer.com/chapter/10.1007/978-3-031-75872-0_8>

[^umlchina]: UMLChina. <http://www.umlchina.com/>

[^modelbaba]: 模型巴巴. <https://modelbaba.com/>

[^uml-china-zh]: UMLChina 中文门户系列 (uml.org.cn / sysml.org.cn / 火龙果软件). <http://www.uml.org.cn/>

[^repo-sysmline-3]: *Ruizhe-Yang/SysMLine*. <https://github.com/Ruizhe-Yang/SysMLine>

[^repo-sysmini-3]: *Ruizhe-Yang/SysMini*. <https://github.com/Ruizhe-Yang/SysMini>

[^repo-sysmloc-3]: *Ruizhe-Yang/SysMLOC*. <https://github.com/Ruizhe-Yang/SysMLOC>

[^repo-codes-3]: *Ruizhe-Yang/CODES*. <https://github.com/Ruizhe-Yang/CODES>

[^repo-emf-sysmlv2]: *cdfeih/emf-sysmlv2*. <https://github.com/cdfeih/emf-sysmlv2>

[^repo-hs1520]: *hs1520/SysML-v2-AST-Parser*. <https://github.com/hs1520/SysML-v2-AST-Parser>

[^repo-petri-2]: *ypj0202/SysML2PetriNet*. <https://github.com/ypj0202/SysML2PetriNet>

[^sysware]: 索为系统. <https://www.sysware.com.cn/>

[^peraglobal]: 安世亚太 PERA Global. <https://www.peraglobal.com/>

[^hoteamsoft]: 山大华天 CrownCAD. <https://www.hoteamsoft.com/>

[^zhirui]: 广州智睿思维 MBSES. <https://modelbaba.com/mbse/2895.html>

[^anwise]: 北京安怀信. <https://anwiseglobal.com/>
