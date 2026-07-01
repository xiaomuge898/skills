---
name: reverse-analysis-898
description: Use when analyzing authorized JavaScript reverse engineering tasks, including JS parameter generation logic, request signing, encryption/decryption routines, key/iv/salt discovery, token/sign generation, obfuscated JavaScript analysis, VM/jsVMP virtual machine obfuscation, anti-debugger/debugger trap analysis, browser runtime tracing, Electron asar inspection, fingerprint browser DevTools enablement, F12/Ctrl+Shift+I shortcut blocking analysis, remote-debugging-port parameter discovery, CryptoJS/WebCrypto/Node crypto usage, Base64/URL encoding/hash/HMAC/AES/RSA/DES/SM2/SM3/SM4 identification, request replay analysis, and producing reverse analysis reports with extracted evidence attachments. Trigger when the user mentions JS reverse analysis, JavaScript encryption, sign, token, key, iv, salt, decrypt, encrypt, CryptoJS, WebCrypto, VM, jsVMP, debugger, anti-debugger, asar, Electron, fingerprint browser, DevTools, F12, Ctrl+Shift+I, remote-debugging-port, request parameters, crawler reverse engineering, anti-bot parameters, or parameter restoration.
---


# NX JS

## 基础定位

这是一个用于授权 JavaScript 逆向分析的技能。

主要目标是分析 JS 代码中的参数生成逻辑、加密解密逻辑、密钥来源、标准库调用、VM/jsVMP 虚拟机混淆、反调试逻辑、运行时数据流，并将分析结果整理成中文报告和附件文本。

也可以处理用户授权的本地 Electron / 指纹浏览器调试辅助任务，例如检查 `app.asar`、去除本地 asar 内阻断调试的 `debugger` 逻辑、关闭禁止 F12 或禁止 `Ctrl+Shift+I` 的快捷键拦截、开启 DevTools 调试窗口、定位 `remote-debugging-port` 参数来源。

只处理用户授权目标、本地靶机、自有代码、公开测试样本、用户明确提供的 JS 文件或用户明确授权的分析材料。不要上传、公开、发布、分享、私用或外传敏感文件。

如果任务涉及真实第三方目标，先确认授权范围。只做分析、复现和防御性说明，不提供未授权入侵、账号攻击、凭证窃取、绕过访问控制、批量滥用接口等用途的帮助。

## 总体原则
默认用中文输出，技术名词可以保留英文，例如 `sign`、`token`、`key`、`iv`、`salt`、`CryptoJS`、`WebCrypto`、`AES`、`RSA`。

结论必须有证据链。不要只说“可能是 AES”或“应该是 MD5”，必须说明判断依据、关键代码位置、输入输出特征和不确定点。

优先使用最小可验证路径：

1. 先定位入口。
2. 再追踪调用链。
3. 再确认参与字段。
4. 再识别编码层、哈希层、加密层。
5. 最后给出复现代码或伪代码。

完成前必须反向验证：

1. 用分析结论反推原始参数是否能生成。
2. 用样本输入对比样本输出。
3. 检查 `key`、`iv`、`mode`、`padding`、编码层是否闭环。
4. 检查是否存在运行时动态变量、接口下发变量、环境指纹变量。

## 输入材料检查

开始分析前，先整理用户提供了哪些材料。

常见材料包括：

- JS 文件
- HTML 页面
- 抓包请求
- curl 命令
- HAR 文件
- 请求 URL
- 请求方法
- 请求头
- Cookie
- Query 参数
- Body 参数
- 加密前样本
- 加密后样本
- 响应样本
- 报错信息
- 浏览器控制台日志
- Electron 安装目录
- `app.asar` 或解包后的 asar 目录
- `package.json` / `main.js` / `preload.js`
- 指纹浏览器快捷方式或启动脚本
- 进程命令行参数
- DevTools 被禁用、F12 被拦截、`Ctrl+Shift+I` 被拦截的现象描述

如果材料不足，不要直接猜结论。明确说明缺少哪一环，以及下一步需要用户补充什么。

## 任务分流

先判断当前任务属于哪一类。一个任务可以同时命中多个分支。

常见分支：

