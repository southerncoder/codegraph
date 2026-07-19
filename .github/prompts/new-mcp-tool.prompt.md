# Add New MCP Tool

Add a new MCP tool named **codegraph_[TOOL_NAME]** to the CodeGraph MCP server.

## Steps

1. **Define the tool** in `src/mcp/tools.ts`:
   - Add a new `ToolDefinition` object to the `tools` array
   - Name: `codegraph_[tool_name]` (use snake_case, prefixed with `codegraph_`)
   - Description: explain what the tool does and when to use it vs other tools
   - Input schema: define parameters with types, descriptions, and defaults
   - Mark required parameters in the `required` array

2. **Add handler routing** in `ToolHandler.execute()`:
   - Add a `case 'codegraph_[tool_name]':` to the switch statement
   - Call `this.handle[ToolName](args)`

3. **Implement the handler** as a private method on `ToolHandler`:
   ```typescript
   private async handle[ToolName](args: Record<string, unknown>): Promise<ToolResult> {
     // Extract and validate args
     // Call CodeGraph APIs (this.cg)
     // Format and return results
   }
   ```
   - Use `this.textResult(text)` for success, `this.errorResult(message)` for errors
   - Handle "symbol not found" cases gracefully
   - Keep response compact to minimize token usage
   - Use `this.cg.searchNodes(name, { limit: 1 })` to resolve symbol names to nodes

4. **Verify**:
   ```bash
   npm run build
   npm test
   ```

## Guidelines

- Keep tool descriptions concise but clear about when to use this vs `codegraph_context`
- Default parameter values should favor minimal output (e.g., `includeCode: false`)
- Group results by file when showing multiple symbols
- Every tool response should be useful standalone — avoid requiring follow-up calls
