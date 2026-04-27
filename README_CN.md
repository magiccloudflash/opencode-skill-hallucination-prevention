# OpenCode 技能：反AI幻觉 (Hallucination Prevention)

> 一个 OpenCode Agent 技能，通过强制验证、不确定性确认和基于证据的回应，防止 AI 产生幻觉。

[English](./README.md) | 中文

## 功能特性

- **先验证再陈述** — 读取文件、运行代码、查阅文档后再做判断
- **承认不确定性** — 明确区分推测和已验证的事实
- **不捏造** — 绝不杜撰错误信息、API 方法、测试结果或修复方案
- **基于证据** — 引用文件路径、行号和实际输出
- **响应自查清单** — 10 点回答前自我审计
- **代码生成护栏** — 写代码前后的双重验证
- **工具级反幻觉规则** — 针对每种工具的具体防幻觉要点
- **8 类幻觉分类** — 每种幻觉类型有专门的预防方案

## 快速安装

### 方式一：克隆仓库（推荐）

```bash
git clone https://github.com/magiccloudflash/opencode-skill-hallucination-prevention.git
```

然后复制 skill 到 OpenCode 全局配置：

```bash
# macOS / Linux
cp -r hallucination-prevention ~/.config/opencode/skills/

# Windows PowerShell
Copy-Item -Recurse hallucination-prevention "$env:USERPROFILE\.config\opencode\skills\"
```

### 方式二：手动安装

```bash
# macOS / Linux
mkdir -p ~/.config/opencode/skills/hallucination-prevention
cp hallucination-prevention/SKILL.md ~/.config/opencode/skills/hallucination-prevention/

# Windows PowerShell
mkdir -p "$env:USERPROFILE\.config\opencode\skills\hallucination-prevention"
Copy-Item hallucination-prevention/SKILL.md "$env:USERPROFILE\.config\opencode\skills\hallucination-prevention\"
```

### 方式三：全局规则（每次会话自动加载，强烈推荐）

用仓库提供的 `AGENTS.md` 替换你的全局规则文件：

```bash
# macOS / Linux
cp AGENTS.md ~/.config/opencode/AGENTS.md

# Windows PowerShell
Copy-Item AGENTS.md "$env:USERPROFILE\.config\opencode\AGENTS.md"
```

> **推荐**：全局 AGENTS.md 会在每次会话中自动生效，无需手动调用 skill。

## 使用方法

### On-Demand 加载（方式一、二）

Skill 安装后会被 OpenCode 自动发现。Agent 在任务相关时会自动调用：

```javascript
skill({ name: "hallucination-prevention" })
```

### 自动加载（方式三）

如果使用 `AGENTS.md` 配置，规则会在每次会话启动时自动注入上下文，**无需任何手动操作**。

## 权限配置

在 `opencode.json` 中控制 skill 访问权限：

```json
{
  "permission": {
    "skill": {
      "hallucination-prevention": "allow"
    }
  }
}
```

按 Agent 类型覆盖权限：

```json
{
  "agent": {
    "plan": {
      "permission": {
        "skill": {
          "hallucination-prevention": "allow"
        }
      }
    },
    "general": {
      "permission": {
        "skill": {
          "hallucination-prevention": "allow"
        }
      }
    }
  }
}
```

权限模式支持通配符：

| 权限 | 行为 |
|------|------|
| `allow` | 立即加载 skill |
| `deny` | 对 Agent 隐藏 skill，拒绝访问 |
| `ask` | 每次加载前提示用户确认 |

## 仓库结构

```
opencode-skill-hallucination-prevention/
├── README.md                                 # 英文文档
├── README_CN.md                              # 中文文档（本文件）
├── LICENSE                                   # MIT 许可证
├── opencode.json                             # 权限配置示例
├── AGENTS.md                                 # 全局规则（自动加载版）
└── hallucination-prevention/
    └── SKILL.md                              # Skill 定义文件
```

## Skill v2.0 内容详解

### 8 类幻觉分类体系

