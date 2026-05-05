# 06 可视化、协作与版本控制基础设施

## 本章简介

本章评估 SysML v2 的**图形建模、多人协作、API 服务、版本控制、模型 diff/merge、LLM 集成**基础设施现状。覆盖 Eclipse SysON 架构详解、OMG SysML v2 API & Services 端点矩阵与规范级缺陷（无 merge 端点）、Open MBEE Flexo MMS、OSLC 联邦、MCP 服务器三家、第三方工具与 SaaS 形态。

## 1 可视化

### 1.1 Eclipse SysON —— 唯一接近生产级的 web 图形建模器

[eclipse-syson/syson][^repo-syson]（EPL-2.0、278★、`v2026.3.0`、2026-05-04）。是当前唯一稳定可部署、可商用的开源 web 图形 v2 建模器。

#### 1.1.1 架构

非常清晰的现代 Web 栈：

| 层 | 技术 |
|---|---|
| 后端 | **Sirius Web 2026.3.x + Spring Boot 4.0.x + GraphQL（spring-boot-starter-graphql）+ Postgres 15** |
| 前端 | **React + MUI 7 + Vite 8 + TypeScript**（`frontend/syson` 子包） |
| 元模型 | 纯 EMF/Ecore（`backend/metamodel/syson-sysml-metamodel`，`SysMLv2.ecore` 由 KerML + SysMLv2 规范生成） |
| 运行时模型 | Postgres（Sirius Web 自带的 project / document / representation 表，**无 CDO**） |
| 全文检索 | Elasticsearch（可选） |

#### 1.1.2 已实现的图类

`backend/views/syson-standard-diagrams-view/.../*DiagramDescriptionProvider.java` 给出：

- **General View / Standard Diagram (SDV)** — 等价于 SysML v2 spec 中的"通用视图"
- **Action Flow View** — 行为 / Action 流（替代 Activity）
- **State Transition View** — 状态机
- **Interconnection View** — 等价于 IBD / 连接视图
- **Tree Explorer View**（`syson-tree-explorer-view`）
- **Requirements Table View**（`syson-table-requirements-view`）

**未实现**：Use Case View、Sequence View、完整 BDD 拆分图。在 v2 中已被合并进 General View 但 SysON 仍在持续重构（v2026.5 路线图明确提到 Stakeholder / Concern / Subject 工具仍在补全）。

#### 1.1.3 实时协作模型

**GraphQL Subscription**（WebSocket，Reactor 驱动），不是 CRDT、不是 OT。多用户同时编辑同一 representation 时，所有变更都过 Sirius Web 的 `IInput → IPayload`，由后端串行应用并广播；冲突解决靠后端事务（**乐观锁，最后写赢**），不是无冲突合并。

#### 1.1.4 OMG SysML v2 API 集成

**部分实现**。`backend/services/syson-sysml-rest-api-services` 暴露 `SysONObjectRestService` 与 `SysONProjectDataVersioningRestService`（Project / Branch / Commit / Element 查询），但官方文档[^syson-doc]明说「The SysMLv2 API isn't fully available yet」。SysON 自己用 GraphQL 做编辑，REST 仅为互操作开放只读 + 部分写。

#### 1.1.5 与 Capella 协同设计

通过 `doc/.../integration/capella.adoc` 文档化——走文件级交换 / 共享片段，不是共享 EMF resource；CEA / Obeo 路线图把 SysON 嵌入 Papyrus 作为 v2 编辑器。

#### 1.1.6 评价

| 优点 | 缺点 |
|---|---|
| 唯一稳定可部署、有完整 React 图编辑、可商用 | 必须接 Sirius Web 整套范式（无法轻松剥离 GraphQL 自建） |
| 元模型从规范生成，规范 vs. 实现一致性强 | 缺 BDD-vs-IBD 完整等价 |
| 实时协同、Postgres 持久化 | 并发模型偏弱（乐观锁，无三方合并） |
| Eclipse 基金会项目，长期维护可期 | 部分 v2 视图（Use Case、Sequence）未实现 |

