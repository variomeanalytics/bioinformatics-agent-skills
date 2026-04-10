# Example outputs

Here's what the MCP tools actually return when Claude calls them. You can also test with `curl` before installing.

## `get_graph_stats`

```bash
curl -sS -X POST https://skillgraph.pipette.bio/mcp \
  -H "content-type: application/json" \
  -H "accept: application/json, text/event-stream" \
  -d '{"jsonrpc":"2.0","id":1,"method":"tools/call","params":{"name":"get_graph_stats","arguments":{}}}'
```

Returns:
```markdown
# SkillGraph Statistics

## Overview
- Total skills: 78
- Total transitions: 483
- Literature-backed edges: 254
- Type-inferred edges: 229
- Ground truth edges: 127
- Total tools across skills: 301
- Papers referenced in edges: 11,337

## Domain breakdown
- Variant analysis: 12
- Drug discovery: 11
- Single-cell / scRNA-seq: 9
- Database queries: 15
- ...
```

## `find_path`

```bash
curl -sS -X POST https://skillgraph.pipette.bio/mcp \
  -H "content-type: application/json" \
  -H "accept: application/json, text/event-stream" \
  -d '{"jsonrpc":"2.0","id":1,"method":"tools/call","params":{"name":"find_path","arguments":{"from_skill":"wgs-alignment","to_skill":"pathway-enrichment"}}}'
```

Returns:
```markdown
# Shortest path: Wgs Alignment → Pathway Enrichment

3 nodes, 2 edges

### Step 1: Wgs Alignment (wgs-alignment)
Align FASTQ reads to a reference genome using BWA-MEM2 and produce
sorted, duplicate-marked BAM files ready for variant calling.

⬇️ via BAM — 288 papers, literature+type

### Step 2: Chipseq (chipseq)
Perform ChIP-seq analysis: from FASTQ to peaks, annotations, motifs,
and visualizations.

⬇️ via GENE_LIST — 62 papers, literature+type ✓

### Step 3: Pathway Enrichment (pathway-enrichment)
```

## `get_transitions`

```bash
curl -sS -X POST https://skillgraph.pipette.bio/mcp \
  -H "content-type: application/json" \
  -H "accept: application/json, text/event-stream" \
  -d '{"jsonrpc":"2.0","id":1,"method":"tools/call","params":{"name":"get_transitions","arguments":{"skill_id":"variant-calling","direction":"downstream"}}}'
```

Returns:
```markdown
# Transitions for Variant Calling (variant-calling)

## Downstream (15) — skills that this feeds into
- Variant Annotation `variant-annotation` ← BCF, VCF — 260 papers,
  literature+type ✓ ground truth
  Annotate VCF files with functional consequences, population frequencies,
  and clinical significance using SnpEff.
- Somatic Variants `somatic-variants` ← BAM, VCF — 125 papers,
  literature+type ✓ ground truth
- Gwas Analysis `gwas-analysis` ← VCF — 89 papers, literature+type
- ...
```

## `search_skills`

```bash
curl -sS -X POST https://skillgraph.pipette.bio/mcp \
  -H "content-type: application/json" \
  -H "accept: application/json, text/event-stream" \
  -d '{"jsonrpc":"2.0","id":1,"method":"tools/call","params":{"name":"search_skills","arguments":{"query":"crispr"}}}'
```

Returns the top 20 matches by keyword relevance, with overview and tool list for each.

## `get_skill`

Returns the full SKILL.md documentation (5-20 KB of structured markdown) for a single skill. Includes triggers, required tools, input formats, workflow steps, key thresholds, critical rules, and output artifacts. This is what agents use to actually execute the workflow.

## Full JSON-RPC payload

All tools are called via standard MCP JSON-RPC 2.0. The endpoint accepts POST with:

```json
{
  "jsonrpc": "2.0",
  "id": <number>,
  "method": "tools/call",
  "params": {
    "name": "<tool_name>",
    "arguments": { ... }
  }
}
```

See the full [MCP specification](https://modelcontextprotocol.io) for protocol details.
