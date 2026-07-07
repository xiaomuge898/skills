# Codex Skills

个人 Codex Skills 仓库，用来沉淀可复用的工作流、领域分析方法、项目理解模板和输出规范。

本仓库的核心目标不是存放普通脚本，而是为 Codex 提供可触发、可复查、可维护的 `SKILL.md` 能力包。当前主要包含四类能力：

- 授权 JavaScript 逆向分析与请求复现辅助。
- 项目结构理解、架构梳理和项目记忆文档生成。
- 未知密文、编码文本、Token 和不透明参数的自动分析与解码推测。
- AI 辅助开发的 Vibe Coding 工程流程、代码规范、调试规范、测试规范、Git 规范和专项逆向工程规范。

## 整理范围

本 README 基于当前仓库主体文件整理，已按要求排除以下目录及其内容：

- `docs/`
- `skill_logs/`

因此，下面的项目结构、Skill 清单、安装说明和维护规范只覆盖可安装的 Skill 主体和仓库根文件。

## 项目结构图

```text
skills/
├── README.md
├── LICENSE
└── skills/
    ├── cipher-auto-analyzer-898/
    │   └── SKILL.md
    ├── project-memory-map-898/
    │   └── SKILL.md
    ├── reverse-analysis-898/
    │   ├── SKILL.md
    │   └── rules/
    │       ├── field-notes.md
    │       ├── parameter-crypto.md
    │       ├── replay-runtime.md
    │       ├── report-contract.md
    │       ├── reproduction-modes.md
    │       ├── scope-and-evidence.md
    │       ├── vm-antidebug-electron.md
    │       └── workflow-routing.md
    └── vibe-coding-standard-898/
        ├── SKILL.md
        ├── agents/
        │   └── openai.yaml
        ├── rules/
        │   ├── ai-coding-standard.md
        │   ├── browser-env-standard.md
        │   ├── code-standard.md
        │   ├── debug-standard.md
        │   ├── git-standard.md
        │   ├── js-reverse-engineering-standard.md
        │   └── testing-standard.md
        └── templates/
            ├── bug-report.md
            ├── code-review.md
            ├── project-plan.md
            └── task-analysis.md
```

### 结构说明

| 路径 | 作用 |
|---|---|
| `README.md` | 仓库说明文档，介绍项目定位、安装方式、使用方式、维护规范和验证清单。 |
| `LICENSE` | MIT License。 |
| `skills/` | 可安装 Skill 的根目录，每个子目录代表一个独立 Skill。 |
| `skills/cipher-auto-analyzer-898/SKILL.md` | 密文/编码文本自动分析与解码推测 Skill。 |
| `skills/project-memory-map-898/SKILL.md` | 项目理解与项目记忆图谱 Skill。 |
| `skills/reverse-analysis-898/SKILL.md` | 授权 JavaScript 逆向分析 Skill。 |
| `skills/reverse-analysis-898/rules/` | 授权逆向分析的范围证据、工作流路由、复现模式、参数加密、请求复现、VM/反调试/Electron 和报告契约规则。 |
| `skills/vibe-coding-standard-898/SKILL.md` | AI 辅助开发 Vibe Coding 工程规范 Skill。 |
| `skills/vibe-coding-standard-898/rules/` | 代码、AI Coding、Debug、测试、Git、JS 逆向和补环境专项规则。 |
| `skills/vibe-coding-standard-898/templates/` | 任务分析、代码审查、Bug 分析和项目规划模板。 |

## 当前 Skill

| Skill | 目录 | 适用场景 |
|---|---|---|
| `cipher-auto-analyzer-898` | `skills/cipher-auto-analyzer-898/` | 未知密文、编码文本、Token、不透明参数、Base64/Hex/URL/JWT/XOR 类数据的识别、解码尝试、置信度评分和可复现报告。 |
| `reverse-analysis-898` | `skills/reverse-analysis-898/` | 授权 JS 逆向、协议逆向、前端加密、签名参数、token、密钥来源、VM/jsVMP、反调试、Electron asar、remote-debugging-port、请求复现。 |
| `project-memory-map-898` | `skills/project-memory-map-898/` | 分析项目目录，生成项目结构图、模块图、依赖图、启动流程、数据流、API 流、爬虫/逆向流程图、项目记忆文档或机器可读项目地图。 |
| `vibe-coding-standard-898` | `skills/vibe-coding-standard-898/` | AI 辅助开发、vibe coding、功能开发、重构、调试、测试、代码审查、架构设计、Python 3.12、Git 流程和授权 JS 逆向任务的统一工程规范。 |

## Skill 详情

### cipher-auto-analyzer-898

显示标题：`Cipher Auto Analyzer 898`

