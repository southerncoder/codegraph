# Copilot Instructions

## Project Overview

CodeGraph is a local-first code intelligence system that builds a semantic knowledge graph from any codebase. It uses tree-sitter for AST parsing and SQLite for storage.

**Key characteristics:**
- Headless library (no UI) — purely an API
- Node.js runtime (works standalone, in Electron, or any Node environment)
- Per-project data stored in `.codegraph/` directory
- Deterministic extraction from AST, not AI-generated summaries

## Build and Development

```bash
npm run build          # Compile TypeScript and copy assets
npm test               # Run all tests (vitest)
npm run test:watch     # Run tests in watch mode
npm run clean          # Remove dist/
```

Run a single test file:
```bash
npx vitest run __tests__/extraction.test.ts
npx vitest run __tests__/extraction.test.ts -t "TypeScript"
```

## Architecture

```
src/
├── index.ts              # Main CodeGraph class — public API entry point
├── types.ts              # All TypeScript interfaces and types
├── db/                   # SQLite database layer (better-sqlite3, FTS5)
├── extraction/           # Tree-sitter AST parsing and symbol extraction
├── resolution/           # Reference resolver (imports, name matching, frameworks)
├── graph/                # Graph traversal (BFS/DFS, impact radius, call graphs)
├── vectors/              # Semantic search with ONNX embeddings
├── context/              # Context building for AI assistants
├── sync/                 # Incremental updates and git hooks
├── mcp/                  # Model Context Protocol server (stdio transport)
└── bin/codegraph.ts      # CLI entry point (Commander.js)
```

### Key Classes

- **CodeGraph** (`src/index.ts`): Main entry point — lifecycle, indexing, queries, search, context building
- **ExtractionOrchestrator** (`src/extraction/index.ts`): Coordinates file scanning, tree-sitter parsing, and storing
- **GraphTraverser** (`src/graph/traversal.ts`): BFS/DFS traversal, call graphs, impact radius, path finding
- **VectorManager** (`src/vectors/manager.ts`): Embeddings via `@xenova/transformers` (ONNX), vectors stored in SQLite BLOBs
- **ReferenceResolver** (`src/resolution/index.ts`): Resolves references using framework patterns, import resolution, name matching

### Database Schema

SQLite tables: `nodes` (code symbols), `edges` (relationships), `files` (tracked source files), `unresolved_refs` (pending references), `vectors` (embeddings as BLOBs), `nodes_fts` (FTS5 full-text search).

## Coding Conventions

- **TypeScript** with strict mode (`strict: true`, `noImplicitAny`, `strictNullChecks`, `noUnusedLocals`, `noUnusedParameters`, `noImplicitReturns`)
- Target **ES2022**, module system **CommonJS**
- All types defined in `src/types.ts` — use existing union types (`NodeKind`, `EdgeKind`, `Language`)
- Tests use **vitest** in `__tests__/` with `fs.mkdtempSync` for temp directories
- No UI code — this is a headless library
- Use JSDoc comments on public APIs
- Error handling: return error results in tool handlers, throw errors in core APIs

## Node and Edge Types

**NodeKind**: `file`, `module`, `class`, `struct`, `interface`, `trait`, `protocol`, `function`, `method`, `property`, `field`, `variable`, `constant`, `enum`, `enum_member`, `type_alias`, `namespace`, `parameter`, `import`, `export`, `route`, `component`

**EdgeKind**: `contains`, `calls`, `imports`, `exports`, `extends`, `implements`, `references`, `type_of`, `returns`, `instantiates`, `overrides`, `decorates`

**Language**: `typescript`, `javascript`, `tsx`, `jsx`, `python`, `go`, `rust`, `java`, `c`, `cpp`, `csharp`, `php`, `ruby`, `swift`, `kotlin`, `liquid`, `unknown`

## MCP Tools

The MCP server exposes these tools via stdio JSON-RPC:

| Tool | Purpose |
|------|---------|
| `codegraph_search` | Quick symbol lookup by name |
| `codegraph_context` | Build comprehensive task context (primary tool) |
| `codegraph_callers` | Find what calls a function |
| `codegraph_callees` | Find what a function calls |
| `codegraph_impact` | Analyze change impact radius |
| `codegraph_node` | Get symbol details and source code |
| `codegraph_status` | Index statistics |

## CLI Commands

```bash
codegraph init [path]       # Initialize in project
codegraph index [path]      # Full index
codegraph sync [path]       # Incremental update
codegraph status [path]     # Show statistics
codegraph query <search>    # Search symbols
codegraph context <task>    # Build context for AI
codegraph hooks install     # Install git auto-sync
codegraph serve --mcp       # Start MCP server
```
