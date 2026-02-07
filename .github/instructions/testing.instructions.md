---
applyTo: "__tests__/**"
---

# Testing Instructions

## Test Framework

Tests use **vitest** (`vitest.config.ts` at root). Run with `npm test` or `npx vitest run`.

## Conventions

- Test files live in `__tests__/` and mirror the module structure:
  - `foundation.test.ts` — Database, config, directory management
  - `extraction.test.ts` — Tree-sitter parsing for all supported languages
  - `resolution.test.ts` — Reference resolution
  - `graph.test.ts` — Traversal and graph queries
  - `vectors.test.ts` — Embedding and semantic search
  - `context.test.ts` — Context building
  - `sync.test.ts` — Incremental updates and git hooks
- Use `fs.mkdtempSync(path.join(os.tmpdir(), 'codegraph-test-'))` for temp directories
- Clean up temp directories in `afterEach` or `afterAll`
- Import types from `../src/types`
- Import the main API from `../src/index`
- Use `describe`/`it` blocks; group related tests with nested `describe`
- Each test should be self-contained and not depend on other tests

## Patterns

When testing extraction for a language, create an inline source string and pass it through `extractFromSource(source, language)`. Assert on the returned nodes, edges, and unresolved references.

When testing graph operations, first set up a CodeGraph instance, index sample files, then run queries and assert on the results.
