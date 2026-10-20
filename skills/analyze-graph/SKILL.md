# analyze-graph

Analyze a compiled governance graph to surface structural insights: rule density, section depth, orphaned nodes, and coverage gaps.

## Trigger

User says something like:
- `/analyze-graph`
- "analyze my governance graph"
- "what does my policy graph look like?"
- "find gaps in my governance document"

## What This Skill Does

Reads a compiled `graph.json` file (produced by `compile-policy`) and generates a structured analysis report covering:
- Section hierarchy depth and breadth
- Rule density per section
- Orphaned nodes (sections with no rules)
- Leaf sections with only one rule (potential under-specification)
- Cross-reference edges and referenced sections
- Coverage suggestions for sparse areas

## Step-by-Step Instructions

### Step 1: Load the Graph

Ask the user for the graph file location. Default: `governance-graph/graph.json`

Read the JSON file. Validate it has `nodes` and `edges` arrays. If not, report: "This file does not appear to be a valid governance graph. Run `/compile-policy` first."

### Step 2: Build In-Memory Structures

Parse into:
- `sections`: map of section-id → {label, level, children: [], rules: []}
- `rules`: map of rule-id → {label, parent}
- `adjacency`: map of node-id → list of neighbor node-ids

### Step 3: Compute Metrics

For each section, compute:
- `rule_count`: number of direct rule children
- `child_section_count`: number of direct subsection children
- `depth`: level in hierarchy (from the `level` field)
- `total_descendants`: recursive count of all rules under this section tree

Global metrics:
- `max_depth`: deepest level in the document
- `total_sections`: count of section nodes
- `total_rules`: count of rule nodes
- `avg_rules_per_section`: total_rules / total_sections
- `orphaned_sections`: sections with rule_count == 0 AND child_section_count == 0

### Step 4: Generate the Analysis Report

```
Governance Graph Analysis
═══════════════════════════════════════════
Graph:          governance-graph/graph.json
Total Nodes:    <N> (<S> sections, <R> rules)
Total Edges:    <N>
Hierarchy Depth: <N> levels

Section Summary:
──────────────────────────────────────────────────────
Section                          │ Level │ Rules │ Children
<name>                           │   1   │  <N>  │   <N>
  <subsection>                   │   2   │  <N>  │   <N>
──────────────────────────────────────────────────────

Avg rules per section: <N>

Findings:
  [SPARSE]   Sections with 0-1 rules: <list names>
  [ORPHANED] Sections with no children or rules: <list names>
  [DEEP]     Sections at level 4+: <list names>
  [DENSE]    Sections with 10+ rules: <list names>

Recommendations:
  - Consider breaking down <dense section> — 12 rules may indicate mixed concerns
  - <orphaned section> has no rules or subsections — add content or remove
```

### Step 5: Cross-Reference Analysis

Identify `"references"` edges in the graph. For each:
- Report the source rule, target section, and whether the target section exists in the graph
- Flag broken references (target does not exist as a node)

### Step 6: Coverage Gap Detection

For standard governance documents, check if these common sections are present (by keyword matching in labels):
- Access control
- Data handling / data protection
- Audit / logging
- Incident response / failure modes
- Credentials / authentication

If a common area is missing, suggest: "Consider adding a section on <topic>."

## Output

Print the full analysis report to the terminal. Optionally write to `governance-graph/analysis.md` if the user requests.
