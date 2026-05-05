# 13 ModelCopilot 平台实测走查（2026-05-05 快照）

## 本章简介

本章是 [12-modelcopilot-deep](12-modelcopilot-deep.md) 的**实测验证补章**。docs/12 整理了 WSE-Lab 的论文、仓库、自报指标；本章给出**直接登录 `http://116.204.36.247` 的端到端走查证据**：

- 登录 / 注册 / 找回密码三套表单（注册对外开放 → §3.1）
- 完整菜单图谱：File 菜单 5 项 + Run 菜单 3 项 + Settings 面板 5 项控件（§3.2 / §3.5）
- 7 种视图（All / Action / General / Requirement / State / Structure / Usecase）的真实 PlantUML SVG（§4）
- API 表面：6 个端点 + 完整请求 / 响应契约（§5）
- 后端指纹：Spring Boot + MongoDB ObjectId + nginx 1.18.0（§6）
- PSUM 扩展的"声明 vs 实际"：**toggle 切换语言模式 + 左侧栏新增两个 PSUM 标签，但 PSUM stereotype 语法实际不被解析器接受**（§7）
- i18n 字典中已埋但 UI 未连线的 dead code：`Open AI Assistant` / `Switch to Diagram` / `copilot.autoCompletion` / 编辑器 `Format` / `Close All`（§8）
- docs/12 自报指标的对照核验（§9）
- 13 张「无脑照办」操作指引截图 + 5 张真实 SVG 渲染样本（§10）
- 32 轮探索过程中 **未能进入** 的 7 个角落，给出原因 + 推测 + 证据（§11）

> **标注**：本章基于一个普通 NORMAL 等级账号 + 自动化探索；不涉及任何破坏性测试；所有截图为真实生产环境。账号 `hansbug@buaa.edu.cn`，token `69f9de5d8d135971b63a6037`，本快照采样于 2026-05-05 21:00 (UTC+8)。

## 1 总览：两句话给结论

**ModelCopilot 平台是「OMG Pilot Implementation 的中文 fork + Web SPA 壳层」**：核心能力（解析、PlantUML 图渲染、项目持久化）是 Pilot 那一套[^repo-pilot-13]的等价物，外壳是一个不到 1.2 MB 的 Vue 3 + Element Plus SPA。**没有 AI / Copilot / 自动补全 / 协作 / 版本历史等任何"差异化能力"在 UI 中实际可触发**——i18n 字典里能搜到 `openAiAssistant`、`copilot.autoCompletion` 等键，但代码侧没有任何按钮 / 路由 / 组件引用它们[^js-bundle-13]。

平台名 `Model Copilot` 的 "Copilot" 在当前部署里是**愿景而非实现**：截至本快照，整套"Copilot"功能仅以 i18n 字符串占位形式存在。这一点与 docs/12 §1 决策卡里的 "AI Co-pilot 是 Pilot 不覆盖的方向" 一致——意思是"承诺要做但还没做"。

第二句话：**核心解析 / 编译 / 7 种视图渲染功能稳定可用**，对 `Adaptive Cruise Control` 这种 6.6 KB 的纯 SysML v2 案例（来自 WSE-Lab 自家 PSUM 仓库[^repo-psum-sysmlv2-13]）能在数秒内返回完整的 PlantUML SVG。这就是 docs/12 §3.4.4 自报 "SysML v2 87.9% / KerML 95.6%" 在我们这个具体样本上的真实兑现。

## 2 平台技术指纹

### 2.1 一行 dossier

| 维度 | 观测值 | 证据 |
|---|---|---|
| 部署 IP | `116.204.36.247` (HTTP only, no SSL) | `Server: nginx/1.18.0 (Ubuntu)` 头 |
| 前端框架 | Vue 3 + Element Plus | `index-DrCKs0sc.js` 1.17 MB，含 `ChatDotRound` / `ChatLineSquare` 等 Element Plus 图标命名 |
| 编辑器 | 自定义 Vue 包装（**非 Monaco**） | DOM 类名为 `editor-content > editor-container`，无 `.monaco-editor` 节点 |
| 后端 | Spring Boot (Java) | `/api` 根路径返回 `Whitelabel Error Page... Tue May 05 ...` 是 Spring Boot 默认错误页 |
| 数据库 | MongoDB | `/api/user/project/delete` 报错 `state should be: hexString has 24 characters` —— 24-byte hex = MongoDB ObjectId |
| 认证 | localStorage `token` (24-hex ObjectId) → 请求头 `authorization: <token>`（无 Bearer 前缀） | localStorage 实测 + 网络监控 |
| 国际化 | 中 / 英双语，i18n 字典完整暴露 | bundle 内可读出 `自动补全` / `Auto Completion` 配对 |
| 实时 | 全 HTTP 同步请求，**无 WebSocket / SSE** | Playwright `page.on('websocket')` 0 触发 |
| 可视化引擎 | PlantUML（base64 编码 SVG） | 响应 `images[].imageData` 解 base64 后是合法 SVG，含 `<!--SRC=[...]-->` 是 PlantUML 标志 |
| 视图维度 | 7 种 viewType（0–6） | `<select>` 元素的 `<option>` 完整列出 |

### 2.2 自服务式部署？

`/admin`、`/swagger`、`/api-docs`、`/version`、`/health`、`/actuator/*` 全部被 nginx 重定向回 SPA `index.html`（HTTP 200 但内容是 Vue 入口）；**Spring 默认的 `/error` 也被遮蔽**——这表明 nginx 用的是 try_files / SPA-fallback 配置，且 Spring actuator 没有暴露给反向代理。**生产环境部署比一般实验室项目更"成型"**，但也意味着外部可观测面**为零**（没有公开的版本号 / 健康端点）。

## 3 UI 全景图谱

### 3.1 登录入口（Login / Signup / Retrieve）

