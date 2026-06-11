# flow2visio-skills

[English](README.md)

这是一个兼容 Codex / Claude 等 Agent Skills 生态的 skill 仓库，用于从 Graphviz DOT 生成可编辑的 Microsoft Visio 流程图。

真正的 skill 位于 `flow2visio/`。安装时应该安装这个目录，而不是仓库根目录。

```text
flow2visio-skills/
  README.md
  README.zh-CN.md
  flow2visio/
    SKILL.md
    scripts/
      Graphviz2Visio.Cli.exe
    references/
    assets/
```

## 功能

`flow2visio` skill 可以帮助 agent 生成 Visio `.vsdx` 流程图：

1. 根据用户需求编写 Graphviz DOT 文件。
2. 将 DOT 转换为 Graphviz plain 格式。
3. 将 plain 文件转换为可编辑的 Visio 文档。

这个 skill 使用 [yjrqz777/graphviz2visio](https://github.com/yjrqz777/graphviz2visio) 项目提供的转换器。该项目提供本 skill 调用的 `Graphviz2Visio.Cli.exe` 命令。

内置 CLI 的预期位置：

```text
flow2visio/scripts/Graphviz2Visio.Cli.exe
```

## 运行要求

- Windows。
- 已安装 Microsoft Visio，并且转换器可以访问 Visio。
- `flow2visio/scripts/` 下存在 `Graphviz2Visio.Cli.exe`。
- Graphviz `dot` 可用，可以来自 CLI 内置 Graphviz，也可以来自系统 `PATH`。

在仓库根目录快速检查：

```powershell
cd flow2visio
.\scripts\Graphviz2Visio.Cli.exe where-dot
```

## 安装到 Codex

Codex 会从仓库级、用户级、管理员级和系统级位置读取 skills。一个 skill 是包含 `SKILL.md` 的目录，也可以包含 `scripts/`、`references/`、`assets/`、`agents/` 等可选目录。

### 用户级安装

如果希望所有 Codex 项目都能使用 `flow2visio`，使用这种方式。

PowerShell：

```powershell
New-Item -ItemType Directory -Force "$HOME\.agents\skills" | Out-Null
Copy-Item -Recurse -Force .\flow2visio "$HOME\.agents\skills\flow2visio"
```

macOS/Linux shell：

```bash
mkdir -p "$HOME/.agents/skills"
cp -R ./flow2visio "$HOME/.agents/skills/flow2visio"
```

注意：当前仓库内置的是 Windows `.exe`。如果在 macOS/Linux 上使用，需要替换为兼容的转换器，或在可以自动化 Visio 的 Windows 环境中运行。

### 仓库级安装

如果只希望某一个目标仓库使用这个 skill，使用这种方式。

在目标仓库中执行：

```powershell
New-Item -ItemType Directory -Force .agents\skills | Out-Null
Copy-Item -Recurse -Force <path-to-this-repo>\flow2visio .agents\skills\flow2visio
```

Codex 会从当前目录向上扫描 `.agents/skills`，直到仓库根目录。如果 skill 没有马上出现，重启 Codex。

### 在 Codex 中使用

显式调用：

```text
$flow2visio Generate a Visio flowchart for ...
```

或者自然语言触发，例如：

```text
Create an editable Visio workflow diagram for this process.
```

在支持的界面中，也可以通过 `/skills` 或输入 `$` 查看和选择 skills。

## 安装到 Claude Code

Claude Code skills 使用 `SKILL.md` 作为入口，可以安装为个人级、项目级、企业级或插件级 skill。目录名会成为斜杠命令名，所以这个 skill 的命令是 `/flow2visio`。

### 个人级安装

如果希望所有 Claude Code 项目都能使用这个 skill，使用这种方式。

PowerShell：

```powershell
New-Item -ItemType Directory -Force "$HOME\.claude\skills" | Out-Null
Copy-Item -Recurse -Force .\flow2visio "$HOME\.claude\skills\flow2visio"
```

macOS/Linux shell：

```bash
mkdir -p "$HOME/.claude/skills"
cp -R ./flow2visio "$HOME/.claude/skills/flow2visio"
```

### 项目级安装

如果希望某个项目随源码携带这个 skill，使用这种方式。

在目标项目中执行：

```powershell
New-Item -ItemType Directory -Force .claude\skills | Out-Null
Copy-Item -Recurse -Force <path-to-this-repo>\flow2visio .claude\skills\flow2visio
```

Claude Code 会监控已经存在的 skill 目录变化。如果是在 Claude Code 已经运行后才创建新的顶层 skills 目录，需要重启 Claude Code。

### 在 Claude Code 中使用

显式调用：

```text
/flow2visio Generate a Visio flowchart for ...
```

或者自然语言触发：

```text
Generate a Visio process map for this workflow.
```

## Claude.ai 网页版

Claude.ai 的 Skills 可以通过 ZIP 上传，但这个仓库包含 Windows `.exe`，并且依赖本地 Visio 自动化。因此它更适合在 Windows 上通过 Claude Code 或 Codex 这类本地 agent 使用。

如果用于 Claude.ai，可以把 `flow2visio/` 目录打包成 ZIP 上传，作为说明和参考类 skill 使用；但不应该期待 Claude.ai 网页版运行这个 Windows 转换器，也不能期待它控制你的本地 Visio。

## 其他兼容 Agent Skills 的客户端

这个仓库遵循 Agent Skills 的目录模式：

```text
flow2visio/
  SKILL.md
  scripts/
  references/
  assets/
```

对于其他客户端，把 `flow2visio/` 复制到对应客户端的 skill 目录，并确认：

- `SKILL.md` 位于已安装 skill 目录的根部。
- `scripts/Graphviz2Visio.Cli.exe` 存在。
- agent 运行在 Windows，并且可以访问 Visio。
- 客户端允许执行 bundled scripts 或外部命令。

## 发布 Release

本仓库包含 GitHub Actions 工作流：`.github/workflows/release.yml`。

该工作流会：

- 校验 `flow2visio/` skill 目录结构。
- 检查 `flow2visio/SKILL.md` frontmatter。
- 运行 `Graphviz2Visio.Cli.exe where-dot`。
- 将 `flow2visio/` 打包成 release ZIP。
- 生成 SHA256 校验文件。
- 将 ZIP 和校验文件发布到 GitHub Release。

通过推送版本 tag 发布：

```powershell
git tag v1.0.0
git push origin v1.0.0
```

也可以在 GitHub Actions 页面手动运行 `Release Skill` workflow，并填写类似 `v1.0.0` 的 tag。

## CLI 命令

```powershell
flow2visio\scripts\Graphviz2Visio.Cli.exe where-dot
flow2visio\scripts\Graphviz2Visio.Cli.exe dot2plain flow\diagram.dot flow\diagram.plain
flow2visio\scripts\Graphviz2Visio.Cli.exe plain2visio flow\diagram.plain flow\diagram.vsdx
```

只有在需要转换过程中显示 Visio 窗口时，才给 `plain2visio` 添加 `--visible`。

默认情况下，生成文件应放在 `flow/` 目录，并使用相同的文件名主体：

```text
flow/diagram.dot
flow/diagram.plain
flow/diagram.vsdx
```

## 排查问题

如果 skill 没有被发现：

- 确认安装的是完整的 `flow2visio/` 目录，而不是只有 `SKILL.md`。
- 确认安装后的路径以 `flow2visio/SKILL.md` 结尾。
- 如果是在 agent 运行后才添加 skill，重启 agent。

如果转换失败：

- 在已安装的 `flow2visio/` 目录中运行 `.\scripts\Graphviz2Visio.Cli.exe where-dot`。
- 确认已经安装 Microsoft Visio。
- 确认 agent 有权限执行 `Graphviz2Visio.Cli.exe`。
- 如果 Windows 拦截了下载的可执行文件，运行：

```powershell
Unblock-File .\flow2visio\scripts\Graphviz2Visio.Cli.exe
```

如果生成的 Visio 形状是黑色填充：

- 更新或重新安装最新的 `flow2visio/` skill 目录。
- 确认生成的 DOT 文件显式设置默认样式：

```dot
graph [bgcolor="white"];
node [style="filled", fillcolor="white", color="black", fontcolor="black"];
edge [color="black", fontcolor="black"];
```

- 避免只写 `style="filled"`，但没有写 `fillcolor="white"` 或其他期望的填充色。

## 参考资料

- [yjrqz777/graphviz2visio](https://github.com/yjrqz777/graphviz2visio)
- [OpenAI Codex Agent Skills 文档](https://developers.openai.com/codex/skills)
- [Claude Code skills 文档](https://code.claude.com/docs/en/skills)
- [Agent Skills 开放标准](https://agentskills.io/)