### 1.2 Pilot Implementation 的 PlantUML visualizer

[Pilot][^repo-pilot]下 `org.omg.sysml.plantuml/`，由 31 个 `V*.java` Visitor 组成：`VStructure`（BDD/IBD）、`VAction`、`VStateMachine`、`VSequence`、`VCase`（Use Case）、`VRequirement`、`VBehavior`、`VTree`、`VComment`、`VMetadata`、`VPath`。覆盖度比 SysON 更广（含 Sequence、Use Case），但是**只读**渲染、PlantUML 文本输出、无交互编辑、布局靠 PlantUML 自动；定位是「Jupyter `%viz` magic 与 Eclipse 文本编辑器中的快速预览」，是 fallback，不是一线工具。

### 1.3 其他可视化栈

- **Mermaid**：[daltskin/sysml-v2-lsp][^repo-daltskin-lsp]（MIT、12★、2026-04-30）的 `server/src/mcp/` 提供 6 种图（auto-detect、focus、diff modes），是目前唯一第一方 Mermaid 输出；可直接嵌入 Markdown / GitHub README。
- **Graphviz/DOT、draw.io、WebGL/3D**：未发现公开成熟实现。[dandelivers/sysml-v2-explorer][^repo-explorer]（0★）为元模型 / 结构 / 需求 / 追溯交互浏览，量级很小。
- **Storybook 风格文档生成**：无原生工具；Sensmetry 的 *Syside Automator* 是闭源商业。

### 1.4 Notebook 渲染

- **Pilot 的 Jupyter kernel**（`org.omg.sysml.jupyter`）：`%viz` magic 走 PlantUML svc，IPython display 通过 ZMQ 将渲染后的 SVG/PNG 推回前端。
- **[tukcps/SysMD][^repo-sysmd]**（Apache-2.0、38★、2026-04-21）：notebook 风格但是独立桌面 / Spring Boot 应用（JDK 21 + JavaFX 安装包），文档 / 模型 cell 互引、内置 AADD 约束求解器，**Markdown 双向交换**——是目前最适合「可执行需求 + 一致性求解」的 OSS 工具，但**不支持 SysML v2 全集**（明确不支持 time slice / view / user keyword）。

## 2 OMG SysML v2 API & Services 真实端点矩阵

参考 [Systems-Modeling/SysML-v2-API-Services][^repo-api-services]的 `conf/routes`（Play / Scala，pilot）。完整端点矩阵：

| 资源 | 操作 |
|---|---|
| Project | `GET/POST/PUT/DELETE /projects[/:id]` |
| Branch  | `GET/POST/DELETE /projects/:id/branches[/:bid]` |
| Tag     | `GET/POST/DELETE /projects/:id/tags[/:tid]` |
| Commit  | `GET/POST /projects/:id/commits`, `GET /commits/:cid`, `GET /commits/:cid/changes[/:chid]` |
| Element | `GET /commits/:cid/elements[/:eid]`, `…/projectUsage`, `…/roots` |
| Relationship | `GET /elements/:eid/relationships?direction=in|out|both` |
| Query   | `GET/POST /queries[/:qid]`, `GET /queries/:qid/results`, `POST /query-results` |
| Meta    | `GET /meta/datatypes[/:id]` |
| Extension | `POST /x/named/...`, `GET /elements/:qualifiedName` |

### 2.1 版本控制语义

**Git-like 但偏弱**：commit 是不可变树根（含 `owningProject` / `previousCommit`），branch 指向 commit，tag 是只读 commit 别名；**`changes` 端点暴露 DataVersion 列表**（即 commit 内每个改动元素），所以 **diff = 比对两个 commit 的 DataVersion 集合**。

### 2.2 规范级空白：无 merge 端点