- 逆向分析 JS 主流程
- 定位参数生成入口
- 分析 `sign` / `token` / `data` / `payload`
- 查找 `key` / `iv` / `salt` / 密码
- 识别加密、哈希、编码算法
- 解密参数
- 还原请求复现逻辑
- 分析 VM / jsVMP 虚拟机混淆
- 定位和处理 `debugger` / 反调试逻辑
- 处理本地 Electron asar 调试开关
- 定位 F12 / `Ctrl+Shift+I` 快捷键拦截逻辑
- 查找 `remote-debugging-port` 参数位置
- 生成分析报告和附件文本

## 功能 1：逆向分析 JS

当用户要求“分析 JS”“找入口”“还原逻辑”“定位 sign 生成位置”“分析参数怎么来的”时，按这个流程执行。

### 分析流程

1. 搜索目标参数名，例如：
   - `sign`
   - `token`
   - `timestamp`
   - `nonce`
   - `data`
   - `payload`
   - `encrypt`
   - `signature`
2. 搜索常见请求发送入口：
   - `fetch`
   - `XMLHttpRequest`
   - `axios`
   - `$.ajax`
   - `sendBeacon`
3. 搜索常见加密和编码关键词：
   - `CryptoJS`
   - `crypto.subtle`
   - `createHash`
   - `createHmac`
   - `AES`
   - `RSA`
   - `DES`
   - `MD5`
   - `SHA`
   - `HMAC`
   - `Base64`
   - `btoa`
   - `atob`
   - `encodeURIComponent`
   - `decodeURIComponent`
   - `debugger`
   - `Function`
   - `eval`
   - `setInterval`
   - `setTimeout`
   - `constructor`
   - `vm`
   - `VM`
   - `jsVMP`
4. 找到疑似函数后，向上追踪调用方，向下追踪返回值。
5. 标出完整调用链：入口函数 -> 中间处理函数 -> 加密/哈希函数 -> 请求发送位置。
6. 判断是否存在混淆、webpack 打包、动态导入、运行时生成代码、VM/jsVMP 虚拟机解释器、反调试逻辑。

### 输出格式

```text
【入口位置】

【核心函数】

【调用链】

【关键参数】

【生成逻辑】

【证据位置】

【可复现程度】

【不确定点】
```

## 功能 2：找出 JS 密钥

当用户要求“找密钥”“找 key”“找 iv”“找 salt”“找密码”“找 AES key”时，按这个流程执行。

### 分析流程

1. 搜索硬编码字符串。
2. 搜索配置对象。
3. 搜索全局变量，例如 `window.xxx`、`globalThis.xxx`。
4. 搜索接口下发字段。
5. 搜索 `localStorage`、`sessionStorage`、`cookie`。
6. 搜索时间戳、随机数、设备指纹、用户标识是否参与派生。
7. 判断密钥是否经过混淆、拼接、反转、Base64、Hex、URL 编码。
8. 判断密钥类型：
   - 固定密钥
   - 动态密钥
   - 接口下发密钥
   - 用户态密钥
   - 派生密钥
   - 混淆后密钥

### 判断要求

不要把“看起来像字符串”的内容直接判定为密钥。必须说明证据链：

- 它在哪里出现
- 被哪个函数读取
- 作为哪个参数传入
- 是否进入加密/解密函数
- 输出是否能被样本验证

### 输出格式

```text
【疑似密钥】

【密钥来源】

【密钥类型】

【关联算法】

【是否固定】

【是否需要运行时获取】

【证据位置】

【验证方式】
```

## 功能 3：解密参数

当用户要求“解密参数”“还原 payload”“看看 data 是什么”“解开密文”时，按这个流程执行。

### 分析流程

1. 确认密文样本。
2. 确认密文外层编码：
   - URL encode
   - Base64
   - Hex
   - JSON stringify
   - gzip
   - protobuf
3. 确认算法：
   - AES
   - DES / 3DES
   - RSA
   - SM2 / SM4
   - XOR
   - 自定义算法
4. 确认 `key`。
5. 确认 `iv`。
6. 确认 `mode`，例如 CBC、ECB、GCM、CTR。
7. 确认 `padding`，例如 PKCS7、PKCS5、NoPadding。
8. 尝试还原解密流程。
9. 如果不能解密，说明缺少哪一环。

### 输出格式

```text
【密文参数】

【编码层】

【加密算法】

【mode】

【padding】

【key】

【iv】

【解密流程】

【解密结果】

【失败原因】

【下一步需要的材料】
```

## 功能 4：分析参数生成逻辑

