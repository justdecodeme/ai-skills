---
name: lenovo-code-formatting-standards
description: Help developers understand, apply, and troubleshoot standardized code-formatting practices across projects, languages, repositories, and development environments. Use this skill when explaining formatting rules, reviewing code for formatting consistency, setting up formatter configuration, configuring editor tooling, or troubleshooting formatting issues.
license: MIT
allowed-tools:
  - Read
  - Grep
  - Glob
compatibility: VS Code Copilot Skill Hub
metadata:
  data-classification: internal
  network-egress: none
  scopes:
    - code-formatting
    - developer-tooling
  env-required: false
---

# Code Formatting Standards

## Purpose

Help developers consistently understand and apply approved code-formatting standards across projects, repositories, languages, and development environments.

This is a standalone knowledge skill. It does not require MCP, backend access, APIs, personal data, or access to internal systems.

## Provenance and License

This skill is Lenovo-authored internal guidance for code-formatting workflows. The included examples and tables are synthetic, generic material created for this skill; no third-party source text, proprietary repository content, credentials, or external assets are bundled.

This material is provided under the MIT license. Lenovo teams may apply additional Skill Hub distribution controls where required by internal policy.

## Operating Contract

Use this skill for formatting work only. Treat the repository's existing configuration and scripts as the authority; this skill supplies a safe workflow for discovering and applying that authority.

Before changing files:

1. Identify the repository root and inspect its manifest, formatter configuration, ignore files, EditorConfig, and relevant scripts.
2. Determine the language, framework, package manager, and formatter used by the touched files.
3. Check the current working tree and avoid overwriting unrelated user changes.
4. State one concrete formatting hypothesis and one focused validation check when the request is a bug or inconsistency.
5. Prefer the smallest formatter or configuration change that addresses the request.

After changing files:

1. Run the narrowest available formatter check on the touched files.
2. Run the project's typecheck, lint, tests, or build when the change affects shared configuration or scripts.
3. Report what changed, what was validated, and any remaining warnings or assumptions.

Explain the intended scope before formatting the repository. Ask for confirmation before a broad rewrite when the request does not clearly authorize it.

## Core Principles

- Follow the project's approved formatting standards rather than personal preferences.
- Keep formatting consistent across files, components, repositories, and teams.
- Prefer automated formatting over manual formatting.
- Use shared configuration as the source of truth where one exists.
- Do not invent organization-specific rules when they are not documented.
- Clearly distinguish documented standards from general recommendations.
- Do not change application logic when the request is only about formatting.
- Avoid unrelated refactoring when formatting is the requested task.
- Consider generated files and excluded content before recommending formatting changes.
- Preserve line-ending and encoding requirements that are already established by the repository.
- Treat formatting as a reproducible toolchain: pin or respect the project's formatter version where possible.
- Prefer configuration changes over editor-only workarounds.
- Never put credentials, tokens, or private source content into examples or shared configuration.

## Configuration Files

### `prettier.config.js` — HOW TO FORMAT

Defines formatting rules used by Prettier, such as:

- indentation
- quotes
- semicolons
- line width
- trailing commas
- bracket spacing
- end-of-line behavior

The exact rules should come from the project's approved configuration.

Other valid Prettier configuration filenames may be used depending on the project setup, such as `prettier.config.cjs`, `prettier.config.mjs`, or equivalent supported formats.

### `.prettierignore` — WHAT NOT TO FORMAT

Defines files and directories that Prettier should skip.

Common examples can include:

- dependency directories
- build output
- generated files
- coverage output
- version-control metadata
- other files that should not be automatically modified

The actual exclusions should follow project requirements.

### `.editorconfig` — EDITOR CONSISTENCY

Defines editor-independent settings that help different editors and IDEs behave consistently.

Typical settings include:

- indentation style
- indentation size
- line endings
- character encoding
- final newline
- trailing whitespace behavior

It complements formatter configuration; it does not replace it.

### `settings.json` — VS CODE INTEGRATION

Contains VS Code-specific settings.

Formatting-related settings may include:

- selecting the formatter
- enabling format-on-save
- configuring workspace formatting behavior

These settings are specific to VS Code and should not be treated as the universal source of formatting rules.

### `package.json` — DEPENDENCIES & SCRIPTS

Defines project dependencies and scripts.

A project may use it to declare:

- the formatter as a development dependency
- formatter plugins
- formatting commands
- formatting validation/check commands

The exact dependencies and scripts depend on the project's technology and approved setup.

### Other formatter configurations

Do not assume Prettier is universal. Check for the project's actual tool before editing configuration:

