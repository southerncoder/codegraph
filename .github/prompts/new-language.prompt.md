# Add New Language Support

Add support for the **[LANGUAGE_NAME]** programming language to CodeGraph.

## Steps

1. **Update `src/types.ts`**:
   - Add `'[language_id]'` to the `Language` union type
   - Add file extensions to `DEFAULT_CONFIG.include` (e.g., `'**/*.[ext]'`)

2. **Update `src/extraction/grammars.ts`**:
   - Add file extension → language mapping
   - Add grammar loading for the tree-sitter package

3. **Install tree-sitter grammar**:
   ```bash
   npm install tree-sitter-[language]
   ```

4. **Create tree-sitter queries** in `src/extraction/queries/[language]/`:
   - `highlights.scm` — Symbol extraction patterns
   - Extract: functions, classes/structs, methods, interfaces, variables, constants, imports, exports
   - Follow the pattern of existing query files (e.g., `src/extraction/queries/typescript/`)

5. **Add tests** in `__tests__/extraction.test.ts`:
   - Add a `describe('[LanguageName]')` block
   - Test extraction of key constructs (functions, classes, imports, etc.)
   - Use inline source strings and `extractFromSource(source, '[language_id]')`
   - Assert on extracted nodes (count, kinds, names) and edges

6. **Verify**:
   ```bash
   npm run build
   npx vitest run __tests__/extraction.test.ts -t "[LanguageName]"
   ```
