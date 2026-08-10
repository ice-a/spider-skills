# 逆向工程技能集（Reverse Engineering Skills Hub）

本仓库汇聚了 10 个面向 AI 编程工具的**逆向工程 skill**，覆盖 **Web/JS 逆向、Android/iOS 逆向、二进制/IDA 分析、游戏 ROM 逆向、GDB 调试**等方向。本文档说明每个 skill 的用途、如何使用，以及如何在 **Claude Code / Codex / Gemini / Cursor / 其他 AI 工具** 中配置。

> 仓库地址：`https://github.com/ice-a/spider-skills`（所有子目录均为 git submodule，指向各自上游仓库）。

> 说明：这些 skill 来自不同上游作者的开源项目，许可证各不相同（MIT / Apache-2.0 / GPL-3.0），使用前请遵守各自 LICENSE 与当地法律法规，仅用于安全研究、授权测试、教学与 CTF 等合法场景。

---

## 0. 本仓库如何使用

本仓库是**逆向工程技能的集合仓库（hub）**，所有子目录（除本 README 外）都是 **git submodule**，分别指向各自的上游仓库。它本身不直接执行逆向任务，而是作为"分发中心"——把需要的 skill 安装到你使用的 AI 工具里。

### 0.1 获取本仓库（含全部子模块）

```bash
# 方式一：克隆时直接拉取所有子模块
git clone --recurse-submodules https://github.com/ice-a/spider-skills.git
cd spider-skills

# 方式二：已克隆但子模块为空，再初始化
git submodule update --init --recursive
```

> 仅 `git clone` 而不加 `--recurse-submodules` 会得到空目录；务必执行 `--init --recursive` 填充子模块内容。

### 0.2 同步子模块到上游最新

上游 skill 会持续更新，定期同步：

```bash
git submodule sync               # 同步 .gitmodules 中的 URL 到本地配置
git submodule update --remote --recursive --merge   # 拉取各子模块远端最新并合并
```

同步后可查看状态（`HEAD` 前有空格=已与父仓库记录一致；有 `+`=本地领先；有 `-`=落后需更新）：

```bash
git submodule status
```

### 0.3 把 skill 安装到 AI 工具

本仓库只是"货架"，要使用某个 skill，需把它复制到对应 AI 工具识别的 skills 目录（详见第 3 章）。通用写法：

```bash
# 以 Claude Code 用户级为例，安装全部 skill
for d in */; do
  [ "$d" = "*/" ] && continue
  cp -r "$d" ~/.claude/skills/
done

# 或只安装其中几个
cp -r spider-king-skill        ~/.claude/skills/  # 全部安装
cp -r js-reverse-automation--skill/js-reverse-automation ~/.claude/skills/  # 仅安装部分
```

### 0.4 仓库结构

```
spider-skills/                       # 本仓库（hub）
├── README.md                        # 本说明文档
├── .gitmodules                      # 各子模块指向的上游仓库地址
├── ai-reverse-toolkit/              # submodule
├── android-reverse-engineering-skill/  # submodule
├── IDA-Skill/                       # submodule
├── js-reverse-automation--skill/    # submodule
├── jshook-skill/                    # submodule
├── re-skill/                        # submodule
├── reverse-skill/                   # submodule
├── reverse-skills/                  # submodule
├── skills/                          # submodule（GDB）
└── spider-king-skill/               # submodule
```

### 0.5 贡献 / 新增 skill

新增一个上游 skill 作为子模块：

```bash
git submodule add <上游仓库URL> <目录名>
git commit -m "add <skill-name> submodule"
```

### 0.6 注意事项

- **submodule 是独立仓库**：在本仓库内修改子模块文件后，需进入子模块目录单独 `git add/commit/push` 到其上游，再回本仓库 `git add <子模块> && git commit` 更新指针。
- **许可证分散**：每个 skill 各有 LICENSE（MIT / Apache-2.0 / GPL-3.0），使用前遵守对应条款。
- **合法用途**：仅限安全研究、授权测试、教学与 CTF；逆向未授权软件可能违法，风险自负。

---

## 1. 技能清单一览

