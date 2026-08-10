# 逆向工程技能集（Reverse Engineering Skills Hub）

汇聚 **10 个**面向 AI 编程工具的逆向工程 skill，覆盖 **Web/JS 逆向、Android/iOS 逆向、二进制/IDA 分析、游戏 ROM 逆向、GDB 调试**。所有子目录均为 git submodule，指向各自上游仓库。

> 仓库：`https://github.com/ice-a/spider-skills` ｜ 许可证：各 skill 自有 LICENSE（MIT / Apache-2.0 / GPL-3.0），仅限安全研究、授权测试、教学与 CTF 等合法用途。

---

## 一、本仓库使用方法

**1. 获取（含全部子模块）**
```bash
git clone --recurse-submodules https://github.com/ice-a/spider-skills.git
# 已克隆但子模块为空时：
git submodule update --init --recursive
```

**2. 同步到上游最新**
```bash
git submodule sync
git submodule update --remote --recursive --merge
git submodule status   # 空格=已同步  + =本地领先  - =落后
```

**3. 安装 skill 到 AI 工具**（本仓库只是货架，需复制到工具识别的 skills 目录，见第三节）

**4. 仓库结构**
```
spider-skills/
├── README.md / .gitmodules
├── ai-reverse-toolkit/          ├── android-reverse-engineering-skill/
├── IDA-Skill/                   ├── js-reverse-automation--skill/
├── jshook-skill/                ├── re-skill/
├── reverse-skill/               ├── reverse-skills/
├── skills/   (gdb)              └── spider-king-skill/
```

**5. 新增 skill**：`git submodule add <上游URL> <目录名>` → commit。
**注意**：子模块是独立仓库，修改后需分别在子模块与父仓库提交。

---

## 二、技能清单

| 目录 | 名称 | 方向 | 依赖 | 许可证 |
|------|------|------|------|--------|
| `ai-reverse-toolkit/` | ai-reverse-toolkit | Web/JS 逆向（加密入口/补环境/AST 解混淆） | js-reverse MCP | MIT |
| `android-reverse-engineering-skill/` | android-reverse-engineering | Android APK 反编译与 API 提取 | jadx / Vineflower | Apache-2.0 |
| `IDA-Skill/` | IDA-Skill | IDA Pro 静态分析（IDAPython） | IDA Pro + LLM | GPL-3.0 |
| `js-reverse-automation--skill/` | js-reverse-automation | JS 逆向自动化（JSRPC+Flask+Burp） | chrome-devtools-mcp / JsRpc / Burp | - |
| `jshook-skill/` | jshook-reverse | JS 逆向（CDP/Hook/反检测/反混淆） | Node + Chrome + LLM | GPL-3.0 |
| `re-skill/` | re | 游戏 ROM 逆向与 Web 移植 | Python + pypng + jq | MIT |
| `reverse-skill/` | jsr-reverse | Web JS 逆向统一入口（证据化流程） | js-reverse-mcp | - |
| `reverse-skills/` | reverse-skills | 二进制/移动逆向集（Frida/Unicorn/DEX/Unity/iOS） | IDA-NO-MCP | MIT |
| `skills/` | skills (gdb) | GDB 非阻塞调试 | gdb | MIT |
| `spider-king-skill/` | spider-king | Web/JS 协议恢复（对抗式 JS 逆向，Python 交付） | chrome-devtools-mcp / js-reverse-mcp | MIT |

---

## 三、在 AI 工具中配置

通用规则：把目标 skill 目录复制到工具的 skills 目录即可（含 `SKILL.md` 的会被自动加载，用 `/skill-name` 或自然语言触发）。

| 工具 | skills 目录 | MCP 配置 |
|------|-------------|----------|
| **Claude Code** | `~/.claude/skills/`（用户级）或 `.claude/skills/`（项目级） | `claude mcp add chrome-devtools -- npx -y chrome-devtools-mcp@latest` |
| **Codex** | `~/.codex/skills/`（Win：`%USERPROFILE%\.codex\skills\`） | `codex mcp add chrome-devtools -- npx -y chrome-devtools-mcp@latest` |
| **Gemini CLI** | `~/.gemini/skills/` | `gemini mcp add chrome-devtools npx -y chrome-devtools-mcp@latest` |
| **Cursor** | `.cursor/skills/` | — |

**安装示例（Claude Code 用户级）**
```bash
cp -r spider-king-skill                ~/.claude/skills/
cp -r js-reverse-automation--skill/js-reverse-automation ~/.claude/skills/
cp -r reverse-skill/jsr-reverse        ~/.claude/skills/
# 其余同理；ai-reverse-toolkit 还需 cp -r ai-reverse-toolkit/rules/* ~/.claude/rules/
```

**其他工具**：任何能读 markdown 的 agent，直接把 `SKILL.md` 作为 context；纯脚本类在终端运行；CLI 类（jshook-skill）用 `node dist/skill.js <cmd>`。

---

## 四、环境依赖

| 依赖 | 涉及 skill |
|------|-----------|
| Node.js ≥ 18 | jshook-skill、js-reverse-automation（Flask 用 Python） |
| Python 3.8+ | re-skill、IDA-Skill、js-reverse-automation、spider-king |
| IDA Pro | IDA-Skill、reverse-skills（IDA-NO-MCP） |
| Java JDK 17+ / jadx | android-reverse-engineering |
| Chrome/Edge + CDP | jshook-skill、js-reverse-automation、spider-king |
| gdb | skills/gdb |