**没有原生 merge 端点**——pilot routes 里完全没有；并发由后端乐观锁（POST commit 必须带 previousCommit / Branch HEAD）。**分页**走 `page[after|size]` query 参数（HAL-style links，未在 routes 中显式但 controller 里有）；**streaming** 没有，全是 HTTP 同步 JSON-LD。

所以「Git-like」更接近 **Mercurial 的扁平 commit DAG，无三方合并算法**。Flexo MMS 的 PSM 想补 merge，但未落地（见 §3）；SysON 自家的 `ProjectDataVersioningRestService` 也只是只读暴露。

**Gap**：标准里压根没定义 merge / 三方合并 / conflict resolution——是规范级别的空白，而不是某个工具的缺失。所有商用与开源工具的 merge 实现都是私有不兼容的。

## 3 Open MBEE Flexo MMS

老的 MMS SDVC（`Open-MBEE/mms`，Java/Spring Boot/Postgres+ES+Minio，2025-11 仍维护）和 View Editor (VE) 是 SysML v1 + Cameo Teamwork Cloud 时代设计的，**未直接支持 v2**。Flexo MMS 才是 v2 的方向：

### 3.1 仓库矩阵

- [Open-MBEE/flexo-mms-sysmlv2][^repo-flexo]（11★、2026-05-05）— **Kotlin + Ktor 3 + Apache Jena + Fuseki/GraphDB triplestore**；是 OMG SysML v2 API 规范的 REST PSM 适配器，把 Project / Commit / Element / Branch / Tag / Query 翻译成 RDF / SPARQL，存到 Layer1 service。
- [Open-MBEE/flexo-mms-sysmlv2-mcp][^repo-flexo-mcp]（2★、2026-05-04）— FastMCP / streamable HTTP，转发 Bearer token，read-only 模式可控。
- [Open-MBEE/flexo_syside][^repo-flexo-syside]（4★、2026-04-10）— 与 Sensmetry SysIDE 桥接。

### 3.2 设计原则

「graph-native 存模型 + 标准协议互通（SPARQL / GSP / LDP / GraphQL / OSLC / SysMLv2 / JSON HyperSchema / gRPC）」。diff/merge **不是 CRDT**，是基于 RDF named graph 的 commit 树（类似 Quad-store + 三元组级别 diff）。

### 3.3 关键缺陷：DiffMerge 是 stub

**作为协同写平台 Flexo 还偏早期**：`DiffMergeApi.kt` 当前两个端点仍 `throw NotImplementedError()`，OpenAPI 自己标注「stub」。

### 3.4 与 SysON 定位互补

SysON 在「人对人协同图编辑」上更成熟，Flexo 在「graph-native 存储 + 大规模查询」上更强；二者其实定位互补。

## 4 OSLC ↔ SysML v2

[oslc-op/sysml-oslc-server][^repo-oslc-server]（Apache-2.0、★13、2025-12）— Eclipse Lyo Designer 生成的 OSLC 4.0 server，把 SysML v2 元模型暴露为 OSLC RM/CM/QM 资源（`org.oasis.oslcop.sysml.oslc-domain*`）。是**跨工具联邦**模式的样板（与 DOORS NG、Jama、Polarion 互联），**不是协同编辑**；典型用例是从需求工具 link 到 v2 元素，反之亦然。OMG 标准里 OSLC 也是一个 PSM（与 REST/HTTP 平行）。

## 5 MCP 服务器：LLM 协同事实标准

| 仓库 | 实现 | 状态 |
|---|---|---|
| [redsteve/SysML-v2-API-MCP-Server][^repo-redsteve-mcp] | C++、MIT、★17、2025-09 | 把 SysML v2 API endpoint 全部包装为 MCP tools；高模块化，新增 transport 容易；WIP |
| daltskin 的 `sysml-mcp` CLI[^repo-daltskin-lsp] | TS / Node、ANTLR4 | 把 LSP 能力（diagnostics、symbols、Mermaid 预览、复杂度指标）作为 MCP resources |
| [Open-MBEE/flexo-mms-sysmlv2-mcp][^repo-flexo-mcp] | Python FastMCP | 直接代理 Flexo Layer |

