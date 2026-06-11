# flow2visio-skills

[中文](README.zh-CN.md)

Codex/Claude-compatible skill repository for generating editable Microsoft Visio flowcharts from Graphviz DOT.

The actual skill lives in `flow2visio/`. Install that folder, not the repository root.

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

## What It Does

The `flow2visio` skill helps an agent create Visio `.vsdx` flowcharts by:

1. Writing a Graphviz DOT file from the requested flow.
2. Converting DOT to Graphviz plain format.
3. Converting the plain file to an editable Visio document.

This skill uses the converter from [yjrqz777/graphviz2visio](https://github.com/yjrqz777/graphviz2visio). That project provides the `Graphviz2Visio.Cli.exe` command used by this skill.

The bundled CLI is expected at:

```text
flow2visio/scripts/Graphviz2Visio.Cli.exe
```

## Requirements

- Windows.
- Microsoft Visio installed and available to the converter.
- The bundled `Graphviz2Visio.Cli.exe` in `flow2visio/scripts/`.
- Graphviz `dot` available either from the CLI's embedded Graphviz bundle or from the system `PATH`.

Quick check from the repository root:

```powershell
cd flow2visio
.\scripts\Graphviz2Visio.Cli.exe where-dot
```

## Install For Codex

Codex reads skills from repository, user, admin, and system locations. A skill is a directory containing `SKILL.md` plus optional `scripts/`, `references/`, `assets/`, and `agents/`.

### User-wide install

Use this when you want `flow2visio` available in all Codex projects.

PowerShell:

```powershell
New-Item -ItemType Directory -Force "$HOME\.agents\skills" | Out-Null
Copy-Item -Recurse -Force .\flow2visio "$HOME\.agents\skills\flow2visio"
```

macOS/Linux shell:

```bash
mkdir -p "$HOME/.agents/skills"
cp -R ./flow2visio "$HOME/.agents/skills/flow2visio"
```

Note: the included `.exe` is Windows-oriented. On macOS/Linux, replace it with a compatible converter or run the skill from a Windows environment that can automate Visio.

### Repository-scoped install

Use this when only one target repository should see the skill.

From the target repository:

```powershell
New-Item -ItemType Directory -Force .agents\skills | Out-Null
Copy-Item -Recurse -Force <path-to-this-repo>\flow2visio .agents\skills\flow2visio
```

Codex scans `.agents/skills` from the current directory up to the repository root. If the skill does not appear immediately, restart Codex.

### Use In Codex

Invoke explicitly:

```text
$flow2visio Generate a Visio flowchart for ...
```

Or ask naturally, for example:

```text
Create an editable Visio workflow diagram for this process.
```

Codex can also list skills through `/skills` or by typing `$` in supported surfaces.

## Install For Claude Code

Claude Code skills use a `SKILL.md` entrypoint and can be installed as personal, project, enterprise, or plugin skills. The directory name becomes the slash command name, so this skill is invoked as `/flow2visio`.

### Personal install

Use this when you want the skill in all Claude Code projects.

PowerShell:

```powershell
New-Item -ItemType Directory -Force "$HOME\.claude\skills" | Out-Null
Copy-Item -Recurse -Force .\flow2visio "$HOME\.claude\skills\flow2visio"
```

macOS/Linux shell:

```bash
mkdir -p "$HOME/.claude/skills"
cp -R ./flow2visio "$HOME/.claude/skills/flow2visio"
```

### Project install

Use this when a project should carry the skill with its source code.

From the target project:

```powershell
New-Item -ItemType Directory -Force .claude\skills | Out-Null
Copy-Item -Recurse -Force <path-to-this-repo>\flow2visio .claude\skills\flow2visio
```

Claude Code watches existing skill directories for changes. If you create a new top-level skills directory while Claude Code is already running, restart Claude Code.

### Use In Claude Code

Invoke explicitly:

```text
/flow2visio Generate a Visio flowchart for ...
```

Or ask naturally:

```text
Generate a Visio process map for this workflow.
```

## Claude.ai Web App

Claude.ai Skills can be uploaded as ZIP files in the web app, but this repository includes a Windows `.exe` and expects local Visio automation. That makes it best suited for local agents such as Claude Code or Codex on Windows.

For Claude.ai, you may upload a ZIP of the `flow2visio/` folder as an instruction/reference skill, but the web app should not be expected to run the bundled Windows converter or control your local Visio installation.

## Other Agent-Skills-Compatible Clients

This repository follows the Agent Skills folder pattern:

```text
flow2visio/
  SKILL.md
  scripts/
  references/
  assets/
```

For other clients, copy `flow2visio/` into that client's skill directory and make sure:

- `SKILL.md` remains at the root of the installed skill folder.
- `scripts/Graphviz2Visio.Cli.exe` is present.
- The agent is running on Windows with access to Visio.
- The client allows execution of bundled scripts or external commands.

## CLI Commands

```powershell
flow2visio\scripts\Graphviz2Visio.Cli.exe where-dot
flow2visio\scripts\Graphviz2Visio.Cli.exe dot2plain flow\diagram.dot flow\diagram.plain
flow2visio\scripts\Graphviz2Visio.Cli.exe plain2visio flow\diagram.plain flow\diagram.vsdx
```

Use `--visible` with `plain2visio` only when Visio should be shown during conversion.

By default, generated files should be placed in a `flow/` directory and share the same basename:

```text
flow/diagram.dot
flow/diagram.plain
flow/diagram.vsdx
```

## Troubleshooting

If the skill is not discovered:

- Confirm you installed the `flow2visio/` folder itself, not only `SKILL.md`.
- Confirm the installed path ends with `flow2visio/SKILL.md`.
- Restart the agent if the skill was added while the agent was already running.

If conversion fails:

- Run `.\scripts\Graphviz2Visio.Cli.exe where-dot` from inside the installed `flow2visio/` folder.
- Confirm Microsoft Visio is installed.
- Confirm your agent has permission to execute `Graphviz2Visio.Cli.exe`.
- If Windows blocks a downloaded executable, run:

```powershell
Unblock-File .\flow2visio\scripts\Graphviz2Visio.Cli.exe
```

If generated Visio shapes appear with black fills:

- Update or reinstall the latest `flow2visio/` skill folder.
- Make sure generated DOT files set explicit defaults:

```dot
graph [bgcolor="white"];
node [style="filled", fillcolor="white", color="black", fontcolor="black"];
edge [color="black", fontcolor="black"];
```

- Avoid `style="filled"` without `fillcolor="white"` or another intended fill color.

## References

- [yjrqz777/graphviz2visio](https://github.com/yjrqz777/graphviz2visio)
- [OpenAI Codex Agent Skills documentation](https://developers.openai.com/codex/skills)
- [Claude Code skills documentation](https://code.claude.com/docs/en/skills)
- [Agent Skills open standard](https://agentskills.io/)
