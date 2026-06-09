# code-review-graph

Local-first code intelligence for MCP. Builds a persistent structural map of your codebase so AI agents read only what matters.

**Reference:** https://github.com/tirth8205/code-review-graph

## Philosophy

Stop burning tokens. Start reviewing smarter.

## Installation

```bash
pip install code-review-graph
code-review-graph install  # Auto-detects and configures all platforms
```

## Core Commands

```bash
# Build graph for current project
code-review-graph build

# Incremental update (changed files only)
code-review-graph update

# Review changes with blast-radius analysis
code-review-graph detect-changes --brief

# Visualize as interactive graph
code-review-graph visualize

# Start watch daemon
code-review-graph daemon start
```

## MCP Tools (30 Available)

### Essential for Agents

1. **build_or_update_graph_tool** — Build or incrementally update the graph
2. **get_impact_radius_tool** — Blast radius of changed files
3. **get_review_context_tool** — Token-optimized review context
4. **query_graph_tool** — Callers, callees, tests, imports
5. **detect_changes_tool** — Risk-scored change impact analysis
6. **get_architecture_overview_tool** — Architecture from community structure
7. **find_large_functions_tool** — Functions exceeding line threshold
8. **get_hub_nodes_tool** — Most-connected architectural hotspots
9. **get_bridge_nodes_tool** — Chokepoints via betweenness centrality
10. **get_knowledge_gaps_tool** — Untested hotspots, structural weaknesses

### Usage in Agent Tasks

```
# Before modifying a file, agent should:
1. query_graph_tool to find all callers/callees
2. get_impact_radius_tool to see affected tests
3. get_review_context_tool for minimal context

# After modifying, agent should:
4. detect_changes_tool to validate impact
5. get_affected_flows_tool to check execution paths
```

## Token Savings

```
┌─────────────────────── Token Savings ────────────────────────┐
│ Full context would be: 12,921 tokens                           │
│ Graph context used:     762 tokens                             │
│ Saved:                12,159 tokens (~94%)                   │
│ Breakdown: Functions 244 · Tests 191 · Risk 244 · Other 83   │
└───────────────────────────────────────────────────────────────┘
```

## Blast Radius Analysis

When a file changes, trace every caller, dependent, and test affected. Agent reads only these files instead of scanning the whole project.

## Multi-Repo Support

```bash
# Register multiple repos
code-review-graph register ~/project-a --alias proj-a
code-review-graph register ~/project-b

# Start daemon for all repos
code-review-graph daemon start

# Search across all repos
cross_repo_search_tool
```

## Integration with Agent Workflow

### Before Task Assignment

1. Run `code-review-graph build` to index current state
2. Use `get_architecture_overview_tool` to understand system
3. Identify `get_hub_nodes_tool` for critical files

### During Task Execution

1. Agent queries `query_graph_tool` for file dependencies
2. Gets minimal context via `get_review_context_tool`
3. Makes edits
4. Validates with `detect_changes_tool`

### Post-Task Review

1. `get_impact_radius_tool` shows what changed
2. `get_affected_flows_tool` checks execution paths
3. `get_knowledge_gaps_tool` identifies missing tests

## Language Support

Python, JavaScript/TypeScript/TSX, Go, Rust, Java, C/C++, C#, Ruby, Kotlin, Swift, PHP, Scala, Solidity, Dart, R, Perl, Lua, Elixir, Zig, PowerShell, Julia, ReScript, GDScript, Nix, Verilog, SQL, Vue/Svelte, Astro, Jupyter notebooks.

## Configuration

```bash
# Exclude paths
.code-review-graphignore:
generated/**
*.generated.ts
vendor/**
node_modules/**

# Environment variables
CRG_MAX_IMPACT_NODES=500
CRG_MAX_IMPACT_DEPTH=2
CRG_TOOL_TIMEOUT=300
```

## Links

- Full docs: https://code-review-graph.com
- MCP integration: https://github.com/tirth8205/code-review-graph/blob/main/docs/COMMANDS.md
- Benchmarks: 38x–528x token reduction, 100% recall on impact analysis