正在形成「**v2 API + MCP transport** 是 LLM-collab 的事实模式」共识。

**Gap**：**没有一个** MCP server 同时暴露**写 + diff + merge + 多 commit 编辑会话**，目前都是只读或单 commit 写。

## 6 模型 diff / merge 现状

### 6.1 规范层面

API 标准只给 commit 内 `changes`（DataVersion 粒度），不给跨 commit 三方合并、不给冲突表达（见 §2.2）。

### 6.2 工具层面

- Flexo `DiffMergeApi.kt` 是 stub。
- SysON 的 `ProjectDataVersioningRestService` 只读浏览。
- **第三方**：未发现 EMF Compare 针对 KerML 的 spec 适配；**视觉化 diff viewer 无生产级开源实现**。

### 6.3 文本侧

[Sensmetry Advent of SysML v2 Lesson 6][^sensmetry-lesson-6]提到 git diff 直接对 `.sysml` 文本可读，但**没有规范化格式器（formatter）来稳定 diff**。

### 6.4 Formatter 现状

- daltskin 的 LSP 提供 code actions / 格式化、SysIDE / Syside 提供 VS Code formatter（[sensmetry/sysml-2ls][^repo-sysml-2ls]，AGPL/EPL 双许可，★52，2025-10），但**没有 "sysml-fmt" 等价 gofmt 的官方工具**；多工具间的格式化结果不收敛 → diff 噪声仍多。
- **pre-commit hook**：未发现任何官方 / 主流 hook 实现。

### 6.5 与包管理结合

[sensmetry/sysand][^repo-sysand]（详见 [04-解析 IDE](04-parsing-ide-infrastructure.md) §5）走 **「v2 工程是包，每个包是独立 git repo，sysand 用 lockfile 锁版本」** 的路线，不直接操作 v2 commit，但能在 CI 中和 git 配合。这条路线 Sensmetry 押注最重，是目前最可工程化的 KerML / SysML v2 工程组织模式。

## 7 Web 平台 / SaaS-style OSS

| 平台 | 架构 | 状态 | 备注 |
|---|---|---|---|
| **Eclipse SysON** | Sirius Web (Spring/GraphQL/React/PG) | 生产级，2026-05 活跃 | 见 §1.1 |
| [mhlscvk/mbse-tool][^repo-mbse-tool] | TypeScript web，无 license，无 README 公开 | ★3、2026-04、玩具级 | 不可用于评估 |
| **Open-MBEE View Editor (VE)** | AngularJS + MMS3 | v1/Cameo 时代，Apache-2.0、★48 仍维护但无 v2 | 不要押注 v2 |
| **Stardog / TopBraid** | RDF 商业图数据库 | 闭源 | 仅可作为 Flexo Layer 替代 |
| **Sensmetry Syside Modeler** | Electron / VS Code 衍生 | 闭源商业 | 与 SysON 直接竞争，但 OSS 仅 SysIDE legacy |
| **GitHub Codespaces preview** | daltskin LSP devcontainer | OSS、轻量 | 适合个人 / 教学，非协同 |

**Sirius Web 通用底座**：除 SysON 外，[ObeoNetwork/Sirius-Web-Tutorial][^repo-sirius-tut]演示自定义 v2 视图，但社区里没看到 SysON 之外的认真自建实例。

## 8 显式 Gap 清单（对资深工程师最实用）

