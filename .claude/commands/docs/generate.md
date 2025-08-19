---
allowed-tools: [Read, TodoWrite, Write, Edit, Glob, Grep, Task]
description: "Generate comprehensive technical documentation using multi-agent orchestration"
---

# /docs:generate - Multi-Agent Documentation Coordinator

## Usage
```
/docs:generate [project-path] [--docs-location path] [--format markdown|html] [--diagrams]
```

## Arguments
- `project-path` - Path to the source project directory (required)
- `--docs-location` - Where to place final documentation (default: {project-path}/docs)
- `--format` - Output format (default: markdown)
- `--diagrams` - Include PlantUML diagrams (default: auto-detect need)

## FIRST ACTION (MANDATORY)

**IMPORTANT:** follow the steps in ./.claude/workflows/docs-generation.md
