<p align="center">
  <img src="https://img.shields.io/badge/claude--code-plugin-8A2BE2" alt="Claude Code Plugin" />
  <img src="https://img.shields.io/badge/skills-5-blue" alt="5 Skills" />
  <img src="https://img.shields.io/badge/agents-2-green" alt="2 Agents" />
  <img src="https://img.shields.io/badge/license-MIT-green" alt="MIT License" />
</p>

# Memoriant Governance Compiler Skill

A Claude Code plugin that transforms natural-language policy documents into executable governance graphs. Compile Markdown policies into typed DAGs, detect policy conflicts, trace compliance paths for specific scenarios, and generate audit evidence packages.

**No servers. No Docker. Just install and use.**

## Install

```bash
/install NathanMaine/memoriant-governance-compiler-skill
```

## Cross-Platform Support

This plugin works with multiple AI coding assistants:

### Claude Code (Primary)
```bash
/install NathanMaine/memoriant-governance-compiler-skill
```

### OpenAI Codex CLI
```bash
git clone https://github.com/NathanMaine/memoriant-governance-compiler-skill.git ~/.codex/skills/governance-compiler
codex --enable skills
```

### Gemini CLI
```bash
gemini extensions install https://github.com/NathanMaine/memoriant-governance-compiler-skill.git --consent
```

## Skills

| Skill | Command | What It Does |
|-------|---------|-------------|
| **Compile Policy** | `/compile-policy` | Parse Markdown policy → typed DAG (JSON + DOT format) |
| **Analyze Graph** | `/analyze-graph` | Structural analysis: density, depth, orphaned nodes, coverage gaps |
| **Detect Conflicts** | `/detect-conflicts` | Find contradictory rules, duplicate mandates, frequency conflicts |
| **Trace Compliance** | `/trace-compliance` | Evaluate a scenario — PERMITTED / PROHIBITED / CONDITIONAL / AMBIGUOUS |
| **Generate Audit** | `/generate-audit` | Full audit evidence package: inventory, coverage matrix, conflict register |

## Agents

| Agent | Best Model | Specialty |
|-------|-----------|-----------|
| **Policy Compiler** | Opus 4.6 + extended thinking | Deterministic Markdown → DAG compilation |
| **Compliance Evaluator** | Opus 4.6 + extended thinking | Scenario evaluation, evidence-backed verdicts |

## Quick Start

```bash
# Compile a policy document
/compile-policy

# Find conflicts in the compiled graph
/detect-conflicts

# Evaluate a specific action
/trace-compliance "a service account reads production data without logging"

# Generate a compliance audit package
/generate-audit
```

## Example: Policy Compilation

**Input:** Markdown policy document with sections and bullet rules

**Output:**
```json
{
  "nodes": [
    {"id": "section-1", "label": "Data Handling", "type": "section", "level": 1},
    {"id": "rule-1", "label": "Encrypt data in transit", "type": "rule", "parent": "section-1"}
  ],
  "edges": [
    {"from": "section-1", "to": "rule-1", "label": "contains"}
  ]
}
```

Visualize the DOT output at [GraphvizOnline](https://dreampuf.github.io/GraphvizOnline/).

## Example: Compliance Trace

```
Scenario: "Agent spawns a sub-agent to execute a financial transaction"

Verdict: CONDITIONAL

Rules applied:
  [REQUIRES] rule-34: "Level 3: Irreversible actions require human supervisor approval"
  [REQUIRES] rule-4:  "Preserve audit trail for all decisions"
  [PERMITS]  rule-20: "Orchestrator agent holds delegation authority"

Conditions for PERMITTED:
  1. Human supervisor approves the transaction
  2. Audit trail entry is created
```

## Use Cases

- AI agent governance: compile agent constitutions into enforceable graphs
- Compliance workflows: trace CMMC, HIPAA, or SOC 2 control applicability
- Policy management: detect conflicting rules before deployment
- Audit preparation: generate structured evidence for compliance reviews
- Multi-agent coordination: define and enforce agent behavior policies

## Source Repository

Built from [NathanMaine/governance-graph-compiler](https://github.com/NathanMaine/governance-graph-compiler).

## License

MIT — see [LICENSE](LICENSE) for details.
