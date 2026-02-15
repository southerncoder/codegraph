---
applyTo: "src/extraction/**"
---

# Extraction Module Instructions

## Overview

The extraction module uses **tree-sitter** to parse source files into ASTs and extract code symbols (nodes) and relationships (edges).

## Key Files

- `index.ts` — `ExtractionOrchestrator`: coordinates scanning, parsing, storing
- `tree-sitter.ts` — Universal parser wrapper around tree-sitter native bindings
- `grammars.ts` — Language detection and grammar loading for all supported languages
- `queries/` — Tree-sitter query files (`.scm`) per language for pattern matching

## Adding a New Language

1. Add the language to the `Language` union type in `src/types.ts`
2. Add file extension mapping in `src/extraction/grammars.ts`
3. Install the tree-sitter grammar package (e.g., `tree-sitter-ruby`)
4. Create tree-sitter query files in `src/extraction/queries/<language>/`
5. Add extraction tests in `__tests__/extraction.test.ts`
6. Update the `include` patterns in `DEFAULT_CONFIG` in `src/types.ts`

## Conventions

- Every extracted symbol becomes a `Node` with a unique ID (hash of file path + qualified name)
- Relationships between symbols become `Edge` entries
- Unresolvable references (cross-file calls, imports) go into `UnresolvedReference` for later resolution
- Use the existing `NodeKind` and `EdgeKind` union types — do not add new kinds without updating `src/types.ts`
- Tree-sitter queries use S-expression syntax in `.scm` files
