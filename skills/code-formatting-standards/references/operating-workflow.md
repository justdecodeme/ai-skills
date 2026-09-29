# Operating Workflow

Use this reference when applying the skill to a real repository.

## 1. Discover Before Editing

Inspect only the files needed to establish the repository's formatting contract:

- manifest and lockfile: `package.json`, `pyproject.toml`, `go.mod`, `Cargo.toml`, or equivalent;
- formatter configuration: `.prettierrc*`, `prettier.config.*`, `biome.json*`, `ruff.toml`, `pyproject.toml`, `.editorconfig`, `.clang-format`, or equivalent;
- ignore files: `.prettierignore`, `.gitignore`, formatter-specific ignores, and generated-file documentation;
- editor and CI configuration: `.vscode/settings.json`, GitHub Actions, Jenkins, GitLab CI, or equivalent;
- scripts: commands containing `format`, `fmt`, `check`, `lint`, `fix`, or the formatter name.

Check the repository status before editing. Never overwrite unrelated local changes.

## 2. Decide What Owns Formatting

Use the repository's declared tool. Common ownership patterns include:

| Repository evidence                       | Likely owner             |
| ----------------------------------------- | ------------------------ |
| `prettier` dependency and `format` script | Prettier                 |
| `biome.json` and `biome check --write`    | Biome                    |
| `ruff format` or `[tool.ruff.format]`     | Ruff                     |
| `black` configuration                     | Black                    |
| `gofmt` in scripts or Go tooling          | gofmt                    |
| `rustfmt.toml` or `cargo fmt`             | rustfmt                  |
| `.clang-format` and clang tooling         | clang-format             |
| `dotnet format` or analyzer configuration | .NET formatter/analyzers |

If two tools can rewrite the same file, identify the intended owner before changing either configuration.

## 3. Run a Read-Only Check

Prefer the project's script. Generic equivalents are examples only:

```text
<package-manager> run format:check
<formatter> --check <paths>
<formatter> --list-different <paths>
```

Use the package manager and command already documented by the repository. Do not install a global formatter as a shortcut.

## 4. Make the Smallest Change

- For a formatting-only request, change formatting and preserve behavior.
- For a configuration request, update the config, script, or editor integration that owns the behavior.
- Format only the requested files or package unless a baseline reformat was explicitly approved.
- Avoid formatting generated, vendored, minified, or sensitive files.
- Do not use a broad ignore pattern to silence source files.

## 5. Validate the Diff

Inspect the diff after formatting. Look for:

- renamed or deleted lines;
- changed strings, regular expressions, or template delimiters;
- unexpected line-ending or encoding changes;
- generated files changed accidentally;
- files outside the requested scope changed;
- formatter output that conflicts with lint or typecheck rules.

A formatter should not change application behavior. If it appears to, stop and investigate instead of accepting the output.

## 6. Validate the Result

Run checks in this order:

1. formatter check for the touched files;
2. relevant lint, typecheck, or tests;
3. the build when shared configuration, package scripts, or broad file scope changed.

Report exact commands and whether they passed. If a command cannot run, explain the missing prerequisite rather than claiming success.

## Troubleshooting Matrix

| Symptom                          | Checks                                                                              | Resolution                                                                           |
| -------------------------------- | ----------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------ |
| Format-on-save does nothing      | formatter extension, selected formatter, file language, config discovery            | select the project formatter and verify the file is supported                        |
| CLI and VS Code disagree         | active formatter, workspace settings, user settings, config path, formatter version | use the repository formatter and remove conflicting editor overrides                 |
| Only some files change           | ignore patterns, nested config, file extension, parser/plugin                       | inspect the nearest config and run the formatter on one file                         |
| Formatting changes on every save | multiple formatters, unstable plugin/version, line endings                          | keep one formatter owner and pin the tool version                                    |
| CI fails but local check passes  | lockfile, Node/runtime version, working directory, generated files                  | reproduce with locked dependencies and the same CI command                           |
| Formatter reports parser errors  | unsupported syntax, wrong parser, missing plugin, malformed config                  | use the correct parser/plugin or exclude only documented generated syntax            |
| Huge unrelated diff              | wrong root, line endings, broad command, changed formatter version                  | stop, narrow scope, restore only formatter noise, and document any baseline reformat |

## Safe Handoff

A completed task should state:

- formatter and version used;
- files or scope changed;
- configuration decisions and assumptions;
- validation commands and results;
- remaining warnings or follow-up work.