- JavaScript/TypeScript: Prettier, ESLint stylistic rules, Biome, or a framework-integrated formatter.
- Python: Black, Ruff formatter, YAPF, or the project's documented tool.
- Java/Kotlin: Spotless, ktfmt, IntelliJ formatting, or the project's build plugin.
- Go: `gofmt` or `goimports`.
- Rust: `rustfmt`.
- C/C++: clang-format.
- C#: `dotnet format` or the repository's analyzer configuration.
- SQL, YAML, Markdown, and shell: use the repository's declared formatter and parser settings.

If multiple tools format the same file, identify the owner. A linter may report style without owning formatting; a formatter should not be configured to fight the linter.

## Recommended VS Code Extensions

When VS Code is the development environment, commonly used extensions include:

### Prettier - Code formatter

Used to format supported files according to the project's Prettier configuration.

It can also be selected as the default formatter and used with format-on-save.

### EditorConfig for VS Code

Used to apply `.editorconfig` settings in VS Code.

It helps maintain basic editor consistency such as indentation and line endings.

### Important distinction

Extensions are tools that help developers apply the standards. They are not the standards themselves.

The project's approved configuration should remain the source of truth.

## How the Pieces Work Together

Think of the setup as five configuration layers plus editor tooling:

1. `prettier.config.js` → defines **HOW code should be formatted**.
2. `.prettierignore` → defines **WHAT should not be formatted**.
3. `.editorconfig` → provides **common editor settings**.
4. `settings.json` → provides **VS Code integration**.
5. `package.json` → provides **dependencies and project scripts**.
6. VS Code extensions → provide the **tools that apply these settings during development**.

## Configuration Precedence

Resolve conflicts in this order, unless the repository documents a different policy:

1. Explicit repository formatter configuration and formatter version.
2. File-specific or directory-specific configuration closest to the file.
3. `.editorconfig` for editor-level behavior not controlled by the formatter.
4. Workspace editor settings such as `.vscode/settings.json`.
5. User-level editor settings and extensions.

An ignore rule can prevent a formatter from touching a file; it does not make that file correctly formatted. A formatter configuration can override some EditorConfig values when the formatter runs. Verify behavior with the tool's `--find-config-path`, `--check`, `--list-different`, or equivalent command when available.

## Standard Workflow

### Existing repository

1. Read `package.json`, lockfiles, formatter configs, ignore files, `.editorconfig`, and CI configuration.
2. Search for scripts containing `format`, `fmt`, `prettier`, `lint`, `check`, or `fix`.
3. Inspect the target file and its nearest configuration boundary.
4. Run a read-only check before formatting when possible.
5. Format only the requested files or package.
6. Inspect the diff for logic, generated-file, and line-ending changes.
7. Run the narrow validation command, then broader validation if configuration or shared tooling changed.

### New repository

1. Select a formatter supported by the language and team.
2. Add the formatter as a pinned or range-controlled development dependency when the ecosystem supports it.
3. Add a checked-in configuration and ignore file.
4. Add `format` and `format:check` scripts, or the equivalent commands.
5. Configure CI to fail on formatting drift.
6. Document editor integration without making VS Code a runtime requirement.
7. Format a small representative sample before formatting the entire repository.

### Monorepo

- Find the nearest package boundary before choosing a config.
- Do not impose a root configuration on packages that intentionally use another tool.
- Keep shared configuration in one versioned location when packages truly share standards.
- Run checks per package or through the workspace runner so failures identify the owning package.
- Exclude generated output, vendored code, migrations, snapshots, and fixtures only when the repository policy supports it.

## What This Skill Can Do

- Explain code-formatting standards in simple language.
- Explain the purpose of each configuration file.
- Explain the purpose of recommended editor extensions.
- Review provided code for formatting inconsistencies.
- Provide a corrected, formatted version of code.
- Explain why a formatting change was recommended.
- Help configure formatting for a new project without assuming a particular framework.
- Troubleshoot formatter and editor issues.
- Explain the difference between formatter configuration and editor configuration.
- Explain how formatting can be checked locally and in CI/CD.
- Help developers understand conflicts between formatter settings and editor settings.
- Recommend a consistent formatting workflow.
- Select the correct formatter based on repository evidence.
- Design or review a formatter configuration for a new repository or monorepo.
- Add safe `format` and `format:check` scripts without assuming npm, pnpm, yarn, or bun.
- Diagnose configuration precedence, plugin/version mismatches, and formatter conflicts.
- Review a formatter diff for accidental behavior or generated-file changes.
- Produce a CI-ready formatting check and a concise developer handoff.

## Example Questions Users Can Ask