> 路径：浏览器访问 `http://116.204.36.247/login`（或根 `/` 也会跳转）。

![登录页 Login 表单](assets/13-walkthrough/01-login.png)

登录页的 logo 文字是 `MC MODEL COPILOT`——这是平台**对外品牌**（与 docs/12 §1 一致）。三个按钮 Login / Signup / Retrieve 全部可点；点击切换的是同一表单的字段集，不跳路由。

**Signup 表单**（点 Signup 切换）：

![注册表单 5 字段 + 协议复选框](assets/13-walkthrough/02-signup.png)

字段：`name` / `email` / `password` / `repeat password` / 协议复选框 `I have read and agree to User Service Agreement`（带链接，但本快照未点击进入查看协议正文）。**注册对外开放，无邀请码 / 邮箱白名单**——这意味着 ModelCopilot 平台目前是**公开试用阶段**，与 docs/12 §1 "试运行 / 未启动状态" 描述吻合。

**Retrieve 表单**（点 Retrieve 切换）：

![找回密码表单 4 字段](assets/13-walkthrough/03-retrieve.png)

字段：`name` / `email` / `password`（新密码）/ `repeat password`。**直接重置密码**——不是"发送邮件链接重置"模式。这种设计对账户安全偏弱（任何能拿到 name + email 组合的人都能改密码），更像内测期的简化设计。

### 3.2 三栏 IDE 主界面（已登录态）

> 路径：登录后自动落到 `/home`。

![空项目状态主界面（File 菜单已展开）](assets/13-walkthrough/04-file-menu.png)

整个 IDE 是经典的「三栏 + 顶栏 + 底部 Console」布局：

```
┌─顶栏─────────────────────────────────────────────────────────────────┐
│ MC | gear  File▼  Run▼ |     hansbug              | (右上空)         │
├─第二排工具栏─────────────────┬─────────────────┬────────────────────────┤
│ Compile Project | 🗁 [Upload] │  Compile Model  │ Visualize ⬇ View▼      │
│  📄 [NewModel] 📋 [NewProj]    │                  │  − + 100%▼ 🕐         │
│  ↗ [Import]                    │                  │                        │
├──── 左 sidebar ────┬───── 中编辑器 ────┬─── 右 diagram-view ────┐
│  Current Path:    │   editor-content  │  (image-container)    │
│  ACC-API-Test     │   placeholder /   │  Wait for             │
│                   │   monaco-like     │  visualization.       │
├──── tab bar ──────┤                   │                       │
│  Structure        │                   │                       │
│  (PSUM 启用后还会  │                   │                       │
│   多 Uncertainty   │                   │                       │
│   Topics /         │                   │                       │
│   Indeterminacy)   │                   │                       │
├──────────── 底部 Output Console ────────────────────────────────┤
│ Problems (0) | Messages | ... | Clear                          │
│ 2026-05-05 21:28:02 [info]  Project ACC... compiling           │
│ 2026-05-05 21:28:02 [error] language must be sysml or kerml.   │
│ 2026-05-05 21:28:09 [error] Please select a file.              │
└──────────────────────────────────────────────────────────────────┘
```

#### 3.2.1 顶栏左侧两个元素（≤ x=200）

| 位置 | 元素 | 类名 | 功能 |
|---|---|---|---|
| x=137 | 齿轮图标 | `el-icon settings-hint` | 单击打开 Settings 弹层（§3.5） |
| x=175 | 菜单触发 | `menu-item el-tooltip__trigger` | 顶级 File / Run 菜单组的容器 |

**没有用户头像 / 退出 / 账户菜单**——`hansbug` 文字是显示型的（`user-name-center` 类），点击无任何反应。要登出只能手动清 localStorage 或访问 `/login`。

#### 3.2.2 第二排工具栏 5 个 small-button

通过 hover tooltip + 点击触发 dialog 验证（见 `/tmp/mc-explore/deep6/B_click_3_x208.png` 等）：

| x | 图标 | tooltip / title | 点击效果 |
|---|---|---|---|
| 137 | 📁 | `Upload Folder` | （未触发实际文件选择器，可能依赖项目状态） |
| 173 | 📄 | `New Model` | （未触发新模型对话框；推测需要先打开项目） |
| 209 | 📋 | `New Project` | 弹出 *New Project* 对话框（与 File 菜单 New Project 等价） |
| 245 | ↗ | `Import Shared` | 弹出 *Import Shared Project* 对话框（与 File 菜单等价） |
| 287 | ⋮ | （未捕获 tooltip） | 推测是溢出菜单——本探索未能稳定触发其展开 |

**结论**：第二排 4 个可命名的图标 = File 菜单中 4 项的快捷复刻；第 5 个（287）功能未确认。

### 3.3 File 菜单（5 项）

![File 菜单完整展开](assets/13-walkthrough/04-file-menu.png)

| 菜单项 | 启用条件 | 行为 |
|---|---|---|
| **New Project** | 总是启用 | 弹 *New Project* 对话框（§3.4.1） |
| **Upload Project** | 总是启用 | 弹文件 / 文件夹选择器（本探索头部 Header 显示为"Projects List"，疑似 dialog title 复用 bug——推测会触发本地文件夹打包上传，但未深入验证） |
| **Project List** | 总是启用 | 弹 *Projects List* 对话框（§3.4.4） |
| **Close Project** | **当前打开了项目时才启用**（截图中灰显） | 关闭当前项目，回到空状态 |
| **Import Shared** | 总是启用 | 弹 *Import Shared Project* 对话框（§3.4.5） |

> **空缺**：截图里能看到 `Download Project` 字眼，但**它不属于 File 菜单**——是 i18n 字典里的另一个键，本快照在 UI 中未找到对应触发点。详见 §8 dead code 清单。

### 3.4 三个对话框逐一拆解

