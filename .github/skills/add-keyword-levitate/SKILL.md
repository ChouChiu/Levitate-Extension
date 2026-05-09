---
name: add-keyword-levitate
description: 'Add a new keyword/command to the Levitate DSL language server. Use when extending the Levitate language with a new keyword, subcommand, alias, or environment variable.'
argument-hint: 'Keyword name, category (script/repl-only), and optional subcommands'
---

# Add Keyword to Levitate DSL

Adds a keyword, subcommand, alias, or environment variable to the Levitate language server and its supporting files.

## When to Use

- Adding a new Levitate DSL keyword (e.g., `zip`, `deploy`, `fmt`)
- Adding a subcommand to an existing keyword (e.g., a new `mrp` subcommand)
- Adding a new alias (e.g., a shorthand for a common command sequence)
- Adding a new built-in environment variable (e.g., `%project.config%`)
- Updating TextMate grammar for syntax highlighting a new keyword

## Procedure

### 1. Add to `languageData.ts`

Edit `packages/server/src/languageData.ts`:

**For a new keyword**, add an entry to `KEYWORD_DEFS`:

```typescript
// [name, commandName, category, [descKey, sigKey], ...subcommands?]
[
  "yourKeyword",
  "yourKeyword",
  "script",              // "script" or "repl-only"
  ["keyword.yourKeyword.description", "keyword.yourKeyword.signature"],
  // optional: ["sub1", "sub2"],
],
```

**For a new subcommand**, add to `SUBCOMMAND_DEFS`:

```typescript
// [name, parentKeywords[], descriptionKey]
["yourSub", ["parentKw1", "parentKw2"], "subcommand.yourSub.description"],
```

**For a new alias**, add to `ALIAS_DEFS`:

```typescript
// [name, expandsTo, descriptionKey]
["yourAlias", 'echo "hello"', "alias.yourAlias.description"],
```

**For a new env var**, add to `ENV_VAR_DEFS`:

```typescript
// [name, descriptionKey]
["project.yourVar", "envvar.project.yourVar.description"],
```

### 2. Add locale strings

Edit `packages/server/src/locales/en.json` (required, the fallback) and `packages/server/src/locales/zh-cn.json` (recommended):

```json
{
  "keyword.yourKeyword.description": "Description of your keyword.",
  "keyword.yourKeyword.signature": "yourKeyword <arg1> [arg2]",
  "subcommand.yourSub.description": "Description of your subcommand.",
  "alias.yourAlias.description": "Description of your alias.",
  "envvar.project.yourVar.description": "Description of your environment variable."
}
```

### 3. Update TextMate grammar (recommended)

Edit `packages/grammar/syntaxes/lvt.tmLanguage.json`:

**For a script keyword**, add the keyword to the `keywordScript` pattern's `match` regex:

```json
"match": "\\b(run|echo|input|...|yourKeyword)\\b"
```

**For a REPL-only keyword**, add to the `keywordRepl` pattern.

**For a subcommand**, add to the `subcommand` pattern.

**For an alias**, add to the `alias` pattern.

### 4. Verify

Run the build to ensure everything compiles:

```bash
pnpm run build
```

## Reference Files

| File                                            | Purpose                                      |
| ----------------------------------------------- | -------------------------------------------- |
| `packages/server/src/languageData.ts`           | Keyword/subcommand/alias/env-var definitions |
| `packages/server/src/locales/en.json`           | English locale (fallback)                    |
| `packages/server/src/locales/zh-cn.json`        | Chinese locale                               |
| `packages/grammar/syntaxes/lvt.tmLanguage.json` | TextMate grammar for highlighting            |
| `test/fixtures/sample.lvt`                      | Test fixture with usage examples             |

## Keywords Cheat Sheet

| Category           | Regex pattern in grammar | Examples                                                 |
| ------------------ | ------------------------ | -------------------------------------------------------- |
| Script keywords    | `keywordScript`          | `run`, `echo`, `var`, `copy`, `pkgr`, `mrp`              |
| REPL-only keywords | `keywordRepl`            | `exit`, `clear`, `help`, `proj`, `ext`, `task`, `reload` |
| Subcommands        | `subcommand`             | `get`, `set`, `add`, `make`, `zip`, `smajor`             |
| Aliases            | `alias`                  | `makeCleanup`, `makeCopy`, `makePkg`, `psdCvt`, `pbrEx`  |