- "What are our code-formatting standards?"
- "How should this code be formatted?"
- "Can you format this code according to the standard?"
- "What does `prettier.config.js` do?"
- "What is the purpose of `.prettierignore`?"
- "Why do we need `.editorconfig`?"
- "What is the difference between `.editorconfig` and Prettier?"
- "What does `settings.json` do for formatting?"
- "Which VS Code extensions are needed for formatting?"
- "Why do I need the Prettier extension?"
- "Why isn't my `.editorconfig` being applied?"
- "Why isn't format-on-save working?"
- "How do I configure Prettier in VS Code?"
- "What should be included in `package.json` for formatting?"
- "How can I set up formatting for a new project?"
- "Why is my editor formatting code differently from another developer's?"
- "How can we validate formatting in CI/CD?"
- "Review this code and show me only the formatting issues."
- "Format this code without changing its logic."
- "What is the difference between formatting and linting?"

## Code Review Behavior

When reviewing code for formatting:

1. Identify formatting issues clearly.
2. Explain the relevant standard briefly.
3. Show corrected code when useful.
4. Preserve application behavior and logic.
5. Do not introduce unrelated refactoring.
6. If the organization's exact rule is unknown, say so and provide a general recommendation separately.

For a review, list findings first and order them by impact:

- **Blocking:** formatter check fails, configuration cannot be parsed, or formatting changes generated/source boundaries unexpectedly.
- **Important:** inconsistent tool ownership, missing CI check, conflicting editor settings, or a broad ignore rule hiding source files.
- **Advisory:** readability or consistency improvement that is not required by the documented standard.

Include the file path, applicable rule, and a focused correction. Do not report lint, type, security, or architecture findings as formatting findings unless the user requests a broader review.

## Formatting vs Other Concerns

Do not confuse formatting with other engineering concerns.

### Formatting

Examples:

- indentation
- whitespace
- quotes
- line breaks
- semicolons
- trailing commas
- line length

### Linting

Examples:

- unused variables
- potential bugs
- code-quality rules
- prohibited patterns

### Code Quality / Architecture

Examples:

- maintainability
- design patterns
- folder structure
- performance
- security
- application architecture

A formatting request should not automatically become a linting, refactoring, or architecture review.

## Technology-Neutral Guidance

This skill must remain generic and framework-neutral.

Do not assume:

- a particular programming language
- a particular frontend or backend framework
- a particular build tool
- a particular repository structure
- a particular package manager
- a particular IDE

When a project-specific configuration is provided, use that configuration as the source of truth.

Use technology-neutral command placeholders in documentation:

```text
<package-manager> run format
<package-manager> run format:check
<formatter> --check <paths>
```

Resolve the placeholders from the repository's package manager and scripts. Tell users to use the repository-provided formatter when one is available, rather than relying on a global installation.

## CI/CD Guidance

A formatting check should be deterministic, non-mutating, and run against the same dependency lockfile used by developers. Prefer a check command such as `prettier --check .` or the repository's equivalent. Keep write commands for local development and autofix workflows.

CI should:

- install the locked dependency versions;
- run the formatter check from the repository root or explicit package scope;
- fail with the offending paths and a remediation command;
- avoid formatting files generated by the same CI job;
- use the same ignore and configuration files as local development.

## Safe Formatting Boundaries

Formatting may be unsafe or noisy for generated code, vendored code, lockfiles, database migrations, golden snapshots, embedded templates, minified assets, and files with tool-specific syntax. Before adding an ignore pattern, verify that the file is generated or intentionally exempt; broad patterns such as `*.js` or an entire source directory can hide real drift.

When a formatter changes many unrelated lines:

1. Stop and inspect the configuration, version, line endings, and ignore rules.
2. Check whether a different formatter or plugin is being applied.
3. Narrow the command to the requested files.
4. Separate an intentional baseline reformat from the functional change.

## Completion Checklist

- [ ] Repository formatter and version identified.
- [ ] Applicable config and ignore files inspected.
- [ ] Existing user changes preserved.
- [ ] Only intended files were formatted or configured.
- [ ] Generated, vendored, and sensitive files were considered.
- [ ] Formatter check passed for the touched scope.
- [ ] Typecheck, lint, tests, or build passed when relevant.
- [ ] Remaining warnings, assumptions, and unavailable tools were reported.

## Safety and Accuracy

- Do not claim a rule is an official organization standard unless it is documented or provided by the user.
- If the approved configuration is unavailable, clearly label examples as generic examples.
- Do not expose or request sensitive internal information.
- Do not require MCP or system access for normal use of this skill.
- Do not upload or include proprietary source, credentials, internal URLs, or user data in a skill ZIP.
- Keep bundled examples synthetic and free of secrets.
- If a rule depends on a Lenovo team or product standard that is not included in the skill, label it as an assumption and request the approved source rather than inventing it.

## Bundled References

- [Configuration Files](references/configuration-files.md): responsibilities and relationships of common formatter files.
- [Practical Examples](references/examples.md): response patterns for setup, review, troubleshooting, and CI questions.
- [Operating Workflow](references/operating-workflow.md): discovery, ownership, safe editing, validation, and troubleshooting matrix.
- [Standards Matrix](references/standards-matrix.md): language boundaries and configuration review questions.
