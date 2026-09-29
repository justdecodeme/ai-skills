# Formatting Configuration File Reference

This reference explains common files and tools used to establish consistent formatting. The examples are intentionally generic and should be adapted to the project's approved configuration.

## Discover Before Editing

Inspect the repository's manifest, lockfile, formatter configuration, ignore files, EditorConfig, editor settings, and CI scripts before changing any of them. A nested package may have a different configuration from the repository root. The command used by CI is the strongest evidence of the formatter that owns the result.

## `prettier.config.js`

**Meaning: HOW TO FORMAT**

This file defines formatter behavior.

Typical options can control:

- indentation
- quotation marks
- semicolons
- line width
- trailing commas
- bracket spacing
- end-of-line behavior

Example:

```js
module.exports = {
	semi: true,
	singleQuote: true,
	tabWidth: 2,
	trailingComma: 'all',
};
```

The values above are examples only. They should not be treated as organization-wide requirements unless approved.

Other supported filenames may be used depending on the project.

## `.prettierignore`

**Meaning: WHAT NOT TO FORMAT**

It contains patterns for files or directories that should be excluded from formatting.

Example:

```text
node_modules/
dist/
build/
coverage/
generated/
```

The exact exclusions should be based on the project.

## `.editorconfig`

**Meaning: EDITOR CONSISTENCY**

EditorConfig helps different editors use common basic settings.

Example:

```ini
root = true

[*]
indent_style = space
indent_size = 2
end_of_line = lf
charset = utf-8
insert_final_newline = true
trim_trailing_whitespace = true
```

These are example settings, not mandatory standards.

## `settings.json`

**Meaning: VS CODE INTEGRATION**

This file can configure workspace-specific VS Code behavior.

A formatting setup may include settings conceptually equivalent to:

```json
{
	"editor.formatOnSave": true
}
```

The exact formatter configuration depends on the tools installed and the project's approved setup.

## `package.json`

**Meaning: DEPENDENCIES & SCRIPTS**

A project manifest can declare formatter dependencies and convenient commands.

Example:

```json
{
	"scripts": {
		"format": "prettier --write .",
		"format:check": "prettier --check ."
	}
}
```

The exact commands depend on the project.

## VS Code Extensions

### Prettier - Code formatter

Provides Prettier formatting support inside VS Code.

It can be used with the project's Prettier configuration and can be selected as the default formatter.

### EditorConfig for VS Code

Provides support for `.editorconfig` rules inside VS Code.

It helps apply editor-level settings such as indentation and line endings.

### Why both?

They solve related but different problems:

- **Prettier** → controls code formatting.
- **EditorConfig** → controls common editor behavior.

The extensions simply make these tools available inside VS Code.

## Relationship Between the Files and Tools

| Item                   | Main responsibility                    |
| ---------------------- | -------------------------------------- |
| `prettier.config.js`   | Defines formatting rules               |
| `.prettierignore`      | Defines what the formatter skips       |
| `.editorconfig`        | Defines common editor behavior         |
| `settings.json`        | Provides VS Code integration           |
| `package.json`         | Defines dependencies and scripts       |
| Prettier extension     | Applies Prettier inside VS Code        |
| EditorConfig extension | Applies `.editorconfig` inside VS Code |

## Important

These files can overlap in some areas. When conflicts occur, follow the project's documented configuration and tooling behavior rather than assumptions.

The formatter configuration should remain the primary source of formatting rules when Prettier is the project's approved formatter.

## Common Non-Prettier Owners

Do not add Prettier merely because another repository uses it. Check for the tool already used by the project:

| Evidence                                  | Likely owner          |
| ----------------------------------------- | --------------------- |
| `biome.json` and `biome check` scripts    | Biome                 |
| `ruff format` or `[tool.ruff.format]`     | Ruff                  |
| `black` configuration or scripts          | Black                 |
| `cargo fmt` or `rustfmt.toml`             | rustfmt               |
| `gofmt` or `goimports` scripts            | gofmt or goimports    |
| `.clang-format` and clang tooling         | clang-format          |
| `dotnet format` or analyzer configuration | .NET format/analyzers |

If two tools can rewrite the same files, assign one formatter owner and keep the other tool focused on linting or a separate concern such as import sorting.

## Safe Configuration Review

Before approving a shared configuration, verify that:

- the configuration parses with the installed tool version;
- every referenced plugin is declared and available from the lockfile;
- file globs do not accidentally include generated, vendored, minified, or sensitive files;
- `.editorconfig`, formatter settings, and line-ending policy do not conflict;
- VS Code selects the same formatter that CI runs;
- both a write command and a non-mutating check are available when the project supports them;
- the documentation does not claim an unverified rule is an official Lenovo standard.

Configuration changes should be tested on one representative source file before a repository-wide format. Review the diff for logic changes, renamed strings, line-ending churn, and unexpected files.