用途：面向未知密文、编码文本、Token、不透明请求参数和多层编码样本的离线分析，帮助 Codex 先识别结构特征，再生成候选假设、尝试可逆解码、评分并归档完整过程。

主要能力：

- 识别 Base64、URL-safe Base64、Hex、URL encode、JWT、JSON、QueryString、Unicode 转义、简单 XOR、gzip/zlib 等常见外层结构。
- 对每条解码路径记录成功/失败、证据、输出预览、拒绝原因和置信度。
- 区分编码、压缩、哈希和加密，避免把 Base64 当成加密，避免把 hash 说成可解密。
- 使用最大深度、最大尝试次数和输出预览长度限制，避免无限递归和盲目爆破。
- 输出最佳猜测、验证依据、缺失材料、风险、替代方案和可复现步骤。

适合这样提问：

```text
分析这个 token 可能是什么编码或加密方式，并尝试还原可读内容。
```

```text
这个字符串像 Base64/JWT/Hex 吗？帮我逐层分析并给出报告。
```

```text
这个接口参数 data 是一串乱码，先离线判断可能的编码层和下一步需要什么材料。
```

### reverse-analysis-898

显示标题：`NX JS Reverse Analysis`

用途：面向授权 JavaScript 逆向分析、协议逆向、前端加密分析、参数生成还原和本地调试辅助。

主要能力：

- 分析 `sign`、`token`、`X-Bogus`、`data`、`payload` 等请求参数生成逻辑。
- 定位请求入口、参数赋值点、生成函数、核心调用链和返回值流向。
- 查找 `key`、`iv`、`salt`、固定盐、动态密钥、接口下发密钥和派生密钥。
- 识别 Base64、URL encode、hash、HMAC、AES、RSA、DES、SM2、SM3、SM4 等编码、哈希和加密特征。
- 分析 `CryptoJS`、`WebCrypto`、`node:crypto` 等常见加密库或 API。
- 处理 VM/jsVMP 混淆、opcode、handler、调度器、常量池和目标参数路径。
- 定位 `debugger`、反调试、DevTools 检测、快捷键拦截和相关副作用。
- 辅助分析本地授权 Electron / 指纹浏览器的 `app.asar`、F12、`Ctrl+Shift+I`、DevTools 开关和 `remote-debugging-port` 来源。
- 先选择逆向复现模式：站内执行扣代码、本地 Node 接口扣代码、Python/JavaScript/其它语言纯算移植。
- `SKILL.md` 保留核心契约和路由，详细规则拆分到 `rules/`，按任务只读取需要的规范，降低上下文占用。
- 拆分后的 `rules/` 保留原有功能和契约：授权范围、Raw Disclosure Contract、最小工作流、报告格式和最终验证清单不私自收窄。
- `SKILL.md` 和规则正文使用英文流程和约束，以提升 Codex 触发后的稳定执行；实际回答语言跟随用户输入。
- 输出分析结论、证据链、复现思路、风险、不确定点和替代方案。

适合这样提问：

```text
分析这段 JS 的 sign 参数怎么生成。
```

```text
帮我找这个 CryptoJS AES 的 key 和 iv。
```

```text
这个 JS 像 jsVMP，帮我找 VM 入口和 opcode 分发逻辑。
```

```text
这个 Electron 客户端禁止 F12，帮我分析 app.asar 里哪里拦截了 Ctrl+Shift+I。
```

### project-memory-map-898

用途：在授权范围内分析项目目录，生成后续 Codex 会话可复用的项目记忆和结构化工程地图。

主要能力：

- 从 README、清单文件、入口文件、配置文件和核心模块识别项目定位。
- 输出项目结构图、入口与启动流程、核心模块、模块依赖关系和运行流程。
- 梳理数据流、外部依赖、配置项、敏感项处理方式和未知风险。
- 对爬虫、逆向、签名、加密、浏览器自动化项目补充专门的逆向流程图。
- 明确区分已验证事实、静态推断和未知项，避免把猜测写成结论。
- 支持输出 Markdown 项目记忆文档，也可以扩展为机器可读的 `project-memory.json`。

适合这样提问：

```text
分析这个项目目录，生成 PROJECT_MEMORY.md。
```

```text
根据当前仓库生成项目结构图、模块图和启动流程。
```

```text
帮我梳理这个爬虫项目的参数生成、请求复现和数据流。
```

### vibe-coding-standard-898

显示标题：`Vibe Coding Standard 898`

用途：面向 AI 辅助开发和 vibe coding 场景，为 Codex 提供统一的软件工程执行标准，要求先分析需求、再设计方案、拆解任务、编码、测试、Review 和优化，避免直接生成大量未经分析的代码。

主要能力：

