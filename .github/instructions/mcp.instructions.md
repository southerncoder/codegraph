---
applyTo: "src/mcp/**"
---

# MCP Server Instructions

## Overview

The MCP (Model Context Protocol) server exposes CodeGraph's functionality over stdio JSON-RPC transport, enabling AI assistants to query the code knowledge graph.

## Key Files

- `index.ts` — `MCPServer` class: handles lifecycle, initialization, request routing
- `tools.ts` — `ToolDefinition` array and `ToolHandler` class for executing tools
- `transport.ts` — Stdio JSON-RPC transport (reads from stdin, writes to stdout)

## Protocol

- Uses MCP protocol version `2024-11-05`
- Transport: stdio (stdin/stdout JSON-RPC)
- Requests handled: `initialize`, `tools/list`, `tools/call`
- CodeGraph initialization is deferred until the client sends `rootUri`

## Adding a New Tool

1. Add the `ToolDefinition` to the `tools` array in `tools.ts`:
   - `name`: prefixed with `codegraph_`
   - `description`: clear, concise; mention when to use this vs other tools
   - `inputSchema`: JSON Schema with `type: 'object'`, `properties`, and `required`
2. Add a case in `ToolHandler.execute()` switch statement
3. Implement the handler method as `private async handle<ToolName>(args)`
4. Return results using `this.textResult(formatted)` or `this.errorResult(message)`
5. Keep responses compact to minimize context token usage

## Conventions

- Tool names use `codegraph_` prefix
- `codegraph_context` is the primary tool — other tools are for targeted follow-up
- Minimize response size: use compact formatting, avoid redundant data
- Always handle the case where a symbol is not found
- Use `searchNodes` to resolve symbol names to node IDs before graph operations