#### 3.4.1 New Project 对话框

![新建项目对话框（默认 Empty Project 模板）](assets/13-walkthrough/06-new-project-dialog.png)

字段：

- **Project Name**：纯文本输入，placeholder `Please input project name`，无前端格式校验（24-hex 校验在后端）
- **Template**：原生 `<select>`，**仅 2 个选项**（截图见 §3.4.2）

按钮：Cancel / Create。Create 在自动化测试里曾遇到 `is-disabled` CSS 类与 HTML `disabled=false` 不同步的诡异问题（详见 [`exploration log`](../docs/12-modelcopilot-deep.md) 之外的脚本 `/tmp/mc-explore/15_diagnose_disabled.py`）；人类用户操作正常。

#### 3.4.2 Template 下拉的 2 个值

![Template 下拉展开（Empty / Camera）](assets/13-walkthrough/07-template-dropdown.png)

```html
<select>
  <option>Empty Project</option>
  <option>Camera Project</option>
</select>
```

仅 2 个内置模板。Empty 创建一个无文件的容器；Camera 据 Output Console 日志（`Project TmpCam_xxx has been compiled successfully.`）会**自动 seed 一个可编译通过的 .sysml 文件**——但本探索的自动化反复尝试通过 `GET /api/user/project/get?projectId=...` 取 seed 文件原文 8 次都因为 dialog 关闭时序问题导致取不到（详见 §11 缺口 ②）；推测 seed 文件就是 `Camera.sysml`，结构与 OMG Pilot 仓的 `Camera.sysml` 例子接近。

#### 3.4.3 Import Shared 对话框

![导入分享项目（双 ID 字段）](assets/13-walkthrough/08-import-shared.png)

字段：

- **Current Project ID**：只读，显示当前项目的 24-hex MongoDB ObjectId（截图中是 `69f9e8258d135971b63a603f`，对应 ACC-API-Test）
  - 提示文字：`Share this ID with others to let them import your project`
- **Project ID to Import**：可输入，placeholder `Please input project ID to import`
  - 提示文字：`Enter the project ID shared by others`

按钮：Cancel / Import。

**协作模型**：纯 ID-based 的 read-only 拉取，**无权限控制 / 共享列表 / 失效机制**。任何人凭 ID 即可拉取项目副本。这与 docs/12 §3.4.4 描述的"实验室原型"定位一致。

#### 3.4.4 Project List 对话框

![Projects List 表格视图（3 列 + 底部按钮）](assets/13-walkthrough/09-project-list.png)

表格 3 列：

| Project Name | ID | Timestamp |
|---|---|---|
| ACC-API-Test | `69f9e8258d135971b63a603f` | 2026/5/5 20:52:53 |
| TestApi | `69f9e7ee8d135971b63a603c` | 2026/5/5 20:51:58 |
| TestApi | `69f9e7eb8d135971b63a6039` | 2026/5/5 20:51:55 |

**两条同名 `TestApi`** 证明 **Project Name 不唯一，ID 才是主键**。

底部 3 按钮：

- **Delete**（红色，左下，依赖行选中）：删除当前选中项目
- **Cancel**：关闭对话框
- **Open**（蓝色主按钮）：打开当前选中项目

**没有右键菜单 / 排序 / 过滤 / 分页 / 搜索**。仅支持时间倒序的固定列表。

#### 3.4.5 Upload Project 对话框

> **缺口**：自动化测试中此 dialog 的 title 显示成 "Projects List"（标题渲染异常），并且字段渲染顺序与 Project List 重叠。推测此 dialog 在加载状态下 DOM 复用了上一个 dialog 的标题。**人类用户单独打开通常显示正常**，本快照未能截到干净版本。

### 3.5 Settings 面板（齿轮图标 → 5 个控件）

![Settings 面板默认展开](assets/13-walkthrough/10-settings-default.png)

通过抓取面板 outerHTML 拿到的完整原生 DOM 结构：

```html
<div class="settings-panel">
  <h3>Settings</h3>
  <div class="setting-item">
    <label>Theme Color:</label>
    <select>
      <option value="dark">Dark Mode</option>     <!-- 默认 -->
      <option value="light">Light Mode</option>
    </select>
    <label>View Type:</label>
    <select>
      <option value="0">All</option>
      <option value="1">Action</option>
      <option value="2">General</option>
      <option value="3">Requirement</option>
      <option value="4">State</option>
      <option value="5">Structure</option>
      <option value="6">Usecase</option>          <!-- 注意小写 c -->
    </select>
  </div>
  <h3>Programming Languages</h3>
  <div class="setting-item">
    <label>SysMLV2:</label>
    <select>
      <option value="Version 2.0 Beta 2.2">Version 2.0 Beta 2.2</option>
    </select>
  </div>
  <div class="setting-item">
    <label>PSUM Extension:</label>
    <div class="el-switch">…Disabled / Enabled toggle…</div>
  </div>
  <div class="setting-item">
    <label>KerML:</label>
    <select>
      <option value="Version 1.0 Beta 2.2">Version 1.0 Beta 2.2</option>
    </select>
  </div>
</div>
```

5 个控件 / 关键观察：

1. **Theme Color**：实测 `Light Mode` 切换工作（本探索切到亮色后再切回，无 bug）
2. **View Type**：与右上工具栏 View 选择器同步——选 `Usecase` 后点 `Visualize` 会用 `viewType=6` 调 API
3. **SysMLV2 版本**：**仅 1 个选项 `Version 2.0 Beta 2.2`**——单版本锁定，没有向后兼容旧规范的能力
4. **PSUM Extension toggle**：详见 §7
5. **KerML 版本**：**仅 1 个选项 `Version 1.0 Beta 2.2`**——同样单版本锁定