- 定义高级软件工程师 + 架构师角色定位，要求以正确需求、可维护设计、小步验证和明确取舍为目标。
- 固化需求分析 → 方案设计 → 任务拆解 → 编码 → 测试 → Review → 优化的强制工作流。
- 要求复杂任务先设计架构、文件结构、数据流、错误处理和验证方案。
- 提供代码规范：Python 3.12+、类型提示、高阶语法、异步 IO、单一职责、函数长度控制、异常处理、日志、命名和可维护性。
- 提供 AI Coding 规范：需求理解、任务拆解、代码生成、自我检查、代码 Review、潜在 Bug / 性能 / 安全问题发现。
- 提供 Debug 规范：禁止看到报错直接改代码，必须先分析 traceback、定位根因、给出修复方案并验证结果。
- 提供测试规范：正常输入、异常输入、边界输入和核心失败路径必须有测试或验证方案。
- 提供 Git 规范：分支命名、Commit 格式、提交信息和 PR 描述要求。
- 提供 JS 逆向工程专项规范：接口分析、抓包分析、参数定位、加密算法分析、自动化复现、混淆处理、AST 分析和 jsVMP 分析。
- 提供补环境规则：优先分析 `window`、`document`、`navigator`、`location`、`cookie`、`storage`、`canvas`、`crypto`、`WebSocket` 等浏览器依赖。
- 提供任务分析、代码审查、Bug 分析和项目规划模板。

适合这样提问：

```text
用 vibe-coding-standard-898 规范来分析并实现这个功能。
```

```text
按照统一 Vibe Coding 工程流程，先做需求分析和方案设计，再修改代码。
```

```text
使用这个 Skill 对当前改动做代码审查，重点检查测试、风险、性能和安全问题。
```

## 安装方式

### 安装单个 Skill

将目标 Skill 目录复制到 Codex 个人 Skills 目录。

Windows 示例：

```powershell
$source = "D:\personal\github\skills\skills\reverse-analysis-898"
$target = "$env:USERPROFILE\.codex\skills\reverse-analysis-898"

Copy-Item -Recurse -Force $source $target
```

安装完成后的结构：

```text
C:\Users\<用户名>\.codex\skills\reverse-analysis-898\
├── SKILL.md
└── rules/
```

### 安装全部 Skill

如果要同步整个仓库内的可安装 Skill，可以复制 `skills/` 下的每个子目录：

```powershell
$repoSkills = "D:\personal\github\skills\skills"
$codexSkills = "$env:USERPROFILE\.codex\skills"

Get-ChildItem -LiteralPath $repoSkills -Directory |
    ForEach-Object {
        Copy-Item -Recurse -Force $_.FullName (Join-Path $codexSkills $_.Name)
    }
```

### 仅作为开发仓库维护

如果只是编辑和维护 Skill，可以保留当前仓库结构：

```text
D:\personal\github\skills\skills\<skill-name>\SKILL.md
```

编辑完成后，再同步到 Codex 个人 Skills 目录。

## 使用方式

Codex 会根据每个 `SKILL.md` frontmatter 中的 `description` 判断是否加载对应 Skill。使用时不需要手动导入代码，只需要提出与 Skill 描述匹配的任务。

触发建议：

- 任务关键词尽量明确，例如 `sign`、`token`、`key`、`iv`、`jsVMP`、`project memory`、`项目结构图`、`vibe coding`、`代码规范`、`debug`、`code review`。
- 输入材料尽量完整，例如 JS、HTML、HAR、curl、项目目录、报错、样本输入输出。
- 如果涉及真实第三方目标，先明确授权范围和允许的验证方式。
- 如果希望生成文件，明确输出路径和文件名。

## 维护规范

每个 Skill 至少包含：

```text
skill-name/
└── SKILL.md
```

`SKILL.md` 必须包含 YAML frontmatter：

```markdown
---
name: skill-name
description: Use when ...
---
```

建议规则：

- `name` 与目录名保持一致。
- `name` 使用小写英文、数字和连字符。
- `description` 写清触发场景、关键词和边界，这是 Codex 判断是否加载 Skill 的核心字段。
- 正文优先使用英文流程、清单、表格和输出模板，减少含糊描述并提高跨会话稳定性。
- 长资料拆到 Skill 内部的 `references/`，脚本放到 `scripts/`，不要把无关长文塞进 `SKILL.md`。
- 结论类 Skill 必须要求证据链、验证方式、不确定点、风险和替代方案。
- 修改后重新读取 `SKILL.md`，检查 frontmatter、标题、触发词、输出契约和安全边界是否一致。

## 反向验证清单

更新 Skill 或 README 后，建议执行以下检查：

```powershell
rg --files -g '!docs/**' -g '!skill_logs/**'
```