| 目录 | 名称 | 方向 | 关键依赖 | 许可证 |
|------|------|------|----------|--------|
| `ai-reverse-toolkit/` | ai-reverse-toolkit | Web/JS 逆向（定位加密入口、补环境、AST 解混淆） | js-reverse MCP | MIT |
| `android-reverse-engineering-skill/` | android-reverse-engineering | Android APK 反编译与 API 提取（含 Kotlin 名称恢复） | jadx / Vineflower | Apache-2.0 |
| `IDA-Skill/` | IDA-Skill | IDA Pro 恶意样本/二进制分析（IDAPython） | IDA Pro + LLM | GPL-3.0 |
| `js-reverse-automation--skill/` | js-reverse-automation | JS 逆向全流程自动化（JSRPC + Flask + Burp autoDecoder） | chrome-devtools-mcp / JsRpc / Burp | - |
| `jshook-skill/` | jshook-reverse | JS 逆向一体化工具（CDP 调试、Hook、反检测、反混淆） | Node + Chrome/Edge + LLM | GPL-3.0 |
| `re-skill/` | re | 游戏 ROM 逆向与 Web 移植（NES/GB/DOS） | Python + pypng + jq | MIT |
| `reverse-skill/` | jsr-reverse | Web JS 逆向统一入口（请求链证据化、壳层恢复、验证） | js-reverse-mcp | - |
| `reverse-skills/` | reverse-skills | 二进制/移动逆向技能集（符号恢复、Frida、Unicorn、DEX/Unity/iOS dump、IDAPython） | 视具体子技能而定 | MIT |
| `skills/` | skills (gdb) | GDB 非阻塞调试（C/C++/Rust） | gdb | MIT |
| `spider-king-skill/` | spider-king | Web/JS 协议恢复（对抗式前端逆向，纯协议 Python 交付） | chrome-devtools-mcp / js-reverse-mcp / Python | MIT |

---

## 2. 各 skill 用法速览

### 2.1 ai-reverse-toolkit（Web/JS 逆向）
任务导向的 Claude Code skill 集合：
- `/find-crypto-entry x-sign` —— 定位加密入口（静态搜索 + XHR 断点）
- `/env-patch` —— 补环境方案（Proxy 监控 + webpack 提取 → `sign.js`）
- `/ast-deobfuscate` —— AST 解混淆（Babel 还原字符串/控制流）
- `rules/js-reverse.md` —— JS 逆向技术手册（6 阶段流程、混淆/算法识别表）

### 2.2 android-reverse-engineering（-skill）（Android 逆向）
反编译 APK/XAPK/JAR/AAR，提取 Retrofit/OkHttp/Ktor/Apollo 等 HTTP API。
- `/decompile path/to/app.apk` —— 全流程（依赖检查 → 反编译 → 结构分析）
- 也可直接用脚本：`fingerprint.sh`（Phase 0 指纹） / `decompile.sh` / `find-api-calls.sh` / `recover-kotlin-names.sh`

### 2.3 IDA-Skill（IDA Pro 分析）
让 AI Agent 像安全分析师一样用 IDA 静态分析样本（**仅静态分析，禁止调试/执行**）。
1. 编辑 `config.json` 设置 `idat_path`
2. 初始化：`python IDA-Skill/tools/init_analysis.py target.exe`
3. 深入分析：`python IDA-Skill/tools/exec_ida.py target.i64 --code "..."`
内置 REAI（LLM 函数重命名）、FindCrypt（加密常量识别）、mkYARA、export_check 等工具。

### 2.4 js-reverse-automation--skill（JS 逆向自动化）
结合 chrome-devtools-mcp 实现 JSRPC + Flask + Burp autoDecoder 全链路：
1. 安装 MCP：`claude mcp add chrome-devtools -- npx -y chrome-devtools-mcp@latest`
2. 把 `js-reverse-automation/` 放入 skill 目录
3. 输入目标 URL、待分析参数、示例请求，AI 自动生成 JSRPC 注入、Flask 代理、Burp 文档。

### 2.5 jshook-skill（JS 逆向一体化工具）
TypeScript 实现，需用 Node 运行：
```bash
npm install && npm run build
cp .env.example .env   # 配置 OPENAI_API_KEY / ANTHROPIC_API_KEY
```
命令示例：`collect <url>` / `search <kw>` / `deobfuscate <code>` / `browser launch` / `debugger enable` / `hook generate function encryptData` / `stealth inject`。

### 2.6 re-skill（游戏 ROM 逆向）
```bash
cp -r re-skill ~/.claude/skills/re/
/re path/to/rom.nes   # 初始化项目
/re                    # 从 REVERSE.md 继续
./re_loop.sh           # 自主循环直到所有任务完成
```
自带从零实现的反汇编器/交叉引用/资源提取/可脚本化模拟器，最终 Web 移植（HTML canvas）。

