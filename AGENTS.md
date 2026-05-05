# AGENTS.md — 维护与协作约束

> 本文档同时是 [CLAUDE.md](CLAUDE.md) 的实体（CLAUDE.md 为 symlink 指向本文件），适用于 Claude Code、Cursor、Codex CLI、Aider、Continue、Roo Code、OpenCode、Gemini CLI 等所有以 AGENTS.md / CLAUDE.md 作为指令源的 AI 编程助手。

## 1 仓库目的

本仓库是一份**快照式中文综述**，汇总 OMG SysML v2 / KerML 截至 2026-05 的标准状态、学术研究、北航相关工作、以及五个维度的开源基础设施现状（解析 / 形式化 / 可视化 / 协作 / 代码生成）。**不是**：教程、操作手册、API 文档、或 SysML v2 项目模板。

## 2 内容组织

```
README.md                                # TLDR + 核心矩阵 + docs/ 索引
AGENTS.md                                # 本文件（CLAUDE.md 是 symlink）
CLAUDE.md -> AGENTS.md
docs/
├── 00-overview.md                       # 总览 / 调研方法 / 12 章导航 / 阅读路径
├── 01-standard-status.md                # OMG 4 份规范深读 / KerML 分层 / API / KEBNF / v1↔v2 转换实测
├── 02-academic-landscape.md             # 学术文献引导页（9 维主题分类）
├── 03-beihang-investigation.md          # 北航专项
├── 04-parsing-ide-infrastructure.md     # 9 套 parser / 解析 / LSP / IDE / KPAR / sysand
├── 05-formal-verification.md            # 形式化 / 验证（11 路径）
├── 06-visualization-collaboration.md    # 可视化 / 协作 / 版本控制
├── 07-codegen-execution.md              # 代码生成 / 执行 / 仿真 / CI
├── 08-baseline-comparison.md            # 8 语言对照（UML / v1 / AADL / Modelica / Capella / BPMN / TLA+ / Alloy）
├── 09-gaps-opportunities.md             # 缺口与机会窗口
├── 10-daltskin-deep-audit.md            # daltskin/sysml-v2-grammar 全仓审计 + 接入策略
├── 11-real-world-corpora.md             # 40 公开仓 × 3255 .sysml 实测语料调研
└── references.md                        # 集中参考文献（聚合索引）
```

## 3 引用规范（GFM 原生脚注）

**强制要求**：所有事实陈述、技术评估、版本声明、star / commit 数据、市场安装量、规范条款引用——**必须**有对应 cite key 脚注。

### 3.1 语法依据