1. **没有生产级模型 diff/merge viewer**（视觉 + 文本 + 三方合并）。
2. **没有 P2P / CRDT 协同编辑**：所有协同都是 server-authoritative + websocket（SysON 是 GraphQL Subscription）。Yjs / Automerge 风格 v2 编辑器 = 0。
3. **没有官方 sysml-fmt**：跨工具格式不收敛；git diff 噪声仍偏高。
4. **没有 Markdown-embedded SysML v2 渲染器**：daltskin 的 Mermaid 输出最近，但还需手工嵌入；**没有 Jekyll / Hugo / MkDocs 插件**直接渲染 `.sysml` fence block。
5. **API 标准没定义 merge**：所有 merge 实现是工具私有，互不兼容。
6. **MCP servers 全是只读 / 单 commit**：缺多 commit 写、缺 diff/merge tool。
7. **WebGL / 3D 空间系统视图全空白**。
8. **真正一体化的 v2 协同 SaaS OSS = 0**：SysON 自托管、Flexo 后端、Open-MBEE 老 stack——没人提供「装一下就有协同 + 版本 + diff」的发行版（[cosgroma/mbse-lab][^repo-mbse-lab]在尝试拼装，但很早期）。
9. **Pilot Jupyter kernel 维护稳定但未现代化**（无 JupyterLab widget 双向编辑），daltskin Python client 已用 LSP 在 notebook 里走通，但仍是只读分析。

## 9 综合判断

**SysON + Flexo MMS（SysML v2 API PSM）+ daltskin LSP/MCP + sysand** 这四件套覆盖了 ~80% 用例，剩下的 ~20%（diff/merge viewer、CRDT 协同、Markdown 嵌入、统一 SaaS 发行版）是当前最值得投入的开源空白——详见 [09-缺口与机会](09-gaps-opportunities.md)。

## 参考文献

[^repo-syson]: *eclipse-syson/syson*. <https://github.com/eclipse-syson/syson>

[^syson-doc]: Eclipse SysON 用户文档. <https://doc.mbse-syson.org/>

[^repo-pilot]: *Systems-Modeling/SysML-v2-Pilot-Implementation*. <https://github.com/Systems-Modeling/SysML-v2-Pilot-Implementation>

[^repo-daltskin-lsp]: *daltskin/sysml-v2-lsp*. <https://github.com/daltskin/sysml-v2-lsp>

[^repo-explorer]: *dandelivers/sysml-v2-explorer*. <https://github.com/dandelivers/sysml-v2-explorer>

[^repo-sysmd]: *tukcps/SysMD*. <https://github.com/tukcps/SysMD>

[^repo-api-services]: *Systems-Modeling/SysML-v2-API-Services*. <https://github.com/Systems-Modeling/SysML-v2-API-Services>

[^repo-flexo]: *Open-MBEE/flexo-mms-sysmlv2*. <https://github.com/Open-MBEE/flexo-mms-sysmlv2>

[^repo-flexo-mcp]: *Open-MBEE/flexo-mms-sysmlv2-mcp*. <https://github.com/Open-MBEE/flexo-mms-sysmlv2-mcp>

[^repo-flexo-syside]: *Open-MBEE/flexo_syside*. <https://github.com/Open-MBEE/flexo_syside>

[^repo-oslc-server]: *oslc-op/sysml-oslc-server*. <https://github.com/oslc-op/sysml-oslc-server>

[^repo-redsteve-mcp]: *redsteve/SysML-v2-API-MCP-Server*. <https://github.com/redsteve/SysML-v2-API-MCP-Server>

[^repo-sysml-2ls]: *sensmetry/sysml-2ls*. <https://github.com/sensmetry/sysml-2ls>

[^repo-sysand]: *sensmetry/sysand*. <https://github.com/sensmetry/sysand>

[^sensmetry-lesson-6]: Sensmetry. *Advent of SysML v2 Lesson 6 — Version Control with Git*. <https://sensmetry.com/advent-of-sysml-v2-lesson-6-version-control-with-git/>

[^repo-mbse-tool]: *mhlscvk/mbse-tool*. <https://github.com/mhlscvk/mbse-tool>

[^repo-sirius-tut]: *ObeoNetwork/Sirius-Web-Tutorial*. <https://github.com/ObeoNetwork/Sirius-Web-Tutorial>

[^repo-mbse-lab]: *cosgroma/mbse-lab*. <https://github.com/cosgroma/mbse-lab>