确认 README 只覆盖主体文件：

```text
README.md
LICENSE
skills/cipher-auto-analyzer-898/SKILL.md
skills/project-memory-map-898/SKILL.md
skills/reverse-analysis-898/SKILL.md
skills/reverse-analysis-898/rules/scope-and-evidence.md
skills/reverse-analysis-898/rules/workflow-routing.md
skills/reverse-analysis-898/rules/reproduction-modes.md
skills/reverse-analysis-898/rules/field-notes.md
skills/reverse-analysis-898/rules/parameter-crypto.md
skills/reverse-analysis-898/rules/replay-runtime.md
skills/reverse-analysis-898/rules/vm-antidebug-electron.md
skills/reverse-analysis-898/rules/report-contract.md
skills/vibe-coding-standard-898/SKILL.md
skills/vibe-coding-standard-898/agents/openai.yaml
skills/vibe-coding-standard-898/rules/code-standard.md
skills/vibe-coding-standard-898/rules/ai-coding-standard.md
skills/vibe-coding-standard-898/rules/debug-standard.md
skills/vibe-coding-standard-898/rules/testing-standard.md
skills/vibe-coding-standard-898/rules/git-standard.md
skills/vibe-coding-standard-898/rules/js-reverse-engineering-standard.md
skills/vibe-coding-standard-898/rules/browser-env-standard.md
skills/vibe-coding-standard-898/templates/task-analysis.md
skills/vibe-coding-standard-898/templates/code-review.md
skills/vibe-coding-standard-898/templates/bug-report.md
skills/vibe-coding-standard-898/templates/project-plan.md
```

检查所有 Skill 是否存在 `SKILL.md`：

```powershell
Get-ChildItem -LiteralPath ".\skills" -Directory |
    ForEach-Object {
        $skill = Join-Path $_.FullName "SKILL.md"
        [PSCustomObject]@{
            Directory = $_.Name
            Exists    = Test-Path -LiteralPath $skill
        }
    }
```

检查 frontmatter 关键字段：

```powershell
Get-ChildItem -LiteralPath ".\skills" -Directory |
    ForEach-Object {
        $skill = Join-Path $_.FullName "SKILL.md"
        if (Test-Path -LiteralPath $skill) {
            $raw = Get-Content -LiteralPath $skill -Encoding UTF8 -Raw
            [PSCustomObject]@{
                Directory      = $_.Name
                HasName        = $raw -match "(?m)^name:\s*.+$"
                HasDescription = $raw -match "(?m)^description:\s*.+$"
            }
        }
    }
```

人工检查：

- README 的项目结构是否与排除 `docs/`、`skill_logs/` 后的实际结构一致。
- Skill 名称是否与目录名一致。
- `description` 是否覆盖核心触发关键词。
- Markdown 内容是否以 UTF-8 正确读取，英文正文是否不存在乱码或残留的大段中文说明。
- 是否写清安全边界、风险、不确定点和替代方案。
- 是否遵循各 Skill 自己的输出契约：`reverse-analysis-898` 原样展示授权逆向结果，其它要求脱敏的 Skill 按各自规则处理。

## 安全边界

本仓库 Skill 只用于以下场景：

- 用户授权目标。
- 本地靶机。
- 自有代码。
- 公开测试样本。
- 用户明确提供并授权分析的文件。
- 本机授权的 Electron / 浏览器客户端调试。
- 项目目录的本地结构分析和文档生成。

不要用于：

- 未授权入侵。
- 账号攻击。
- 凭证窃取。
- 绕过访问控制。
- 绕过付费授权。
- 批量滥用接口。
- 上传、公开、发布、分享或外传敏感文件。

涉及真实第三方目标时，应先确认授权范围，再进行分析和验证。

## 风险与替代方案

风险：

- Skill 的触发依赖 `description`，描述过窄会漏触发，描述过宽会误触发。
- `SKILL.md` 内容过长会增加读取成本，内容过短又会缺少执行约束。
- `reverse-analysis-898` 涉及授权逆向材料时，按该 Skill 约定原样展示逆向结果；安全边界由授权范围和禁止外传敏感文件控制。
- 项目记忆类 Skill 如果把推断写成事实，后续会误导实现和排查。

替代方案：

- 简单规则可以写入项目 `AGENTS.md`，适合当前项目专用约束。
- 长篇资料可以放入 Skill 的 `references/`，由 `SKILL.md` 按需路由读取。
- 通用自动化能力可以独立做成 MCP server 或脚本，而不是塞进 Skill 正文。
- 多人协作时可以把 Skill 拆成更小的主题目录，降低单个 Skill 的维护成本。

## 许可证

本仓库使用 MIT License。详见 [LICENSE](LICENSE)。
