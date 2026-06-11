---
name: flow2visio
description: Generate editable Microsoft Visio flowcharts from user requirements by writing Graphviz DOT, converting DOT to Graphviz plain output, and converting plain output to .vsdx with the bundled Graphviz2Visio.Cli tool. Use when Codex needs to create, modify, or export flow diagrams, process maps, decision trees, workflow charts, or Visio files.
---

# Flow To Visio

Create editable Visio flowcharts through this pipeline:

1. Write a Graphviz DOT file for the requested flowchart.
2. Convert DOT to Graphviz plain format with `Graphviz2Visio.Cli dot2plain`.
3. Convert plain format to `.vsdx` with `Graphviz2Visio.Cli plain2visio`.

## Tool Location

Prefer the bundled CLI at:

```text
scripts/Graphviz2Visio.Cli.exe
```

This direct `scripts/Graphviz2Visio.Cli.exe` layout is appropriate when the C# CLI was published as a single-file executable. If the CLI is published as a framework-dependent or multi-file build, place the whole published output folder under `scripts/Graphviz2Visio.Cli/` instead and keep sibling files such as `.dll`, `.deps.json`, `.runtimeconfig.json`, and native dependencies next to the `.exe`.

If the bundled CLI is absent, check whether `Graphviz2Visio.Cli` is available on `PATH`. If neither is available, ask the user to place the published CLI executable under `scripts/` or the published CLI folder under `scripts/Graphviz2Visio.Cli/`.

## Commands

Use these commands, replacing paths with the actual working files. Keep `.dot`, `.plain`, and `.vsdx` files in the `flow/` directory with the same basename:

```powershell
scripts\Graphviz2Visio.Cli.exe where-dot
scripts\Graphviz2Visio.Cli.exe dot2plain flow\diagram.dot flow\diagram.plain
scripts\Graphviz2Visio.Cli.exe plain2visio flow\diagram.plain flow\diagram.vsdx
```

Add `--visible` to `plain2visio` only when the user explicitly wants Visio to open visibly during conversion.

## Workflow

Create outputs in a `flow/` directory under the user's current project or requested output root. Use the same basename for all generated files:

```text
flow/<name>.dot
flow/<name>.plain
flow/<name>.vsdx
```

Create the `flow/` directory if it does not exist. Keep generated `.dot` and `.plain` files alongside the `.vsdx` unless the user asks for cleanup; they are useful for debugging layout issues.

Before conversion, make the DOT simple and layout-friendly:

- Use stable node IDs and quoted labels.
- Set explicit graph, node, and edge defaults so Visio does not inherit black fills:

```dot
graph [
  bgcolor="white"
];

node [
  style="filled",
  fillcolor="white",
  color="black",
  fontcolor="black"
];

edge [
  color="black",
  fontcolor="black"
];
```

- Prefer standard flowchart shapes: `oval` for start/end, `box` for process, `diamond` for decisions.
- Use `rankdir=TB` for top-to-bottom flows or `rankdir=LR` for left-to-right flows.
- Keep labels concise and line-break long text with `\n`.
- Do not use `style="filled"` without an explicit `fillcolor`.
- Avoid advanced Graphviz styling unless the Visio converter is known to preserve it.

Run `where-dot` when Graphviz availability is uncertain or when `dot2plain` fails. If Graphviz is missing, explain that Graphviz `dot` must be installed or made available to the CLI.

After conversion, verify that the expected `.vsdx` exists and report the final path plus any important warnings from the CLI.

