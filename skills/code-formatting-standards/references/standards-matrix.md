# Standards Matrix

This matrix is a decision aid, not a replacement for a repository's approved standard. The repository configuration always wins.

## Cross-Language Baseline

| Concern             | Recommended baseline when no project rule exists | Notes                                                          |
| ------------------- | ------------------------------------------------ | -------------------------------------------------------------- |
| Encoding            | UTF-8                                            | Preserve an established encoding when required by a toolchain. |
| Line endings        | LF                                               | Use CRLF only when a platform or repository requires it.       |
| Final newline       | Present                                          | Avoid files ending without a newline.                          |
| Trailing whitespace | Remove                                           | Markdown may intentionally preserve line breaks.               |
| Indentation         | Use the language ecosystem default               | Do not mix tabs and spaces in one file.                        |
| Line width          | Use the formatter default or project setting     | Do not manually wrap strings or generated content.             |
| Generated files     | Exclude when documented                          | Do not hide source files with broad patterns.                  |

## Language and Tool Boundaries

### JavaScript and TypeScript

- Use the repository's Prettier, Biome, or ESLint formatter configuration.
- Confirm parser and plugin support for JSX, TSX, decorators, import attributes, and framework syntax.
- Keep lint rules and formatter rules complementary.
- Avoid changing import order unless an approved import-sorting tool owns that behavior.

### React, Next.js, Vue, and Other Component Syntax

- Use the framework-aware parser or plugin already declared by the project.
- Preserve template expressions, directives, JSX props, and embedded CSS/JavaScript semantics.
- Do not treat accessibility, hook, or framework lint findings as formatting findings.
- Do not add a framework plugin solely because another project uses it.

### Python

- Identify whether Black, Ruff, YAPF, or another tool owns formatting.
- Respect `pyproject.toml`, `ruff.toml`, and line-length settings.
- Keep import sorting separate unless the selected tool explicitly owns it.

### Go, Rust, Java, Kotlin, C#, and C/C++

- Prefer the ecosystem formatter invoked by the build or repository scripts.
- Avoid introducing a second formatter that rewrites the same files.
- Treat build plugins and IDE profiles as part of the repository contract.

### Markdown, YAML, JSON, and SQL

- Check whether prose wrapping, YAML key ordering, JSON comments, or SQL dialect syntax needs special handling.
- Do not format lockfiles, generated API documents, or snapshots unless the project explicitly does so.
- Validate parsed syntax after formatting when the file is consumed by automation.

## Configuration Review Questions

Before approving a shared standard, ask:

1. Is the configuration parseable by the declared tool version?
2. Does every referenced plugin exist in the manifest and lockfile?
3. Are the file globs specific enough to avoid generated and vendored content?
4. Do EditorConfig and formatter settings agree on indentation and line endings?
5. Does VS Code select the same formatter used by CI?
6. Are package scripts available for both writing and checking formatting?
7. Does CI run a non-mutating check with locked dependencies?
8. Is the standard documented without claiming approval that has not been provided?

## Example Portable Scripts

These are templates. Adapt them to the repository's package manager and tool:

```json
{
	"scripts": {
		"format": "<formatter> --write <source-scope>",
		"format:check": "<formatter> --check <source-scope>"
	}
}
```

A shared skill may explain these commands, but should not add them to a repository without checking the project's existing scripts and package manager.