| 类型 | 说明 | 示例 | 预防方法 |
|------|------|------|----------|
| **A: 事实性** | 陈述未经核实的事实 | "这个函数返回 Promise"（未读源码） | 先读源码，再运行代码 |
| **B: 结构性** | 捏造不存在的文件/路径 | "处理器在 src/routes/auth.ts"（文件不存在） | 用 glob/ls 确认目录结构 |
| **C: 行为性** | 假设代码行为 | "Bug 已经修复了"（未运行项目） | 执行代码并捕获实际输出 |
| **D: 外部性** | 捏造 API/库函数 | "Lodash 有 groupByAndSort() 函数"（不存在） | 查文档、源码或类型定义 |
| **E: 结果性** | 想象测试结果 | "所有测试通过"（未运行） | 运行测试，展示实际输出 |
| **F: 时序性** | 错误的版本/日期 | "Node v24 新增了这个功能"（v24 不存在） | 检查当前版本和更新日志 |
| **G: 权威性** | 假装已执行了验证 | "我已确认修改正确"（未重读文件） | 实际执行验证并展示证据 |
| **H: 引用性** | 捏造 URL/ID/commit | "参见 https://github.com/foo/issues/99999"（假链接） | 验证 URL 或注明"未核实" |

### 工具级反幻觉规则

| 工具 | 核心规则 |
|------|---------|
| **read** | 不猜文件内容；读取前确认文件存在；编辑后重读 |
| **write** | 写入后立即重读验证；未被要求时不创建文档 |
| **edit** | 编辑前必须读取原文件；编辑后重读确认；匹配缩进风格 |
| **bash** | 不幻想命令输出；修复后重新运行验证；禁运作前询问 |
| **grep** | 引用函数/类前先 grep 确认存在 |
| **glob** | 引用路径前先用 glob 发现实际结构 |
| **question** | 指令模糊时先询问再操作，提供具体选项 |

### 10 点响应自查清单

回答前逐条检查：

| # | 检查项 | 不满足时的操作 |
|---|--------|---------------|
| 1 | 我确实读取了正在讨论的文件吗？ | 现在去读 |
| 2 | 我确实运行了我正在报告输出的命令吗？ | 现在去运行 |
| 3 | 我确实检查了我引用的包/API 吗？ | 现在检查或标注不确定 |
| 4 | 我确定这个错误信息是真实的吗？ | 展示实际输出 |
| 5 | 编辑文件后我重新读过了吗？ | 现在重读 |
| 6 | 我在猜测目录结构吗？ | 列出目录 |
| 7 | 我在猜测函数签名吗？ | 阅读源码 |
| 8 | 我在未测试的情况下声称修复有效吗？ | 运行代码 |
| 9 | 我在编造命令参数吗？ | 查看 --help |
| 10 | 我的回答可能包含虚构的输出吗？ | 仅保留真实输出 |

### 上下文锚定协议

1. **重大文件操作前**：重新读相关源文件
2. **多次工具调用后**（5 次以上）：重读已修改文件，确认累积状态
3. **回到之前的任务时**：重读所有相关文件，不依赖记忆

### 语言/框架特定规则

- **JS/TS**：检查 package.json 再 import；检查 tsconfig.json 再声称配置
- **Python**：检查 requirements.txt/pyproject.toml 再 import；验证 python --version
- **Rust**：检查 Cargo.toml；验证 rust-toolchain.toml
- **Go**：检查 go.mod；验证 go version
- **Shell**：脚本前确认可用命令；一行命令先测试

### Git 特定规则

- 禁止伪造 commit hash — 用 `git log` 查看真实的
- 禁止假设 commit 信息 — 用 `git log --oneline` 查看
- 禁止捏造 PR 编号或 Issue ID
- Commit 后运行 `git status` 和 `git log -1` 验证
- 声称分支存在前运行 `git branch -a`

### 确定性级别标注

- **已验证**：我直接读取/运行/检查过了
- **很可能**：有强证据但未直接验证
- **推测性**：基于类似模式推断，需要验证
- **未知**：无法从现有信息判断

默认应达到"已验证"级别。

### 升级协议（何时停下来问用户）

以下情况立即停止并询问用户：
1. 指令含糊，有多种理解方式
2. 所需工具/库不可用
3. 操作需要提升权限
4. 观察到的情况与用户描述矛盾
5. 任务需要你无法验证的知识
6. 操作可能导致数据丢失或安全问题
7. 上下文变化太多，难以准确追踪

## 许可证

MIT — 详见 [LICENSE](LICENSE) 文件

## 贡献

欢迎提交 Issues 和 Pull Requests：

https://github.com/magiccloudflash/opencode-skill-hallucination-prevention

---

**预览**：[English Version](./README.md) | **仓库模板**：点击 [Use this template](https://github.com/magiccloudflash/opencode-skill-hallucination-prevention) 创建你自己的副本