### 2.7 reverse-skill（jsr-reverse，Web JS 逆向统一入口）
以 `jsr-reverse` 为唯一入口，工作流脊：`intake → evidence → locate → recover → runtime → validation → handoff`。
接入 [js-reverse-mcp](https://github.com/zhizhuodemao/js-reverse-mcp)，把 `jsr-reverse/` 复制到技能根目录，按 `reverse-records/请求链路.md` 记录证据链。

### 2.7.1 spider-king-skill（Web 协议恢复 / 对抗式 JS 逆向）
把"依赖浏览器环境/页面上下文/挑战脚本"的 hostile web client 拆回成**可复现、可验证的本地纯协议链路**（6.0 强调：先 intake 模式、最小交付形态、Known 边界走专线、browser-free Python collector 交付）。
- 自带 `SKILL.md`（Claude Code 原生路由器）→ 复制到 skills 目录即可被自动加载
- 工具链：`scripts/check_reverse_env.py`（环境体检）、`scaffold_reverse_project.py`（Python-first 脚手架）、`crypto_fingerprint.py`、`protocol_diff.py`、`evidence_normalizer.py`（脱敏）、`validate_skill.py`（技能自检）
- 发布/大改前自测：`python scripts/check_reverse_env.py && python scripts/validate_skill.py`
- 内置 3 套可执行 Profile：`browser-hook-snippets`（贴即用 Hook）、`static-ast`（Babel 结构化还原）、`env-patch`（Node/VM 补环境）

### 2.8 reverse-skills（二进制/移动逆向技能集）
支持 40+ AI 编程工具，专为 [IDA-NO-MCP](https://github.com/P4nda0s/IDA-NO-MCP) 设计。子技能：
`rev-symbol`、`rev-struct`、`rev-frida`、`rev-unicorn-debug`、`rev-dex-dumper`、`rev-u3d-dump`、`rev-idapython`、`rev-ios-dump`。
安装：`npx skills add P4nda0s/reverse-skills`

### 2.9 skills（gdb）
GDB 非阻塞调试技能，支持任何能跑 shell 的 agent。
安装：`npx add-skill https://github.com/betab0t/skills/gdb` 或克隆到 `.cursor/skills`。

---

## 3. 在各类 AI 工具中配置

### 3.1 Claude Code
Claude Code 使用**项目级** `.claude/` 或**用户级** `~/.claude/` 目录存放 skills。

**方式 A：复制 skill 目录（适用于含 SKILL.md 的 skill）**
```bash
# 复制到用户级 skills（对所有项目生效）
cp -r ai-reverse-toolkit/skills/*        ~/.claude/skills/
cp -r ai-reverse-toolkit/rules/*         ~/.claude/rules/
cp -r android-reverse-engineering-skill/plugins/android-reverse-engineering/skills/* ~/.claude/skills/
cp -r IDA-Skill                         ~/.claude/skills/
cp -r js-reverse-automation--skill/js-reverse-automation ~/.claude/skills/
cp -r jshook-skill                      ~/.claude/skills/
cp -r re-skill                          ~/.claude/skills/re
cp -r reverse-skill/jsr-reverse         ~/.claude/skills/
cp -r reverse-skills/skills/*           ~/.claude/skills/
cp -r skills/skills/gdb                 ~/.claude/skills/
cp -r spider-king-skill                 ~/.claude/skills/

# 或复制到项目级 .claude/（仅对当前项目生效）
mkdir -p .claude/skills && cp -r <skill> .claude/skills/
```
之后在 Claude Code 中以 `/skill-name` 或自然语言触发（如 `/decompile app.apk`、`/re rom.nes`）。

**方式 B：Plugin 市场（android 技能）**
```text
/plugin marketplace add SimoneAvogadro/android-reverse-engineering-skill
/plugin install android-reverse-engineering@android-reverse-engineering-skill
```

**方式 C：直接挂仓库 URL（jshook-skill 支持）**
在 Claude Code 的 skill 配置中加入仓库 URL：`https://github.com/wuji66dde/jshook-skill`

**配置 MCP（JS 类 skill 需要）**
```bash
# js-reverse-automation / ai-reverse-toolkit
claude mcp add chrome-devtools -- npx -y chrome-devtools-mcp@latest
```

### 3.2 Codex（OpenAI）
Codex 使用 `%USERPROFILE%\.codex\skills\`（Windows）或 `~/.codex/skills/`（Linux/macOS）：
```bash
cp -r <skill> "$USERPROFILE\.codex\skills\"
```
MCP 配置：
```bash
codex mcp add chrome-devtools -- npx -y chrome-devtools-mcp@latest
```
其余用法与 Claude Code 相同（自然语言或 `/skill` 触发）。

### 3.3 Gemini CLI
Gemini CLI 的 skills 目录通常为 `~/.gemini/skills/`：
```bash
cp -r <skill> ~/.gemini/skills/
```
MCP 配置：
```bash
gemini mcp add chrome-devtools npx -y chrome-devtools-mcp@latest
```

### 3.4 Cursor
Cursor 使用项目内的 `.cursor/skills/` 目录，agent 会自动加载：
```bash
cd your-project
git clone <skill-repo> .cursor/skills   # 或 cp -r <skill> .cursor/skills/
```
（如 `skills/` 仓库示例：`git clone https://github.com/betab0t/skills.git .cursor/skills`）

### 3.5 其他 AI 工具 / 通用方法
这些 skill 本质上是带 `SKILL.md`（含 YAML frontmatter）的知识库 + 脚本，任何支持"读取 markdown 作为 system prompt / context"的 agent 都能用：
- **直接作为上下文**：把 `SKILL.md` / `rules/*.md` 内容粘贴进对话，或作为项目知识库让模型读取。
- **纯脚本类**（如 `re-skill`、`android-*` 的 `.sh`/`.ps1`）：直接在终端运行，AI 负责解释与编排。
- **已封装 CLI 类**（如 `jshook-skill`）：通过 `node dist/skill.js <cmd>` 调用，可在 agent 的工具/命令配置里注册。

---

## 4. 典型工作流组合建议

- **Web JS 登录参数逆向**：`js-reverse-automation`（JSRPC 全自动）或 `jsr-reverse`（证据化流程）+ chrome-devtools-mcp；需要反混淆时用 `ai-reverse-toolkit/ast-deobfuscate` 或 `jshook-skill/deobfuscate`。
- **Android App API 提取**：`android-reverse-engineering` 反编译 + `reverse-skills/rev-dex-dumper`（脱壳）+ `rev-frida`（动态验证）。
- **二进制/恶意样本分析**：`IDA-Skill`（静态）或 `reverse-skills/rev-idapython`（脚本参考）+ `reverse-skills/rev-symbol`（符号恢复）+ `reverse-skills/rev-unicorn-debug`（模拟执行）。
- **游戏 ROM 研究**：`re-skill` 全流程 + Web 移植。
- **本地原生程序调试**：`skills/gdb` 非阻塞追踪。
- **对抗式前端/协议恢复（sign/token/cookie/WS/GraphQL/protobuf/响应解码）**：`spider-king`（先 intake 模式 → 最小交付；chrome-devtools-mcp + js-reverse-mcp 侦察，纯 Python collector 交付）；需要补环境时联动 `jshook-skill` 或 `ai-reverse-toolkit/env-patch`。

---

## 5. 环境依赖汇总

| 需求 | 涉及 skill |
|------|-----------|
| Node.js ≥ 18 | jshook-skill、js-reverse-automation（Flask 用 Python） |
| Python 3.8+ | re-skill、IDA-Skill、js-reverse-automation、spider-king（脚本与 collector 交付） |
| IDA Pro | IDA-Skill、reverse-skills（IDA-NO-MCP） |
| Java JDK 17+ / jadx | android-reverse-engineering |
| Chrome/Edge + CDP | jshook-skill、js-reverse-automation、spider-king（chrome-devtools-mcp） |
| LLM API Key | IDA-Skill(REAI)、jshook-skill、(可选) 各 JS 类 |
| gdb | skills/gdb |

---

## 6. 许可证与免责声明

- 各 skill 许可证见各自目录的 `LICENSE` 文件（MIT / Apache-2.0 / GPL-3.0）。
- 本项目仅供**安全研究、授权渗透测试、恶意软件分析、互操作性研究、教学与 CTF** 等合法用途。
- 使用本项目逆向你未授权拥有的软件可能违反知识产权与计算机相关法规，风险由使用者自行承担。
