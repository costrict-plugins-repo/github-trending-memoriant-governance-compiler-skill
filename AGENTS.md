# Memoriant Governance Compiler Skill

AI governance policy compilation and compliance evaluation skills for coding agents.

## Available Skills

### compile-policy
Transform a Markdown policy document into a typed governance DAG (nodes: sections and rules; edges: contains, requires, references). Outputs `graph.json` and `graph.dot` for Graphviz visualization.

Skill file: `skills/compile-policy/SKILL.md`

### analyze-graph
Structural analysis of a compiled governance graph: section density, rule distribution, orphaned nodes, coverage gaps, and recommendations for sparse areas.

Skill file: `skills/analyze-graph/SKILL.md`

### detect-conflicts
Scan a governance graph for policy conflicts: contradictory rules, duplicate mandates, frequency conflicts, and precedence ambiguities.

Skill file: `skills/detect-conflicts/SKILL.md`

### trace-compliance
Evaluate a described action or scenario against the governance graph. Returns a verdict (PERMITTED / PROHIBITED / CONDITIONAL / AMBIGUOUS) with a full traceable evidence path.

Skill file: `skills/trace-compliance/SKILL.md`

### generate-audit
Produce a complete audit evidence package: policy inventory, coverage matrix, conflict register, compliance trace log, and executive summary.

Skill file: `skills/generate-audit/SKILL.md`

## Available Agents

### policy-compiler-agent
Deterministic Markdown → DAG compiler agent. Parses policy documents, assigns stable node IDs, builds typed graphs, and exports JSON + DOT output.

Agent file: `agents/policy-compiler-agent.md`

### compliance-evaluator-agent
Scenario evaluation and audit evidence agent. Evaluates actions against compiled governance graphs and produces traceable compliance verdicts.

Agent file: `agents/compliance-evaluator-agent.md`

## Install

```bash
# Claude Code (primary)
/install NathanMaine/memoriant-governance-compiler-skill

# OpenAI Codex CLI
git clone https://github.com/NathanMaine/memoriant-governance-compiler-skill.git ~/.codex/skills/governance-compiler
codex --enable skills

# Google Gemini CLI
gemini extensions install https://github.com/NathanMaine/memoriant-governance-compiler-skill.git --consent
```

## Source Repository

[NathanMaine/governance-graph-compiler](https://github.com/NathanMaine/governance-graph-compiler)