当用户要求“这个参数怎么来的”“sign 怎么生成”“token 怎么算”“请求参数如何复现”时，按这个流程执行。

### 分析流程

1. 找参数赋值点。
2. 找参数生成函数。
3. 找参与字段。
4. 判断是否包含：
   - 时间戳
   - nonce
   - 请求路径
   - query 参数
   - body 参数
   - cookie
   - user-agent
   - 设备指纹
   - 固定盐
   - 接口返回值
5. 还原拼接顺序。
6. 还原编码方式。
7. 还原哈希、HMAC 或加密方式。
8. 给出 JS 或 Python 复现代码。

### 输出格式

```text
【参数名】

【生成函数】

【参与字段】

【拼接顺序】

【编码方式】

【加密/哈希方式】

【返回格式】

【复现代码】

【验证结果】
```

## 功能 5：识别标准库和算法

当用户要求“这是什么算法”“属于什么库”“是不是标准库”“是不是魔改算法”时，按这个流程执行。

### 常见库识别

优先检查是否使用这些库或 API：

- `CryptoJS`
- `WebCrypto API`
- `Node.js crypto`
- `JSEncrypt`
- `node-forge`
- `jsrsasign`
- `sm-crypto`
- `crypto-js`

### 常见算法特征

根据函数名、参数结构、输出长度和输入输出样本判断：

- MD5：常见 32 位 hex。
- SHA1：常见 40 位 hex。
- SHA256：常见 64 位 hex。
- HMAC：通常同时存在 `key` 和 `message`。
- AES：常见 key 长度为 16、24、32 字节，常见 mode 为 CBC、ECB、GCM、CTR。
- RSA：常见公钥或私钥格式包含 `BEGIN PUBLIC KEY`、`BEGIN RSA PRIVATE KEY` 等。
- Base64：常见字符集为 `A-Z`、`a-z`、`0-9`、`+`、`/`、`=`。
- URL 编码：常见 `%2F`、`%3D`、`%2B`、`%25`。
- SM2 / SM3 / SM4：常见于国密相关库或函数命名。

### 输出格式

```text
【疑似库】

【疑似算法】

【判断依据】

【输入结构】

【输出特征】

【是否标准算法】

【是否存在魔改】

【还需要验证的点】
```

## 功能 6：还原请求复现逻辑

当用户要求“写复现代码”“用 Python 复现”“用 JS 复现请求”“还原接口请求”时，按这个流程执行。

### 分析流程

1. 明确请求方法、URL、headers、cookies、query、body。
2. 明确动态参数列表。
3. 分离固定字段和动态字段。
4. 复现参数生成逻辑。
5. 复现编码、哈希、加密流程。
6. 构造最小请求。
7. 对比原始请求和复现请求：
   - 参数格式
   - 参数长度
   - 时间戳精度
   - nonce 格式
   - sign 输出
   - 响应状态码
   - 响应关键字段

### Python 要求

如果需要生成 Python 代码：

- 默认使用 Python 3.12。
- 使用 Python 前先创建虚拟环境。
- 不要向原始 Python 环境安装模块。
- 优先使用标准库。
- 只有明显降低复杂度或风险时才添加第三方依赖。
- 代码要写详细注释。

## 功能 7：分析 VM / jsVMP 虚拟机混淆

当用户要求“分析 VM”“分析 jsVMP”“虚拟机混淆”“解释器混淆”“opcode 还原”“字节码还原”时，按这个流程执行。

### 分析流程

1. 先判断是否真的是 VM/jsVMP：
   - 是否存在大数组、字节码数组、常量池。
   - 是否存在 `while` / `switch` / 指令分发循环。
   - 是否存在 opcode、pc、sp、stack、context、register 类变量。
   - 是否存在解释器函数、调度器函数、handler 表。
2. 定位 VM 入口：
   - 初始字节码从哪里传入。
   - 常量池从哪里生成。
   - 外部参数如何进入 VM。
   - VM 返回值如何流向 `sign`、`token`、`data`、`payload`。
3. 分析指令分发：
   - opcode 到 handler 的映射。
   - 栈操作、寄存器操作、跳转操作。
   - 函数调用、属性访问、字符串解码。
4. 提取关键路径：
   - 不需要完整还原全部 VM。
   - 优先还原目标参数相关的最短路径。
   - 标记无关分支和混淆分支。
