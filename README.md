# Claude Code Config Template — Android Development

A ready-to-use configuration template for [Claude Code](https://docs.anthropic.com/en/docs/claude-code) that enforces clean architecture, SOLID principles, and Android best practices in your AI-assisted development workflow.

## What's Inside

```
├── CLAUDE.md                    # Claude Code instructions (auto-loaded)
└── .claude/
    └── PROJECT_SPEC.md          # Project spec template (architecture, conventions, tech stack)
```

**`CLAUDE.md`** — The main instructions file. Claude Code reads this automatically. Contains:
- Build, test, and lint commands
- Architecture summary (Clean Architecture + MVVM)
- Code style rules (SOLID, no dead code, self-documenting)
- Testing requirements
- Behavioral rules (suggest better solutions, auto-build after changes)

**`.claude/PROJECT_SPEC.md`** — Fill-in template for your specific project. Covers:
- App overview, target SDK, tech stack
- Module and package structure
- Layer boundaries and ViewModel patterns
- Compose conventions, error handling, networking, performance
- Project-specific gotchas and fragile areas

## Quick Start

1. **Copy files into your Android project root:**
   ```bash
   git clone https://github.com/dmytroSamoilov/claude-code-android-template.git
   cp -r claude-code-android-template/{CLAUDE.md,.claude} /path/to/your/project/
   ```

2. **Fill in the placeholders** in both files:
   - `CLAUDE.md` — Replace `[APP NAME]` with your app name
   - `.claude/PROJECT_SPEC.md` — Fill in all `[bracketed]` sections with your project details

3. **Start Claude Code** in your project directory — it picks up `CLAUDE.md` automatically.

## Tech Stack (Default Template)

The template is pre-configured for a modern Android stack:

- **Kotlin** + **Jetpack Compose**
- **MVVM + Clean Architecture** (domain → data → presentation)
- **Koin** for dependency injection
- **Retrofit** / **Ktor** for networking
- **Room** / **DataStore** for persistence
- **Coroutines + Flow** for async
- **JUnit5 + MockK + Turbine** for testing

Swap any of these in the PROJECT_SPEC to match your project.

## Customization

This is a starting point. Adapt it to your needs:

- **Different DI?** Replace Koin references with Hilt/Dagger
- **Multi-module?** Update the package structure section
- **Different testing stack?** Adjust the testing section in both files
- **KMP project?** Restructure the architecture rules for shared modules

## Key Principles Enforced

- **Clean Architecture** — strict layer boundaries, no domain → Android dependencies
- **SOLID** — single responsibility, composition over inheritance
- **No dead code** — unused variables, speculative code, or code comments
- **Strings in resources** — no hardcoded strings, no duplicates
- **Build verification** — Claude runs `assembleDebug` after every change
- **Suggest-first** — Claude suggests better approaches before implementing

## Contributing

Found a way to improve the template? PRs welcome. Keep it opinionated but practical — this isn't meant to cover every possible setup, just provide a strong default.

## License

[GPL-3.0](LICENSE)