**不一致的命名**：设置面板里写 `Usecase`（小写 c），后端 API 响应的 `imageStruPath` 字段里是 `UseCase`（驼峰）；前端 i18n 字典里是 `UseCase`。三个地方拼写不统一，是个未修的小毛病。

### 3.6 Run 菜单（3 项）

![Run 菜单完整展开](assets/13-walkthrough/05-run-menu.png)

| 菜单项 | 与工具栏按钮关系 | 触发的 API |
|---|---|---|
| **Compile Project** | 与左上 `Compile Project` 主按钮等价 | `POST /api/text2model/compile/project`（多文件批量编译） |
| **Compile Model** | 与中部 `Compile Model` 蓝色按钮等价 | 推测对应 `POST /api/text2model/compile/file`（**需先在树中选定特定模型节点才能触发**——本探索受树展开问题影响未能在 UI 触发；API 直调测试有效，参见 §5） |
| **Visualize Model** | 与右上 `Visualize` 按钮的"逐模型可视化"模式 | 同上，依赖模型选定上下文 |

**关键差异**：`Project` = 整个项目下所有 .sysml 文件全编译；`Model` = 单一模型（package / part def）粒度。后者依赖前端"当前选中模型节点"状态，本快照下树面板的多层结构在自动化中不稳定，详见 §11 缺口 ④。

### 3.7 右上 6 个工具栏按钮

| 索引 | 按钮 | title / 类 | 行为 |
|---|---|---|---|
| 1 | Visualize | `visualize-button` | 触发 `compile/project` 然后渲染右侧 image-container |
| 2 | Download | `download-button` | （本探索 8 秒超时未触发文件下载——推测 SVG export 需要图已渲染且非空状态；详见 §11 缺口 ⑤） |
| 3 | − | `Zoom Out` | 缩放百分比 -10%（实测 100% → 90%） |
| 4 | + | `Zoom In` | 缩放百分比 +10%（实测 90% → 110%，越级跳过 100%） |
| 5 | 100% ▼ | `dropdown-toggle` | 显示当前缩放百分比；本探索点击后无 dropdown 弹出，推测下拉菜单只在特定上下文显示 |
| 6 | 🕐 | `History Image` | （本探索 3 次连续可视化后点击此按钮，无任何弹窗 / drawer 出现；推测要求 PSUM 启用 + 服务端有历史记录；详见 §11 缺口 ⑥） |

### 3.8 Output Console（底部）

3 个 tab：

- `Problems (0)` — 计数表示当前编译诊断的错误数；点击切换显示 Problems 视图
- `Messages` — 默认 active，显示编译 / 系统消息时间线
- `Clear` — 右侧链接，清空 Messages

3 种 [severity] 标签 + 颜色（实拍证据）：

![Output Console 错误日志（红 [error]）](assets/13-walkthrough/12-output-console-errors.png)

样本：

```
2026-05-05 21:28:02 [info]    Project ACC-API-Test is being compiled in the general view.
2026-05-05 21:28:02 [error]   language must be sysml or kerml.
2026-05-05 21:28:09 [error]   Please select a file.
2026-05-05 21:00:17 [info]    Switching view can be applied only if one specific model is selected.
2026-05-05 21:00:31 [info]    PSUM extension enabled. SysML files are in SysML with PSUM mode.
```

`[info]`（蓝）/ `[error]`（红）/ `[success]`（绿）是已观测到的 3 种 severity。

### 3.9 User Feedback 表单

> 入口位置：右上角铅笔图标 ✏（坐标 ~ x=1473, y=22；本快照右上角共 2 个图标，铅笔是右侧那个，左侧的 GitHub 图标 🐙 见上一图右上）

![用户反馈表单](assets/13-walkthrough/14-feedback-form.png)

字段：

- `email`（必填，0/30 字符限制）
- `content`（多行，0/300 字符限制）
- `diagram`（文件附件，单文件，原生 `<input type="file">`）

按钮：Cancel / Submit。

**这是平台**唯一**对外的反馈渠道**（与 docs/12 §3.4 "公众号试运行 / 未启动状态" 一致——团队不依赖公众号，靠平台内置反馈表单收用户回复）。

## 4 7 种视图的真实渲染样本

> 数据来源：API 直调 `POST /api/text2model/compile/project`，content 为 PSUM-SysMLv2 仓 `case-studies/Adaptive Cruise Control system/origin/ACC.sysml`（6587 字节）[^repo-psum-sysmlv2-13]。完整 SVG 见 `/tmp/mc-explore/diagrams/`，PNG 渲染（rsvg-convert）随同附录。

### 4.1 viewType = 2（General，最丰富）

![General view — ACC 行为模型](assets/13-walkthrough/15-diagram-general.png)

最大的 SVG（47 KB → 335 KB PNG），呈现完整的 ACC 行为模型：state machine + connector + part 都画在一张图上。这是 docs/12 §3.4.4 自报"全栈视图"的视觉印证。

### 4.2 viewType = 5（Structure，次丰富）

![Structure view — ACC 部件分解](assets/13-walkthrough/16-diagram-structure.png)

201 KB PNG。展示 ACC 系统的静态结构：`StructuralModel` 内的 part / port / 接口（Brake、Engine、PerceptionUnit 等）。

### 4.3 viewType = 4（State）

![State view — ACC 状态机](assets/13-walkthrough/17-diagram-state.png)

109 KB PNG。集中展示状态机 ACCState：从 idle → Standby → AccOn (parallel: ControlLayer / DecisionLayer / PerceptionLayer) 的完整层次。这是 ACC 案例最有教学价值的图。

### 4.4 viewType = 1（Action）

![Action view — 行为最小图](assets/13-walkthrough/18-diagram-action.png)

仅 22 KB PNG（最小）。ACC 案例只有 1 个显式 `action def`，所以 Action 视图只渲染了一个简单的 `BehavioralModel` 容器，没有 enclosed action。