5. 必要时建议动态插桩：
   - 打印 opcode。
   - 打印 handler 入参和返回值。
   - 打印栈变化。
   - 打印目标参数生成前后的变量。

### 输出格式

```text
【是否 VM/jsVMP】

【判断依据】

【VM 入口】

【字节码/常量池来源】

【调度器位置】

【关键 opcode / handler】

【目标参数路径】

【可还原部分】

【仍需动态验证的部分】
```

## 功能 8：定位和处理 debugger / 反调试逻辑

当用户要求“去 debugger”“分析反调试”“一直 debugger 卡住”“打开控制台就卡死”“DevTools 被检测”时，按这个流程执行。

### 分析流程

1. 搜索直接反调试关键词：
   - `debugger`
   - `setInterval`
   - `setTimeout`
   - `Function("debugger")`
   - `constructor("debugger")`
   - `eval`
   - `console.clear`
   - `devtools`
   - `isOpen`
2. 判断触发方式：
   - 定时器循环触发。
   - 函数构造器动态触发。
   - 控制台对象检测。
   - 窗口尺寸差检测。
   - 快捷键拦截。
   - 右键菜单拦截。
3. 定位副作用：
   - 是否只是暂停调试。
   - 是否会清空页面。
   - 是否会跳转页面。
   - 是否会上报风控事件。
   - 是否会影响参数生成。
4. 给出处理策略：
   - 优先定位并注释本地授权样本中的反调试代码。
   - 或者把 `debugger` 替换为空语句。
   - 或者禁用对应定时器。
   - 或者在运行时 hook 对应检测函数。
5. 处理后必须验证：
   - 页面是否正常打开。
   - 参数生成是否未被破坏。
   - DevTools 是否能正常停在业务代码。
   - 是否还有二次反调试逻辑。

### 输出格式

```text
【反调试类型】

【触发位置】

【触发方式】

【副作用】

【处理方案】

【修改点】

【验证结果】

【风险】
```

## 功能 9：处理本地 Electron asar 指纹浏览器调试开关

当用户要求“去除 asar 里的 debugger”“指纹浏览器禁止 F12”“指纹浏览器禁止 Ctrl+Shift+I”“开启指纹浏览器调试窗口”“Electron app.asar 调试”时，按这个流程执行。

### 安全边界

只处理用户本机、用户授权、用户自有或本地测试用的 Electron / 指纹浏览器客户端。不要用于篡改第三方服务、绕过付费授权、窃取账号、规避风控滥用接口。

修改前必须备份原始 `asar` 文件或原目录。

### 分析流程

1. 明确 Electron 程序位置：
   - 安装目录。
   - `resources` 目录。
   - `app.asar` 路径。
   - 是否存在 `app.asar.unpacked`。
2. 解包或查看 asar 内容：
   - 找 `package.json`。
   - 找入口文件，例如 `main.js`、`index.js`、`background.js`。
   - 找 `preload.js`、`renderer.js`、webpack bundle。
3. 搜索 DevTools 和快捷键相关关键词：
   - `openDevTools`
   - `devTools`
   - `webPreferences`
   - `BrowserWindow`
   - `globalShortcut`
   - `before-input-event`
   - `setMenu`
   - `setApplicationMenu`
   - `context-menu`
   - `F12`
   - `Control+Shift+I`
   - `CommandOrControl+Shift+I`
   - `preventDefault`
4. 搜索 debugger 和反调试关键词：
   - `debugger`
   - `Function("debugger")`
   - `constructor("debugger")`
   - `setInterval`
   - `devtools-detect`
   - `console.clear`
5. 判断禁用来源：
   - 主进程禁用 DevTools。
   - 渲染进程监听快捷键。
   - preload 脚本拦截按键。
   - 菜单被清空或禁用。
   - 打包混淆代码中做反调试。
6. 给出最小修改方案：
   - 注释或移除本地授权样本中的 `debugger` 触发。
   - 移除或绕过 `F12` / `Ctrl+Shift+I` 的 `preventDefault`。
   - 添加或恢复 `webContents.openDevTools()`。
   - 检查 `webPreferences.devTools` 是否为 `false`，必要时改为 `true`。
   - 保留业务逻辑，不要改动账号、授权、风控、网络请求逻辑。
7. 重新打包 asar 或替换解包目录。
8. 启动程序验证 DevTools 是否能打开。

