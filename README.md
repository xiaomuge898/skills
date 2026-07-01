# Skills

个人 Codex Skills 仓库，用来沉淀可复用的工作流、领域规则和分析模板。

当前仓库主要收录面向授权 JavaScript 逆向分析的 Skill，适合在 Codex 中处理 JS 参数生成逻辑、加解密算法识别、密钥来源追踪、VM/jsVMP 混淆分析、Electron asar 调试辅助和分析报告生成等任务。

## 仓库内容

```text
skills/
├── README.md
├── LICENSE
└── skills/
    └── reverse-analysis-898/
        └── SKILL.md
```

### 结构说明

- `README.md`：仓库说明文档，介绍 Skill 的用途、安装方式、使用方式和维护规范。
- `LICENSE`：开源许可证，目前为 MIT License。
- `skills/`：Skill 存放目录。每个子目录代表一个独立 Skill。
- `skills/reverse-analysis-898/SKILL.md`：`reverse-analysis-898` 的核心说明文件，包含触发条件、分析流程、输出模板和安全边界。

## 当前 Skill

### reverse-analysis-898

显示标题：`NX JS`

用途：用于授权 JavaScript 逆向分析和本地调试辅助。

主要能力：

- 分析 JS 参数生成逻辑，例如 `sign`、`token`、`data`、`payload`。
- 定位请求参数生成入口、核心函数和调用链。
- 查找 `key`、`iv`、`salt`、密码、固定盐、动态密钥和派生密钥。
- 识别常见编码、哈希和加密算法，例如 Base64、URL encode、MD5、SHA、HMAC、AES、RSA、DES、SM2、SM3、SM4。
- 分析 `CryptoJS`、`WebCrypto API`、`Node.js crypto`、`JSEncrypt`、`node-forge`、`jsrsasign`、`sm-crypto` 等库的使用方式。
- 处理 VM / jsVMP 虚拟机混淆分析，包括 opcode、handler、调度器、常量池和目标参数路径。
- 定位和处理 `debugger`、反调试、DevTools 检测、快捷键拦截等逻辑。
- 辅助分析本地授权 Electron / 指纹浏览器的 `app.asar` 调试开关。
- 定位 F12、`Ctrl+Shift+I`、菜单、preload、主进程中的 DevTools 禁用逻辑。
- 查找 `remote-debugging-port` 参数来源和验证方式。
- 生成中文分析报告和附件文本，方便复查证据链。

## 安装方式

### 方式一：复制到 Codex Skills 目录

将需要使用的 Skill 目录复制到 Codex 的个人 Skills 目录。

Windows 常见路径：

```powershell
$source = "D:\personal\github\skills\skills\reverse-analysis-898"
$target = "$env:USERPROFILE\.codex\skills\reverse-analysis-898"

Copy-Item -Recurse -Force $source $target
```

复制完成后的结构应类似：

```text
C:\Users\<用户名>\.codex\skills\reverse-analysis-898\
└── SKILL.md
```

### 方式二：按仓库结构管理

如果只是维护和编辑 Skill，可以保留当前仓库结构：

```text
D:\personal\github\skills\skills\reverse-analysis-898\SKILL.md
```

编辑完成后，再同步到 Codex 的个人 Skills 目录。

## 使用方式

在 Codex 中提出和 Skill 描述匹配的任务时，Skill 会根据 `SKILL.md` 中的 `description` 触发。

示例请求：

```text
分析这段 JS 的 sign 参数怎么生成。
```

```text
帮我找这个 CryptoJS AES 的 key 和 iv。
```

```text
这个 data 参数能不能解密，帮我分析编码层和加密算法。
```

```text
这个 JS 像 jsVMP，帮我找 VM 入口和 opcode 分发逻辑。
```

```text
这个 Electron 指纹浏览器禁止 F12，帮我分析 app.asar 里哪里拦截了 Ctrl+Shift+I。
```

```text
帮我找 remote-debugging-port 参数是在哪里设置的。
```

## 安全边界

本仓库 Skill 只用于以下场景：

- 用户授权目标。
- 本地靶机。
- 自有代码。
- 公开测试样本。
- 用户明确提供并授权分析的文件。
- 本机授权的 Electron / 指纹浏览器客户端调试。

不要用于：

- 未授权入侵。
- 账号攻击。
- 凭证窃取。
- 绕过访问控制。
- 绕过付费授权。
- 批量滥用接口。
- 上传、公开、发布、分享或外传敏感文件。

涉及真实第三方目标时，应先确认授权范围，再进行分析。

## 编写和维护规范

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

维护建议：

- `name` 使用小写英文、数字和连字符。
- `description` 写清楚触发场景和关键词，这是 Codex 判断是否加载 Skill 的核心字段。
- 正文可以用中文写，但要结构清晰，优先使用流程、清单和输出模板。
- 一个 Skill 可以包含多个功能，但应通过“任务分流”或“功能模块”组织。
- 不要把大量无关说明塞进 `SKILL.md`，长资料应拆到 `references/`，脚本应放到 `scripts/`。
- 结论类 Skill 应要求证据链、验证方式和不确定点。
- 修改前建议备份，修改后建议重新读取检查 frontmatter 和关键章节。

## 反向验证清单

更新 Skill 或 README 后，建议检查：

- `SKILL.md` 是否存在。
- YAML frontmatter 是否只有必要字段。
- `name` 是否和目录名一致。
- `description` 是否覆盖核心触发关键词。
- 中文正文是否能被 UTF-8 正确读取。
- README 中的目录结构是否和实际仓库一致。
- 安全边界是否写清楚。

## 许可证

本仓库使用 MIT License。详见 [LICENSE](LICENSE)。
