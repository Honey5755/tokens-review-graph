# tokens-review-graph

**Stop burning tokens. Start reviewing smarter.**

AI coding tools re-read your entire codebase on every task. `tokens-review-graph` fixes that. It builds a structural map of your code with [Tree-sitter](https://tree-sitter.github.io/tree-sitter/), tracks changes incrementally, and gives your AI assistant precise context via [MCP](https://modelcontextprotocol.io/) so it reads only what matters.

---

## Quick Start

```bash
pip install tokens-review-graph
tokens-review-graph install          # auto-detects and configures all supported platforms
tokens-review-graph build            # parse your codebase
```

One command sets up everything. `install` detects which AI coding tools you have, writes the correct MCP configuration for each one, and injects graph-aware instructions into your platform rules. Restart your editor/tool after installing.

To target a specific platform:

```bash
tokens-review-graph install --platform codex
tokens-review-graph install --platform cursor
tokens-review-graph install --platform claude-code
```

Requires Python 3.10+. For the best experience, install [uv](https://docs.astral.sh/uv/).

Then open your project and ask your AI assistant:

```
Build the code review graph for this project
```

The initial build takes ~10 seconds for a 500-file project. After that, the graph updates automatically on every file edit and git commit.

---

## How It Works

Your repository is parsed into an AST with Tree-sitter, stored as a graph of nodes (functions, classes, imports) and edges (calls, inheritance, test coverage), then queried at review time to compute the minimal set of files your AI assistant needs to read.

### Blast-radius analysis

When a file changes, the graph traces every caller, dependent, and test that could be affected. This is the "blast radius" of the change. Your AI reads only these files instead of scanning the whole project.

### Incremental updates in < 2 seconds

On every git commit or file save, a hook fires. The graph diffs changed files, finds their dependents via SHA-256 hash checks, and re-parses only what changed. A 2,900-file project re-indexes in under 2 seconds.

### The monorepo problem, solved

Large monorepos are where token waste is most painful. The graph cuts through the noise — dramatically reducing the number of files read for review.

### 23 languages + Jupyter notebooks

Full Tree-sitter grammar support for functions, classes, imports, call sites, inheritance, and test detection in every language. Includes Zig, PowerShell, Julia, and Svelte SFC support. Plus Jupyter/Databricks notebook parsing (`.ipynb`) with multi-language cell support (Python, R, SQL), and Perl XS files (`.xs`).

---

## Features

| Feature | Details |
|---------|---------|
| **Incremental updates** | Re-parses only changed files. Subsequent updates complete in under 2 seconds. |
| **23 languages + notebooks** | Python, TypeScript/TSX, JavaScript, Vue, Svelte, Go, Rust, Java, Scala, C#, Ruby, Kotlin, Swift, PHP, Solidity, C/C++, Dart, R, Perl, Lua, Zig, PowerShell, Julia, Jupyter/Databricks (.ipynb) |
| **Blast-radius analysis** | Shows exactly which functions, classes, and files are affected by any change |
| **Auto-update hooks** | Graph updates on every file edit and git commit without manual intervention |
| **Semantic search** | Optional vector embeddings via sentence-transformers, Google Gemini, MiniMax, or any OpenAI-compatible endpoint |
| **Interactive visualisation** | D3.js force-directed graph with search, community legend toggles, and degree-scaled nodes |
| **Hub & bridge detection** | Find most-connected nodes and architectural chokepoints via betweenness centrality |
| **Surprise scoring** | Detect unexpected coupling: cross-community, cross-language, peripheral-to-hub edges |
| **Knowledge gap analysis** | Identify isolated nodes, untested hotspots, thin communities, and structural weaknesses |
| **Suggested questions** | Auto-generated review questions from graph analysis (bridges, hubs, surprises) |
| **Edge confidence** | Three-tier confidence scoring (EXTRACTED/INFERRED/AMBIGUOUS) with float scores on edges |
| **Graph traversal** | Free-form BFS/DFS exploration from any node with configurable depth and token budget |
| **Export formats** | GraphML (Gephi/yEd), Neo4j Cypher, Obsidian vault with wikilinks, SVG static graph |
| **Graph diff** | Compare graph snapshots over time: new/removed nodes, edges, community changes |
| **Community detection** | Cluster related code via Leiden algorithm with resolution scaling for large graphs |
| **Architecture overview** | Auto-generated architecture map with coupling warnings |
| **Risk-scored reviews** | `detect_changes` maps diffs to affected functions, flows, and test gaps |
| **Refactoring tools** | Rename preview, framework-aware dead code detection, community-driven suggestions |
| **Wiki generation** | Auto-generate markdown wiki from community structure |
| **Multi-repo registry** | Register multiple repos, search across all of them |
| **MCP prompts** | 5 workflow templates: review, architecture, debug, onboard, pre-merge |
| **Full-text search** | FTS5-powered hybrid search combining keyword and vector similarity |
| **Local storage** | SQLite file in `.code-review-graph/`. No external database, no cloud dependency. |
| **Watch mode** | Continuous graph updates as you work |

---

## Usage

### Slash Commands

| Command | Description |
|---------|-------------|
| `/code-review-graph:build-graph` | Build or rebuild the code graph |
| `/code-review-graph:review-delta` | Review changes since last commit |
| `/code-review-graph:review-pr` | Full PR review with blast-radius analysis |

### CLI Reference

```bash
tokens-review-graph install          # Auto-detect and configure all platforms
tokens-review-graph install --platform <name>  # Target a specific platform
tokens-review-graph build            # Parse entire codebase
tokens-review-graph update           # Incremental update (changed files only)
tokens-review-graph status           # Graph statistics
tokens-review-graph watch            # Auto-update on file changes
tokens-review-graph visualize        # Generate interactive HTML graph
tokens-review-graph visualize --format graphml   # Export as GraphML
tokens-review-graph visualize --format svg       # Export as SVG
tokens-review-graph visualize --format obsidian  # Export as Obsidian vault
tokens-review-graph visualize --format cypher    # Export as Neo4j Cypher
tokens-review-graph wiki             # Generate markdown wiki from communities
tokens-review-graph detect-changes   # Risk-scored change impact analysis
tokens-review-graph register <path>  # Register repo in multi-repo registry
tokens-review-graph unregister <id>  # Remove repo from registry
tokens-review-graph repos            # List registered repositories
tokens-review-graph eval             # Run evaluation benchmarks
tokens-review-graph serve            # Start MCP server
```

### MCP Tools

Your AI assistant uses 28 tools automatically once the graph is built, including:
- `build_or_update_graph_tool` — Build or incrementally update the graph
- `get_minimal_context_tool` — Ultra-compact context (~100 tokens)
- `get_impact_radius_tool` — Blast radius of changed files
- `get_review_context_tool` — Token-optimised review context
- `query_graph_tool` — Callers, callees, tests, imports, inheritance queries
- `traverse_graph_tool` — BFS/DFS traversal with token budget
- `semantic_search_nodes_tool` — Search code entities by name or meaning
- And 21 more specialized analysis tools...

---

## Configuration

To exclude paths from indexing, create a `.code-review-graphignore` file in your repository root:

```
generated/**
*.generated.ts
vendor/**
node_modules/**
```

Note: in git repos, only tracked files are indexed (`git ls-files`), so gitignored files are skipped automatically.

### Optional Dependencies

```bash
pip install tokens-review-graph[embeddings]          # Local vector embeddings
pip install tokens-review-graph[google-embeddings]   # Google Gemini embeddings
pip install tokens-review-graph[communities]         # Community detection
pip install tokens-review-graph[eval]                # Evaluation benchmarks
pip install tokens-review-graph[wiki]                # Wiki generation with LLM
pip install tokens-review-graph[all]                 # All optional dependencies
```

### Environment Variables

| Variable | Description | Default |
|----------|-------------|---------|
| `CRG_GIT_TIMEOUT` | Timeout in seconds for Git operations | `30` |
| `CRG_EMBEDDING_MODEL` | Default model for vector embeddings | `all-MiniLM-L6-v2` |
| `CRG_MAX_IMPACT_NODES` | Maximum nodes to include in impact analysis | `500` |
| `CRG_MAX_IMPACT_DEPTH` | Search depth for blast-radius analysis | `2` |
| `CRG_MAX_BFS_DEPTH` | Maximum depth for graph traversal | `15` |
| `GOOGLE_API_KEY` | API key for Google Gemini embeddings | - |
| `MINIMAX_API_KEY` | API key for MiniMax embeddings | - |
| `CRG_OPENAI_BASE_URL` | OpenAI-compatible embeddings endpoint | - |
| `CRG_OPENAI_API_KEY` | API key for OpenAI-compatible embeddings | - |
| `CRG_OPENAI_MODEL` | Model name for OpenAI-compatible embeddings | - |
| `NO_COLOR` | If set, disables ANSI colors in terminal | - |
| `CRG_SERIAL_PARSE` | If `1`, disables parallel parsing | - |

---

## Troubleshooting

### Windows Configuration Issues
If you encounter `Invalid JSON: EOF while parsing` or `MCP error -32000: Connection closed` on Windows, ensure `fastmcp` is updated to at least `3.2.4+`. Configure your `~/.claude.json` to execute the `.exe` directly:

```json
"code-review-graph": {
  "command": "C:\\path\\to\\your\\venv\\Scripts\\code-review-graph.exe",
  "args": ["serve", "--repo", "C:\\path\\to\\your\\project"],
  "env": { "PYTHONUTF8": "1" }
}
```

---

## Contributing

```bash
git clone https://github.com/yourusername/tokens-review-graph.git
cd tokens-review-graph
python3 -m venv .venv && source .venv/bin/activate
pip install -e ".[dev]"
pytest
```

### Adding a New Language

Edit `code_review_graph/parser.py` and add your extension to `EXTENSION_TO_LANGUAGE` along with node type mappings. Include a test fixture and open a PR.

---

## License

MIT. See [LICENSE](LICENSE).

---

```
pip install tokens-review-graph && tokens-review-graph install
```
