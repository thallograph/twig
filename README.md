# Twig CLI

> Knowledge management system with graph-RAG for markdown notes

 `twig` is a command-line tool for managing markdown-based knowledge bases with semantic search, wiki-link graph traversal, and tag-based organization powered by local embeddings via Ollama.

## Features

- 🔍 **Semantic search** across markdown files using vector embeddings
- 🕸️ **Wiki-link graph traversal** for discovering connected knowledge
- 🏷️ **Tag-based organization** with automatic tag extraction
- 🤖 **Ollama-powered embeddings** using nomic-embed-text (local, private)
- 📊 **Link analysis** and broken link detection
- ⚡ **Fast local database** with PGlite (PostgreSQL in-process)
- 🔄 **Real-time sync** with file watching
- 📈 **Graph-RAG** combining vector similarity, keyword search, and graph proximity

## Prerequisites

- **Bun** 1.3.3 or higher ([install](https://bun.sh))
- **Ollama** with `nomic-embed-text` model ([install](https://ollama.ai))

### Setup Ollama

```bash
# Install Ollama (macOS/Linux)
curl -fsSL https://ollama.ai/install.sh | sh

# Start Ollama service
ollama serve

# Pull the embedding model
ollama pull nomic-embed-text
```

## Installation

```bash
npm install -g @thallograph/twig
# or
bun install -g @thallograph/twig
```

## Quick Start

```bash
# Navigate to your markdown notes directory
cd ~/notes

# Initialize Thallograph project
twig init

# Sync markdown files (generates embeddings, extracts links and tags)
twig sync

# Query your knowledge base
twig query "How do I configure embeddings?"

# Check system health
twig doctor
```

## Commands

### Project Management

#### `twig init [directory]`

Initialize Thallograph project in current or specified directory.

**Options:**
- `--force` - Overwrite existing `.thallograph` directory
- `--skip-ollama-check` - Skip Ollama validation (for CI/CD)

**Example:**
```bash
twig init
twig init ~/my-notes --force
```

#### `twig doctor`

Validate environment and dependencies: Ollama service, database, migrations, and runtime.

**Example:**
```bash
twig doctor
```

### File Synchronization

#### `twig sync [options]`

Sync markdown files to database: extract links, tags, and generate embeddings.

**Options:**
- `--no-embeddings` - Skip embedding generation (faster, links/tags only)
- `--dry-run` - Show what would be synced without making changes
- `--watch` - Continuous sync mode (monitors file changes)
- `--concurrency <n>` - File processing concurrency (default: 10)

**Examples:**
```bash
# Full sync with embeddings
twig sync

# Quick sync without embeddings
twig sync --no-embeddings

# Watch mode for continuous updates
twig sync --watch
```

### Context Retrieval

#### `twig query <question> [options]`

Retrieve relevant context using Graph-RAG: hybrid search with vector similarity, full-text, and graph traversal.

**Options:**
- `--max-docs <n>` - Maximum documents to return (default: 20)
- `--depth <n>` - Graph traversal depth (default: 2)
- `--semantic-weight <n>` - Semantic search weight (default: 0.4)
- `--keyword-weight <n>` - Keyword search weight (default: 0.3)
- `--graph-weight <n>` - Graph proximity weight (default: 0.2)
- `--min-score <n>` - Minimum relevance score (default: 0.3)
- `--json` - Output JSON format for scripting

**Examples:**
```bash
# Interactive query
twig query "How to configure embeddings?"

# JSON output for scripting
twig query "setup instructions" --json

# Adjust scoring weights
twig query "architecture" --semantic-weight 0.6 --graph-weight 0.3
```

### Link Management

#### `twig links list [options]`

List all wiki-links in the project.

**Options:**
- `--broken` - Show only broken links
- `--ambiguous` - Show only ambiguous links (multiple targets)
- `--file <path>` - Filter by source file
- `--json` - Output JSON format

**Example:**
```bash
twig links list --broken
```

#### `twig links stats`

Display link statistics and graph metrics.

**Example:**
```bash
twig links stats
```

### Tag Management

#### `twig tags list`

List all tags in the project with document counts.

**Example:**
```bash
twig tags list
```

#### `twig tags show <tag>`

Show all documents with a specific tag.

**Example:**
```bash
twig tags show "#architecture"
```

### Database Management

#### `twig db migrate`

Run pending database migrations.

**Example:**
```bash
twig db migrate
```

#### `twig db reset`

Reset database (WARNING: destroys all data).

**Options:**
- `--force` - Skip confirmation prompt

**Example:**
```bash
twig db reset --force
```

### Embeddings Management

#### `twig embeddings generate [options]`

Generate embeddings for documents.

**Options:**
- `--force` - Regenerate embeddings for all documents (even if already generated)
- `--file <path>` - Generate embeddings for specific file only

**Example:**
```bash
# Generate missing embeddings
twig embeddings generate

# Regenerate all embeddings
twig embeddings generate --force
```

#### `twig embeddings stats`

Display embedding generation statistics.

**Example:**
```bash
twig embeddings stats
```

### Configuration

#### `twig config get <key>`

Get configuration value.

**Example:**
```bash
twig config get embeddings.model
```

#### `twig config set <key> <value>`

Set configuration value.

**Example:**
```bash
twig config set embeddings.model "nomic-embed-text"
```

## Configuration File

Thallograph uses `.thallograph/config.json` for configuration:

```json
{
  "embeddings": {
    "model": "nomic-embed-text",
    "baseUrl": "http://localhost:11434",
    "dimensions": 768
  },
  "sync": {
    "concurrency": 10,
    "includePatterns": ["**/*.md"],
    "excludePatterns": ["node_modules/**", ".git/**"]
  },
  "retrieval": {
    "maxDocs": 20,
    "traversalDepth": 2,
    "semanticWeight": 0.4,
    "keywordWeight": 0.3,
    "graphWeight": 0.2,
    "bm25Weight": 0.1
  }
}
```

## Project Structure

```
your-notes/
├── .thallograph/          # Thallograph project directory
│   ├── db/                # PGlite database
│   └── config.json        # Configuration
├── docs/                  # Your markdown files
├── notes/
└── *.md
```

## Troubleshooting

### "Ollama service not available"

Ensure Ollama is running:
```bash
ollama serve
```

### "Model nomic-embed-text not found"

Pull the embedding model:
```bash
ollama pull nomic-embed-text
```

### "Database migration failed"

Reset and reinitialize:
```bash
twig db reset --force
twig init
```

## License

This software is licensed under the **PolyForm Shield License 1.0.0**.

**Required Notice**: Copyright 2026 Craft Effect LLC (https://github.com/thallograph/twig)

See the [LICENSE](LICENSE) file for complete terms, or visit:  
https://polyformproject.org/licenses/shield/1.0.0

### What This Means

- ✅ You can use this software for any purpose
- ✅ You can modify and distribute it
- ❌ You cannot use it to compete with Thallograph or Craft Effect LLC products
- ❌ No liability or warranty provided

See the **Noncompete** section in the license for full details.

## Contributing

See [GitHub repository](https://github.com/thallograph/twig) for source code and issue tracking.

## Support

- Documentation: https://github.com/thallograph/twig
- Issues: https://github.com/thallograph/twig/issues
