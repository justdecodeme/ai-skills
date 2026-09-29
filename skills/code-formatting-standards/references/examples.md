# Practical Examples

These examples describe response and implementation patterns. They are not a substitute for a repository's checked-in configuration.

## 1. Explain a configuration

User:
"What does `.prettierignore` do?"

Answer approach:

- Explain that it tells Prettier which paths to skip.
- Give a few generic examples.
- Explain why generated or build output is commonly excluded.
- Avoid claiming a specific exclusion is mandatory unless documented.

## 2. Review code formatting

User:
"Review this code according to our formatting standards."

Answer approach:

1. Identify formatting issues.
2. State the applicable rule.
3. Provide corrected code.
4. Preserve the original logic.
5. Mention when the exact organization rule is not available.

## 3. Format code without changing logic

User:
"Format this code but don't change the functionality."

Answer approach:

- Change whitespace, indentation, line breaks, quotes, semicolons, and other formatter-controlled elements.
- Do not rename variables.
- Do not change control flow.
- Do not refactor functions.
- Do not change APIs or behavior.

## 4. New project setup

User:
"How should I set up standard formatting for a new project?"

Answer approach:

- Add the approved formatter configuration.
- Add an ignore file where needed.
- Add EditorConfig settings.
- Configure editor integration if required.
- Add formatter dependencies.
- Add formatting and format-check scripts.
- Add CI/CD validation when appropriate.
- Do not assume a specific language, framework, package manager, or editor unless the user specifies one.

## 5. VS Code setup

User:
"Which VS Code extensions do I need for formatting?"

Answer approach:

- Recommend the Prettier extension when Prettier is the approved formatter.
- Recommend EditorConfig support when the project uses `.editorconfig`.
- Explain that extensions are tooling and the project configuration remains the source of truth.

## 6. Format-on-save troubleshooting

User:
"Why isn't format-on-save working?"

Check conceptually:

- Is the formatter installed?
- Is the correct formatter selected?
- Is format-on-save enabled?
- Is the file excluded by an ignore rule?
- Is the project configuration being detected?
- Are there conflicting editor or formatter settings?
- Is the file type supported?
- Is the project using a different formatter?
- Is the required VS Code extension installed and enabled?

## 7. EditorConfig troubleshooting

User:
"Why does my editor use different indentation or line endings?"

Check conceptually:

- Is `.editorconfig` present?
- Is EditorConfig support installed/enabled?
- Is the file covered by the relevant EditorConfig pattern?
- Are workspace or user settings overriding the expected behavior?
- Is another formatter changing the file after editor settings are applied?

## 8. CI/CD validation

A project may use a command such as:

```bash
prettier --check .
```

This validates formatting without rewriting files.

The exact command should follow the project's package scripts and approved CI/CD setup.

## 9. Package-Manager-Neutral Commands

When the package manager is unknown, inspect the manifest and lockfile first. Then substitute the repository's command in the following templates:

```text
<package-manager> run format
<package-manager> run format:check
<package-manager> exec <formatter> --check <paths>
```

Typical mappings include `npm run`, `pnpm run`, `yarn`, or `bun run`, but never assume one from the language alone. Do not install a global formatter when the repository provides a local one.

## 10. Formatting vs linting

If a developer asks why a problem remains after formatting:

Explain that formatting tools and linters solve different problems.

For example:

- Formatter → indentation and whitespace.
- Linter → code-quality or programming-pattern issues.

Do not automatically recommend changing linting rules when the request is only about formatting.

## 11. Review a Large or Suspicious Diff

When formatting produces unexpected changes:

1. Stop before accepting the diff.
2. Check the formatter version, nearest configuration, ignore rules, and line endings.
3. Confirm that only one formatter or save action is rewriting the file.
4. Narrow the command to the requested files.
5. Separate an intentional baseline reformat from the functional change.

Never hide source files with a broad ignore rule just to make a check pass.

## 12. CI Check

A CI job should install locked dependencies and run a deterministic, non-mutating check from the correct repository or package root:

```text
<package-manager> run format:check
```

The failure message should identify the command developers can run locally. CI should not format files in place and then report success, because that can hide drift.

## 13. Safe Handoff

For a completed formatting task, report the formatter and version, changed scope, configuration assumptions, validation commands, and any remaining warnings. Do not claim validation passed when a required tool or dependency was unavailable.

## 14. Configuration precedence and conflicts

User:
"Why is my code being formatted differently from the project standard?"

Answer approach:

- Check the project formatter configuration.
- Check editor-specific settings.
- Check whether another formatter is active.
- Check installed formatter extensions.
- Check whether the file is covered by ignore rules.
- Identify which configuration is actually being applied.
- Prefer the documented project configuration over personal editor preferences.