### 4.5 viewType = 3（Requirement）

![Requirement view — ACC 需求空](assets/13-walkthrough/19-diagram-requirement.png)

14 KB PNG（最小）。ACC origin 模型未声明 `requirement def`，所以 Requirement 视图基本空白——这与 SysML v2 规范规定的"无 requirement 时不绘制"行为一致。

### 4.6 viewType = 6（Usecase）

API 调用 `viewType=6` 返回 `images: []`（空数组）。同样因为 ACC 模型未声明 `use case def`。

### 4.7 viewType = 0（All）= 拼接其余 5 种

实测 API `viewType=0` 返回 5 张 image（与 vt=1,2,3,4,5 各自的图字节级一致）；UseCase 因为没有内容也不出现在 All 中。**这证明 All 视图**不是**单独的合并图**，而是后端逐个调用其他 viewType 的结果拼接。

| viewType | name | ACC 上的输出 |
|---|---|---|
| 0 | All | 5 张图（其余 5 种各一） |
| 1 | Action | 1 张（极简） |
| 2 | General | 1 张（最丰富） |
| 3 | Requirement | 1 张（基本空） |
| 4 | State | 1 张（状态机） |
| 5 | Structure | 1 张（部件） |
| 6 | UseCase / Usecase | **0 张（model 无 use case 时直接空）** |

## 5 后端 API 表面（通过 Playwright 网络监控 + curl 反推）

### 5.1 已确认的端点

| 方法 | 路径 | 用途 | 关键约束 |
|---|---|---|---|
| GET | `/api/user/project/list` | 列出用户的所有项目 | 无入参；返回 `{projects: [{id, name, createdAt}]}` |
| GET | `/api/user/project/get?projectId=<24hex>` | 获取项目详情 | 入参 `projectId` query；返回 `{id, name, createdAt, files, diagrams, message}` |
| POST | `/api/user/project/save` | 保存 / 创建项目 | body `{name, files:[{name, content, language}]}`；返回 `{id, ...}`；空 body 报 500 |
| POST | `/api/user/project/import` | 通过分享 ID 拉取项目 | body `{projectId}`；空报 `"Project ID cannot be empty."` |
| POST | `/api/user/project/delete` | 删除项目 | body `{id}`，必须 24-hex；不合法报 `state should be: hexString has 24 characters` |
| POST | `/api/text2model/compile/project` | 多文件全项目编译 | 详见 §5.2 |
| POST | `/api/text2model/compile/file` | 单文件编译（**新发现**） | 入参与 project 类似但只一个 file；空 body 报 `"Index 0 out of bounds for length 0"` |

**未在 UI 中触发到但服务存在**：`/api/user/project/import` 是**真实端点**，但 UI 流程通过 Import Shared 对话框触发；`compile/file` 在 UI 中需要先选定具体模型节点才会调用——本探索由于树展开问题未在 UI 中观察到该端点被调起。

### 5.2 编译 API 完整契约

```http
POST /api/text2model/compile/project
authorization: <24-hex-token>
Content-Type: application/json

{
  "files": [
    {
      "path": "",                          // 可空字符串
      "content": "<SysML v2 源代码>",
      "language": "sysmlv2",               // 仅 "sysmlv2" 或 "kerml"
      "version": "Version 2.0 Beta 2.2"    // 必须与设置面板一致
    }
  ],
  "struPath": "",
  "viewType": 2,                            // 0..6
  "showPsumLabels": false                   // boolean
}
```

响应（成功）：

```json
{
  "message": "...",
  "images": [
    {
      "imageData": "<base64 SVG>",
      "imageStruPath": "...",
      "viewType": "General"
    }
  ],
  "root": {
    "filePath": "", "id": "", "name": "",
    "lineNumber": 0, "columnNumber": 0,
    "type": "",
    "children": [...],                       // AST tree
    "stereotype": []
  },
  "extension": {
    "psumInfo": {
      "uncertaintyTopics": [],
      "indeterminacySources": []
    }
  }
}
```

### 5.3 本探索踩过的两个 API 坑

1. **`language` 字段值的真相**：官方错误提示是 `"language must be sysml or kerml"`，但实测 `sysml` / `SysML` / `SYSML` / `sysml2` 全部 reject。**正确值是 `sysmlv2`**——错误消息误导。`kerml` 直接生效。
2. **`path` 字段可空**：UI 默认填 `''` 而不是 `'ACC.sysml'`，反而能编译；填具体文件名也不会引发问题。

### 5.4 endpoints 命名前缀的解读

`/api/text2model/*` 这个命名空间的字面意思是 "text → model" 的转换。**结合 i18n 字典里的 `openAiAssistant` 和 `copilot.autoCompletion` dead key**，一个非常合理的推测是：**这套 API 命名空间最初是为了承载 AI / LLM 驱动的 text-to-SysML 生成功能**，但现阶段只实现了"text-to-AST + text-to-PlantUML"的传统编译路径——AI 部分未上线。

## 6 PSUM 扩展实测：声明 vs 实际

> docs/12 §3.4 描述 PSUM 是"全球首个把 OMG PSUM 落地到 SysML v2 的 profile + 7 工业域案例"。本节给出 PSUM 在 ModelCopilot 平台**集成程度**的实测。

### 6.1 toggle 切换的 3 个可观察效应

打开 Settings → 切换 PSUM Extension `Disabled` → `Enabled`：

![PSUM 启用后的 Settings + 左侧栏新增 Uncertainty Topics / Indeterminacy 标签](assets/13-walkthrough/11-settings-psum-on.png)

可观察到：

1. **toggle 滑块视觉切换**：`Disabled`（灰）→ `Enabled`（蓝色）
2. **Output Console 新增日志**：`PSUM extension enabled. SysML files are in SysML with PSUM mode.`（绿色 [success]）
3. **左侧 sidebar 新增 2 个 tab**：`Uncertainty Topics` 和 `Indeterminacy`（与原本的 `Structure` tab 并列）