本仓库采用 **GitHub Flavored Markdown 原生脚注扩展**。该扩展由 GitHub 在 2021-09-30 公告添加[^gfm-footnotes-2021]，对应 Issue / PR / Discussion / Markdown 文件等所有支持 GFM 的渲染场景，但**不支持 Wiki**[^github-basic-writing]。底层规范源自 [pandoc / Python-Markdown 的 footnotes 扩展](https://github.com/Python-Markdown/markdown/blob/master/docs/extensions/footnotes.md) 惯例，与 [Markdown Guide 「Extended Syntax」](https://www.markdownguide.org/extended-syntax/) 描述一致。注意：**核心 [GFM 规范](https://github.github.com/gfm/) 文档本身并未列出脚注**，脚注是 GitHub 在该规范基础上的额外渲染扩展。

[^gfm-footnotes-2021]: GitHub Changelog. *Footnotes now supported in Markdown fields*. 2021-09-30. <https://github.blog/changelog/2021-09-30-footnotes-now-supported-in-markdown-fields/>

[^github-basic-writing]: GitHub Docs. *Basic writing and formatting syntax — Footnotes*. <https://docs.github.com/en/get-started/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax#footnotes>

### 3.2 标准语法形式

```markdown
KerML 的 4D 时空语义被 NEMO/UFES 团队系统分析[^almeida-2024-kerml]。

[^almeida-2024-kerml]: Almeida, J.P.A. 等. *An Analysis of the Semantic Foundation of KerML and SysML v2*. ER 2024. <https://link.springer.com/chapter/10.1007/978-3-031-75872-0_8>
```

GitHub 渲染为上标编号 + 自动生成的 Footnotes 段落（带 `↩` 回链）。

**关键规则**（取自 [^github-basic-writing] 与 [^gfm-footnotes-2021]）：

1. **行内引用**：`[^id]`（caret + 方括号 + 标识符）。
2. **脚注定义**：`[^id]: 内容`。位置可在文档任意处，**渲染时 GitHub 一律收拢到文末**；本仓库惯例放在每章 `## 参考文献` 段。
3. **作用域**：脚注 ID 在**单个 markdown 文件内必须唯一**。GFM 不支持跨文件脚注引用——这就是为什么本仓库还要维护 `docs/references.md` 作为聚合索引（见 §3.4）。
4. **多行脚注**：续行前置 **2 空格**；空行结束脚注：

   ```markdown
   [^multi]: 第一行内容。
     第二行内容（前置 2 空格）。

     段落分隔后续行同样 2 空格缩进。
   ```

5. **标识符字符**：可以用任何在 HTML `id` 属性中合法的字符——纯数字 (`[^1]`)、单词 (`[^word]`)、混合 (`[^almeida-2024-kerml]`)、甚至特殊字符 (`[^@#$%]`) 都被官方接受。本仓库**强制使用** §3.3 命名规则。
6. **不可重复定义**：同一 ID 多次 `[^id]: ...` 定义，GitHub 行为未定义；务必只定义一次。
7. **回链**：自动生成 `↩` 上行链，无需手写。

### 3.3 cite key 命名

- 学术论文：`[第一作者-年份-关键词]`，如 `almeida-2024-kerml`、`molnar-2024-formal-verif`。
- GitHub 仓库：`repo-<短名>`，如 `repo-pilot`、`repo-syson`。
- 标准文档：`omg-<短名>`，如 `omg-sysmlv2-final-2025`、`omg-kerml-spec`。
- 商用产品：`vendor-<厂商-产品>`，如 `vendor-cameo-2026x`。
- 一律小写、连字符、ASCII，与 `docs/references.md` 中的锚点保持完全一致。

### 3.4 references.md 与脚注的分工

`docs/references.md` 是**聚合索引**：所有 cite key 在此文件以 `<a id="cite-key"></a>` 锚点声明，附完整书目（作者、标题、venue、年份、URL/DOI），按主题分组（标准 / 学术 / OSS / 商用 / Baseline / 教程 / 第三方）。

各 doc 内的 `[^cite-key]: ...` **不必重复完整书目**——只需写一行简短引用 + URL，让读者在不离开当前文档的前提下能识别来源即可。需要完整元数据时跳转到 `references.md`。

例：

```markdown
[^almeida-2024-kerml]: Almeida 等. *An Analysis of the Semantic Foundation of KerML and SysML v2*. ER 2024. 完整书目见 [references.md](references.md#almeida-2024-kerml)。
```

### 3.5 新增引用流程

1. 先在 `docs/references.md` 对应分类下追加新条目，含 `<a id="cite-key"></a>` 锚点与完整元数据。
2. 在引用该条目的 doc 中，inline 用 `[^cite-key]`，文末追加简短脚注定义。
3. URL 必须**直接可访问**——若仅为付费墙 PDF，需同时附 arXiv / preprint 镜像。
4. 同一篇文献在不同 doc 中复用同一 cite key。

## 4 学术风约束

本仓库以**学术综述风格**写作，不是 blog、不是 tweet。具体约束：

- **观点必须可验证**：每条评估都要能由 cite 链路追溯到一手证据（commit hash、文件行号、论文章节、规范页码）。**禁止**「业界普遍认为」「据说」「我觉得」。
- **数据必须有时间锚**：所有 star / commit / 市场安装数据必须标注采样日期（默认本仓库基准日期 2026-05-05）。
- **避免营销语言**：不写「业内领先」「颠覆性」「下一代」；写「实现了 X，未实现 Y」「截至 commit `abc1234`」「VS Code marketplace 4254 安装」。
- **正反两面同时呈现**：列优势必同时列限制；列开源项目必同时列 license、活跃度、bus factor、与规范的同步延迟。
- **不要自夸**：本仓库描述他人工作。所有「最完整」「唯一」类断言必须有横向比较证据，且范围明确（如「截至 2026-05、在我们调研到的 N 个项目中」）。

## 5 文档结构惯例

- 标题层级最多到 H4，超过应拆分小节。
- 每章首段为「本章简介」，告诉读者本章解答的问题与可跳过的小节。
- 每章末尾必须有 `## 参考文献` 章节，紧随其后是该章用到的所有 `[^cite-key]: ...` 脚注定义。
- 表格列宽控制在终端 / GitHub markdown 渲染美观范围内（≤6 列推荐，>6 列改成多个表格）。
- mermaid 图可用，但 ASCII art 与 PlantUML 不引入（保持纯 markdown 可移植）。
- 段落不要硬换行（不要 80 列换行），按完整自然段写单行长句。
- 不使用 emoji（除非引用原文）。

## 6 编辑工作流（Agent 自检清单）

向本仓库提交修改前，AI agent **必须**自检：

- [ ] 所有新增事实陈述都至少有一条 `[^cite-key]` 脚注。
- [ ] 所有新增 cite key 都已加入 `docs/references.md` 并设置 `<a id="...">` 锚点。
- [ ] 没有引入未经核对的 GitHub 仓库 / 论文 URL（最低标准：用 `gh repo view` 或 `curl -I` 验证 200 响应）。
- [ ] 文档间内部链接（`docs/xx.md`）路径正确（用 `find docs -name '*.md' | xargs grep -l 'docs/'` 自查）。
- [ ] 没有引入英文营销词、emoji、非学术口语（除非引用原文）。
- [ ] commit message 中文写作；标题 ≤72 字符，正文段落不硬换行。

## 7 维护节奏

- **OMG RTF 修订发布**：检查 `omg-sysmlv2-spec` 是否有新版本号；更新 `01-standard-status.md`。
- **每年 5 月、11 月**：重新跑一次 GitHub star / commit / VS Code 安装数据；更新 `04-` / `06-` / `07-` 三章的活跃度信号；更新 `references.md` 各 GitHub 条目末尾的「N★ · YYYY-MM」字段。
- **新论文（MODELS / SBMF / DASC / INCOSE IS / arXiv MBSE 类目）**：择优纳入 `02-academic-landscape.md`。
- **任何修改**：同步更新 `references.md` 与本 README 的「调研基准日期」。

## 8 不接受的修订请求

以下修订请求请直接关闭，**不要**因「礼貌」而接受：

- 仅基于「我用过这个工具感觉不错」、缺乏链接 / commit hash / 规范引用的评分调整。
- 把开源工具评分上调到与商用产品同档而无横向 benchmark 数据。
- 删除批判性评估（如「Sensmetry 闭源是生态最大隐忧」）以「保持中立」——批判性评估是综述价值的一部分，去除评估等同于淡化事实。
- 添加未引用源的「业界经验」或个人推荐。

## 9 与 AI 助手协作

- **Claude Code / Cursor / Aider 等**：可以用本文件作为系统提示注入；可以请求增量更新（如「拉一下 2026-05 → 2026-11 之间 Pilot 仓库的 commit，更新活跃度信号」）。
- **务必**在本文件第 6 节自检清单的约束下工作。
- 不要让 AI 助手发明 cite key 或论文 URL——若搜索不到一手出处，宁可标 `TODO: 待查实` 也不要伪造。

## 10 联系与贡献

- Issue：欢迎，但请按 §8 约束。
- PR：欢迎，但需附上修改对应的 cite key 与一手证据链接。
- 仓库维护人：[@HansBug](https://github.com/HansBug)。