### 输出格式

```text
【Electron 程序路径】

【asar 路径】

【入口文件】

【DevTools 禁用位置】

【快捷键拦截位置】

【debugger 触发位置】

【最小修改方案】

【备份文件】

【验证方式】

【风险和回滚方式】
```

## 功能 10：查找 remote-debugging-port 参数位置

当用户要求“找 remote-debugging-port”“开启远程调试端口”“找到调试端口参数在哪”“指纹浏览器 remote debugging”时，按这个流程执行。

### 分析流程

1. 先确认运行方式：
   - 桌面快捷方式。
   - 启动脚本。
   - Electron 主进程。
   - 子进程启动 Chromium。
   - 配置文件控制启动参数。
2. 搜索关键词：
   - `remote-debugging-port`
   - `remoteDebuggingPort`
   - `debuggingPort`
   - `--remote-debugging-port`
   - `appendSwitch`
   - `commandLine.appendSwitch`
   - `spawn`
   - `execFile`
   - `child_process`
   - `chromium`
   - `chrome.exe`
3. 检查参数来源：
   - 写死在代码中。
   - 来自配置文件。
   - 来自数据库。
   - 来自命令行参数。
   - 来自环境变量。
   - 启动时随机分配。
4. 判断修改方式：
   - 快捷方式追加参数。
   - 配置文件修改端口。
   - Electron `app.commandLine.appendSwitch` 添加参数。
   - 子进程启动参数中添加或修改参数。
5. 验证方式：
   - 检查进程命令行是否包含 `--remote-debugging-port=端口`。
   - 访问 `http://127.0.0.1:端口/json/version`。
   - 访问 `http://127.0.0.1:端口/json/list`。
   - 确认只绑定本机地址，避免暴露到公网。

### 输出格式

```text
【参数位置】

【参数来源】

【当前端口】

【修改方式】

【验证 URL】

【安全注意】

【回滚方式】
```

## 报告生成要求

每次完成分析后，生成一份中文报告。

报告必须包含：

1. 任务目标
2. 输入材料
3. 分析过程
4. 关键代码位置
5. 参数生成逻辑
6. 密钥、iv、salt 来源
7. 加密/解密算法判断
8. 标准库识别
9. 可复现代码或伪代码
10. VM/jsVMP 或反调试分析结果
11. Electron asar / DevTools / 快捷键拦截分析结果
12. `remote-debugging-port` 参数位置和验证方式
13. 验证过程
14. 结论
15. 风险和不确定点
16. 附件文本

## 附件文本模板

附件文本用于保存证据和可复查内容。

```text
【关键函数】
粘贴或概括关键函数。

【调用链】
入口 -> 中间函数 -> 加密函数 -> 请求发送位置

【关键参数】
参数名：
来源：
生成方式：

【疑似密钥】
key：
iv：
salt：
来源：

【算法判断】
算法：
库：
依据：

【VM/jsVMP 分析】
入口：
调度器：
关键 opcode / handler：
目标参数路径：

【debugger / 反调试】
触发位置：
触发方式：
处理方案：
验证结果：

【Electron asar 调试】
asar 路径：
入口文件：
DevTools 禁用位置：
快捷键拦截位置：
修改点：
备份文件：

【remote-debugging-port】
参数位置：
参数来源：
验证 URL：

【复现代码】
放 JS 或 Python 复现代码。

【验证记录】
输入：
输出：
对比结果：
```

## 最终回答格式

回答用户时，优先使用下面结构：

```text
大师，结论：

【解决方法】

【关键证据】

【为什么是这个答案】

【隐患和不确定点】

【验证结果】

【其他可选写法】
```

如果证据不足，明确写：

```text
当前证据不足，不能直接下结论。
缺少的材料：
下一步建议：
```

## 质量要求

所有结论必须能被复查。

所有“疑似”判断必须标注可信度。

有多种可能时，按可信度排序。

不要省略编码层。很多 JS 参数不是单纯加密，而是“JSON -> gzip -> AES -> Base64 -> URL encode”这类多层结构。

不要忽略运行时变量。很多密钥、盐、token、nonce、时间戳和设备指纹只在运行时出现。

不要把混淆变量名当作语义依据。优先看数据流、调用关系、入参、返回值和输出特征。

完成前必须反向验证结果；如果无法验证，明确说明无法验证的原因。