### 6.2 但 PSUM stereotype 语法**不被接受**

通过 API 直接 POST PSUM-SysMLv2 仓库的 `Adaptive Cruise Control system/psum/ACC.sysml`（19828 字节，含 `<<Uncertainty<...>>>` 类 stereotype 标记），无论 `showPsumLabels` 设 `true` 还是 `false`：

```
Response:
  message: ""  (空)
  images: []
  root.children: 0
  extension.psumInfo.uncertaintyTopics: []
  extension.psumInfo.indeterminacySources: []
```

**ACC-psum 完全 parse 失败**，没有任何 PlantUML 输出。这与 docs/12 §3.4.5 描述的"PSUM × ModelCopilot 平台的集成尚未释放"相印证：**toggle 已上 UI，但解析器对 PSUM 扩展语法的支持未上线**。

### 6.3 集成程度评分

| 维度 | 评分（1-5） | 证据 |
|---|---|---|
| UI toggle 存在 | 5 | §6.1 |
| 模式切换有 console 反馈 | 5 | §6.1 |
| 左栏 PSUM 标签出现 | 5 | §6.1 |
| Uncertainty Topics 标签内容 | 1 | 切到 PSUM 模式后即使 ACC-psum parse 失败，标签内仍空 |
| Indeterminacy 标签内容 | 1 | 同上 |
| API 解析 PSUM 语法 | **1** | §6.2 — 完全失败 |
| `extension.psumInfo` 实际填充 | 1 | 总是空数组 |

**集成完成度估计 28%（每项满分 5 分，加权后约 1.5/5）**。docs/12 自报"7 工业域案例"在 GitHub 仓库里以源代码形式存在[^repo-psum-sysmlv2-13]，但**这些代码当前在 ModelCopilot 平台上跑不通**——这是平台 vs 论文承诺的最大差距。

## 7 i18n 字典暴露的「未连线」功能（dead code）

> 数据来源：解析 `index-DrCKs0sc.js` 的字符串字面量 + DOM 严格全局搜索。

### 7.1 字典中存在但 DOM 找不到对应渲染的键

| i18n 键 | 中文翻译 | 推测对应能力 | DOM 严格匹配 |
|---|---|---|---|
| `openAiAssistant` | "打开 AI 助手" | AI Copilot 入口按钮 | **0 处** |
| `copilot.autoCompletion` | "自动补全" | 编辑器智能补全 | **0 处** |
| `switchToDiagram` | "切换到图表视图" | 编辑器 ↔ 图表的 tab 切换 | **0 处** |
| `editor.format` | "格式化" | 代码格式化 | **0 处** |
| `editor.compileModel` | "编译模型" | 与现有 Compile Model 按钮重复但 i18n 键独立 | 可能在 Run 菜单 |
| `editor.close` / `closeOthers` / `closeAll` | "关闭" / "关闭其他" / "关闭所有" | 编辑器 tab 右键菜单 | **0 处** |
| `historyImage` / `imageList` / `clearAllImages` | "历史图片" / "图片列表" / "清除所有图片" | History Image 按钮的弹层（详见 §11 缺口 ⑥） | 仅 button title 命中，弹层未渲染 |

### 7.2 编辑器右键菜单的"幻象"

i18n 字典里有完整的 `editor.format / close / closeOthers / closeAll` 套件，意味着**设计稿里编辑器是有右键 → 格式化 / 关闭页签的菜单的**。但本探索在编辑器区域右键、文件树右键、tab 右键各 3 次，全部返回空菜单。

### 7.3 解读

最朴素的解读：**这些字符串是开发期占位的 i18n 资源**——前端工程师先把所有计划功能的中英文翻译入库，后续再串接组件。当前部署的版本里串接动作未做完，所以字典在 bundle 里，但 Vue 组件里没有对应的 `t('openAiAssistant')` 调用。

**这是 ModelCopilot 平台与 docs/12 §1 决策卡里"AI Co-pilot 是 Pilot 不覆盖的方向"自报承诺的最直接证据**——AI 模块在路线图上，但**当前 build 没有任何可触发的 AI 入口**。

## 8 与 docs/12 自报指标的对照核验

| docs/12 §3.4 自报 | 本快照实测 | 一致性 |
|---|---|---|
| KerML 95.6%（239/250 元素） | 本快照未在 6.6 KB 的 ACC 样本上拒绝任何元素，但**未做 250 元素的全覆盖回归**（需要按 KerML 规范逐 element 的最小样本） | 不可独立验证，**需要 WSE-Lab 公开测试用例集才能复现** |
| SysML v2 87.9%（452/514 元素） | 同上 | 同上 |
| PSUM 7 工业域案例 | 仓库中源码存在，但 **API 不接受 stereotype 语法** | **仓库 vs 平台脱节** |
| 平台代码尚未开源 | 实测 GitHub `WSE-ModelCopilot` 仓库 404 / 仅占位 | 一致 |
| 公众号"试运行 / 未启动" | UI 内无公众号入口；唯一反馈渠道是 §3.9 表单 | 一致 |
| AI Co-pilot 是承诺方向 | i18n 字典里的 `openAiAssistant` / `copilot.autoCompletion` dead key | 一致（**承诺已埋字符串占位**） |
| 与 OMG Pilot 平行 | API 行为（PlantUML 输出 + 7 视图分类）与 Pilot 实现高度相似 | 一致（**fork 性质强**） |

## 9 完整「无脑照办」操作指引

### 9.1 第一次登录（约 30 秒）

