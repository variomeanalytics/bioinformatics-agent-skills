# Bioinformatics Agent Skills

By [**Pipette.bio**](https://pipette.bio) — AI tooling for molecular biologists.

**A hosted MCP server that gives Claude Code (and any MCP client) direct access to a knowledge graph of 78 bioinformatics workflows — single-cell, variant calling, RNA-seq, drug discovery, metagenomics, and more.**

Query pipelines mid-conversation. No install. No data download. Just point your AI coding agent at a URL.

🔗 **Live graph explorer:** [skillgraph.pipette.bio](https://skillgraph.pipette.bio)
🔗 **Main site:** [pipette.bio](https://pipette.bio)

## Quick start

### 1. Add the MCP server to Claude Code

```bash
claude mcp add --transport http --scope user skillgraph https://skillgraph.pipette.bio/mcp
```

Or edit `~/.claude.json` manually:

```json
{
  "mcpServers": {
    "skillgraph": {
      "type": "http",
      "url": "https://skillgraph.pipette.bio/mcp"
    }
  }
}
```

### 2. Restart Claude Code

MCP servers only load at startup. Fully exit and reopen your session.

### 3. Verify it's connected

```bash
claude mcp list
```

You should see:
```
skillgraph: https://skillgraph.pipette.bio/mcp (HTTP) - ✓ Connected
```

### 4. Ask Claude anything

```
> What tools does the scanpy skill use?
> Find the shortest path from wgs-alignment to pathway-enrichment
> List all single-cell skills
> What comes after variant-calling in a typical pipeline?
> Search for CRISPR-related skills
```

Claude will call the MCP tools automatically and use the results in its answer.

## What's in the graph

- **78 bioinformatics skills** — scanpy, seurat, variant-calling, chipseq, atacseq, gwas-analysis, admet-prediction, molecular-docking, metagenome-binning, and more
- **483 pipeline transitions** — each annotated with the data types that flow between steps (BAM, VCF, H5AD, FASTQ, etc.), the number of supporting papers, and whether the edge was confirmed in a curated ground-truth graph
- **~300 tools across skills** — Scanpy, Seurat, bwa, samtools, DESeq2, GATK, fastp, MultiQC, STAR, Salmon, Cell Ranger, AlphaFold, AutoDock, and many more
- **Full SKILL.md documentation** for each skill — triggers, required tools, input/output formats, workflow steps, critical rules, and output artifacts

## Tools exposed by the MCP server

| Tool | What it does |
|---|---|
| `get_skill` | Returns full SKILL.md documentation for a skill, plus overview, tools, I/O, and connections |
| `list_skills` | Lists all available skills, optionally filtered by domain keyword |
| `search_skills` | Keyword search across skill IDs, trigger keywords, and tool names |
| `get_transitions` | Upstream and/or downstream skills with edge evidence (paper counts, data types) |
| `find_path` | Shortest pipeline path between two skills, annotated with data flow |
| `get_graph_stats` | Summary statistics: total skills, edges, domain breakdown |

## Example prompts

**Explore a specific skill:**
```
What does the atacseq skill do? What tools does it need?
```

**Build a multi-step workflow:**
```
I have raw FASTQ files from a WGS experiment. What's the sequence of skills
I should run to get a list of pathway enrichments?
```

**Understand the graph:**
```
How many single-cell skills are in the graph? Give me a breakdown by domain.
```

**Find related work:**
```
Show me skills that produce VCF files as output. Which ones are most commonly
used downstream of variant-calling?
```

## How it works

```
Claude Code
  │ HTTPS POST
  ▼
skillgraph.pipette.bio/mcp
  │ (CloudFront)
  ▼
API Gateway → AWS Lambda → in-memory graph + skill docs
```

Everything runs on our infrastructure. Your Claude Code client just speaks MCP over HTTPS. No install, no data download, no local dependencies.

## About the data

The SkillGraph knowledge graph was built from open-access bioinformatics papers indexed in PubMed Central. Tool mentions were extracted with a fine-tuned PubMedBERT NER model, pipeline transitions were inferred from document-level tool ordering, and data-type compatibility was validated against the EDAM ontology. The full interactive graph explorer is at [skillgraph.pipette.bio](https://skillgraph.pipette.bio).

## About us

Built by [Variome Analytics / Pipette.bio](https://pipette.bio). We make AI tooling accessible to molecular biologists.

## Issues and feedback

Found a bug? Want a new tool? Have a skill we should add to the graph? [Open an issue](https://github.com/variomeanalytics/bioinformatics-agent-skills/issues).

## License

MIT — see [LICENSE](LICENSE).