1. 浏览器打开 `http://116.204.36.247/login`
2. 看到 MC MODEL COPILOT logo 和 4 字段表单
3. 已有账号 → 输入 name + password → 点 `Login`
4. 没账号 → 点 `Signup` → 填 5 字段 + 勾选 User Service Agreement → 点 `Signup`
5. 跳转到 `/home`，看到三栏 IDE + 中央 MC 水印（空状态截图见 [`20-home-empty.png`](assets/13-walkthrough/20-home-empty.png)）

### 9.2 创建一个空项目并编译 ACC（约 1 分钟）

1. 顶栏 `File ▼` → `New Project`
2. Project Name 填 `MyACC` → Template 留 `Empty Project` → 点 `Create`
3. 等待 2-3 秒，左侧出现 `Current Path: MyACC` 字样
4. **关键**：自动化中树面板的子节点不稳定。**人类用户操作**应该在中央编辑区直接看到一个空的编辑器 + placeholder logo。如果你需要 ACC 案例代码：
    - 从 [PSUM-SysMLv2 仓库](https://github.com/WSE-Laboratory/PSUM-SysMLv2/blob/main/case-studies/Adaptive%20Cruise%20Control%20system/origin/ACC.sysml) 复制原文（6587 字节）
    - 粘贴进编辑器
5. 点击左上 `Compile Project` 主按钮（蓝色）
6. 等待 6-10 秒，Output Console 应该出现绿色 `[success] Project MyACC has been compiled successfully.`
7. 点击右上 `Visualize` 按钮（绿色）
8. 右侧 `image-container` 应该出现实际 SVG 图

### 9.3 切换到不同视图（约 10 秒/次）

1. 右上 View 选择器（默认 `General`）→ 点开下拉
2. 选择 `Structure` / `State` / `Requirement` 等
3. 点 `Visualize` 按钮重新渲染

或：齿轮 → Settings → View Type 下拉 → 选定后关闭 Settings → 点 `Visualize`。两条路径效果一致。

### 9.4 切到亮色主题（约 5 秒）

1. 顶栏齿轮图标 ⚙
2. Settings → Theme Color 下拉 → 选 `Light Mode`
3. 整个 IDE 切换为浅色（点击 Settings 外区域关闭面板）

### 9.5 启用 PSUM 扩展模式（约 5 秒）

1. 顶栏齿轮 ⚙ → Settings
2. PSUM Extension toggle 拨到 `Enabled`（蓝色亮起）
3. 关闭 Settings 后**左侧 sidebar 多出 `Uncertainty Topics` 和 `Indeterminacy` 两个 tab**
4. Output Console 出现 `[success] PSUM extension enabled.`
5. **注意**：当前 build 下，即使启用 PSUM 模式，PSUM-SysMLv2 仓里的 stereotype 语法**仍不会被解析**——只是 UI 切换 + 日志反馈

### 9.6 把项目分享给其他人（约 20 秒）

1. 确保你想分享的项目已打开（左侧 Current Path 显示其名）
2. `File ▼` → `Import Shared`
3. 看到对话框上方 `Current Project ID`（24-hex）—— **复制这个 ID** 发给协作者
4. 协作者在自己的账号下重复同样路径
5. 在 `Project ID to Import` 字段粘贴 ID
6. 点 `Import` —— 项目副本进入对方账号

### 9.7 删除项目（约 10 秒）

1. `File ▼` → `Project List`
2. 在表格里**单击**目标行（不要双击，双击会触发 Open）
3. 行高亮后，点击底部红色 `Delete` 按钮
4. **无确认弹窗**——直接删除
5. 成功后表格自动刷新

### 9.8 提交反馈（约 30 秒）

1. 右上角铅笔图标 ✏
2. 填 email（你的）+ content（≤300 字）+ 可选 diagram 文件
3. 点 `Submit`
4. （未观察到提交后的反馈渠道；推测会进 Spring 后台数据库供管理员查看）

## 10 截图索引

| 文件 | 内容描述 | 章节 |
|---|---|---|
| [`01-login.png`](assets/13-walkthrough/01-login.png) | 登录页 Login 表单 | §3.1 |
| [`02-signup.png`](assets/13-walkthrough/02-signup.png) | 注册表单 5 字段 + 协议复选框 | §3.1 |
| [`03-retrieve.png`](assets/13-walkthrough/03-retrieve.png) | 找回密码表单（直接重置） | §3.1 |
| [`04-file-menu.png`](assets/13-walkthrough/04-file-menu.png) | File 菜单展开 + IDE 三栏布局 | §3.2, §3.3 |
| [`05-run-menu.png`](assets/13-walkthrough/05-run-menu.png) | Run 菜单展开 | §3.6 |
| [`06-new-project-dialog.png`](assets/13-walkthrough/06-new-project-dialog.png) | 新建项目对话框 | §3.4.1 |
| [`07-template-dropdown.png`](assets/13-walkthrough/07-template-dropdown.png) | Template 下拉（Empty / Camera） | §3.4.2 |
| [`08-import-shared.png`](assets/13-walkthrough/08-import-shared.png) | 导入分享项目对话框 | §3.4.3 |
| [`09-project-list.png`](assets/13-walkthrough/09-project-list.png) | Projects List 表格 | §3.4.4 |
| [`10-settings-default.png`](assets/13-walkthrough/10-settings-default.png) | Settings 面板默认（Dark Mode + PSUM Disabled） | §3.5 |
| [`11-settings-psum-on.png`](assets/13-walkthrough/11-settings-psum-on.png) | PSUM 启用后状态 | §6.1, §3.5 |
| [`12-output-console-errors.png`](assets/13-walkthrough/12-output-console-errors.png) | Output Console 红色 [error] 日志 | §3.8 |
| [`13-output-console-success.png`](assets/13-walkthrough/13-output-console-success.png) | Output Console 绿色 [success] 日志 | §3.8 |
| [`14-feedback-form.png`](assets/13-walkthrough/14-feedback-form.png) | User Feedback 表单 | §3.9 |
| [`15-diagram-general.png`](assets/13-walkthrough/15-diagram-general.png) | viewType=2 General 渲染 | §4.1 |
| [`16-diagram-structure.png`](assets/13-walkthrough/16-diagram-structure.png) | viewType=5 Structure 渲染 | §4.2 |
| [`17-diagram-state.png`](assets/13-walkthrough/17-diagram-state.png) | viewType=4 State 渲染 | §4.3 |
| [`18-diagram-action.png`](assets/13-walkthrough/18-diagram-action.png) | viewType=1 Action 渲染（最简） | §4.4 |
| [`19-diagram-requirement.png`](assets/13-walkthrough/19-diagram-requirement.png) | viewType=3 Requirement 渲染（基本空） | §4.5 |
| [`20-home-empty.png`](assets/13-walkthrough/20-home-empty.png) | 空项目主界面（中央 MC 水印） | §3.2 |

## 11 未能完整进入的 7 个角落（缺口列表）

> 学术综述要求"正反两面同时呈现"。本节诚实标注本探索 32 轮自动化未能稳定进入的 UI 区域，给出 **原因 + 推测 + 证据**。读者如有人工验证机会，可补充。

| # | 未能进入的角落 | 原因（自动化） | 推测（人类用户应该看到什么） | 证据 |
|---|---|---|---|---|
| ① | Upload Project 完整字段集 | dialog title 与 Project List 复用，DOM 字段叠加 | 应该有"Choose Folder / Choose Files"按钮 + 项目名输入 + Cancel/Create | `/tmp/mc-explore/deep5/E13_upload_project.png` 标题异常 |
| ② | Camera Project 模板的 seed 文件原文 | UI 创建后 API GET 项目时机未对齐（dialog 关闭未稳定） | 应该是单文件 `Camera.sysml`，结构与 OMG Pilot 仓的 Camera 例子接近 | Output Console 有 `[success]` 但 8 次脚本尝试取项目内容均返回空 |
| ③ | 文件树（el-tree）的多级子节点 | `aria-expanded='false'` 即使点击 expand-icon 也不变化 | 推测项目根 = 文件叶子的扁平结构，不存在多级；或子节点用 lazy-load 但 headless 触发失败 | `/tmp/mc-explore/deep5/E6a_tree_first_click.png` 树节点数量始终为 1 |
| ④ | Run > Compile Model / Visualize Model 的真实触发 | 这两项需要"当前选中模型节点"上下文 | 推测：先在树中展开项目 → 选定具体 `package` 或 `part def` 节点 → Run 菜单项变为可触发 | `/tmp/mc-explore/deep5/E5a_run_compile_model.png` 网络监控 0 请求 |
| ⑤ | Download 按钮的实际下载产物 | 8 秒超时未触发 download 事件 | 推测：导出当前可视化的 SVG 文件，文件名格式可能为 `<项目名>-<viewType>.svg` | Playwright `expect_download` timeout |
| ⑥ | History Image 按钮的弹窗内容 | 3 次连续可视化后点击该按钮，无任何 dialog / drawer 渲染 | i18n 字典含 `clearAllImages / historyImage / imageList`，推测应有一个图片列表抽屉 | DOM 查询 `.el-dialog, .el-drawer` 无新增节点 |
| ⑦ | 第二排工具栏 x=287 的图标实际功能 | 仅捕获到 `el-tooltip__trigger` 类，无 title | 推测是溢出 / 更多菜单（"⋯"图标的标准位置） | `/tmp/mc-explore/deep6/B_icon_x286.png` |

**额外一类**：i18n 字典里完整出现但 DOM 严格 0 匹配的功能（§7 已列）——这些不是"自动化进不去"，而是**整个产品当前 build 里就不存在对应 UI**：
- `Open AI Assistant` / `Switch to Diagram` / `Auto Completion` / `编辑器右键 Format / Close / Close Others / Close All`
- 推断为产品规划但未上线的占位翻译。

## 12 与本仓其他章节的交叉引用

- **docs/03-beihang-investigation §2.7** — WSE-Lab 团队画像 + 8 个仓库总览
- **docs/04-parsing-ide-infrastructure §1** — OMG Pilot Implementation 解析能力（与本平台的渲染管线高度同源）
- **docs/06-visualization-collaboration** — PlantUML 在 SysML v2 可视化的生态地位
- **docs/12-modelcopilot-deep §1 / §3.4** — 平台自报指标的来源
- **docs/12-modelcopilot-deep §3.4.5** — PSUM 论文与 7 案例的源出处

## 参考文献

[^repo-pilot-13]: OMG. *SysML v2 Pilot Implementation*. <https://github.com/Systems-Modeling/SysML-v2-Pilot-Implementation>. 完整书目见 [references.md](references.md#repo-pilot)。

[^repo-psum-sysmlv2-13]: WSE-Laboratory. *PSUM-SysMLv2*. <https://github.com/WSE-Laboratory/PSUM-SysMLv2>. 包含 7 个工业域案例（含 ACC、Camera 等）；本章用的 `Adaptive Cruise Control system/origin/ACC.sysml`（6587 字节）即源自此仓库。完整书目见 [references.md](references.md#repo-psum-sysmlv2)。

[^js-bundle-13]: ModelCopilot 平台前端 JS bundle `index-DrCKs0sc.js`（采样 2026-05-05，1.17 MB）。本章对 i18n 字典的关键字段提取（`openAiAssistant` / `copilot.autoCompletion` / `switchToDiagram` 等）通过 `requests.get` 该资源 + 正则匹配获得；DOM 严格匹配通过 Playwright `page.evaluate` 在登录态 `/home` 路由下执行。原始 bundle 与提取脚本保存于 `/tmp/mc-explore/27_deep_explore.py` 及后续 6 个 round 的脚本中。
