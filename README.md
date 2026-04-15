# Claude Code Config Template — Android Development

A ready-to-use configuration template for [Claude Code](https://docs.anthropic.com/en/docs/claude-code) that enforces clean architecture, SOLID principles, Android best practices, and security-by-default in your AI-assisted development workflow.

## Install (30 seconds)

**Everything you need is inside the [`template/`](./template) folder. Copy its entire contents into the root of your Android project — nothing else.**

### Option A — Drag & drop (simplest)
1. Download or clone this repo.
2. Open the `template/` folder.
3. Select **all** contents (`CLAUDE.md` + the hidden `.claude/` folder) and drag them into your Android project's root directory.
   - macOS Finder: press `⌘ + Shift + .` to reveal hidden folders like `.claude/`.
   - Windows Explorer: enable "Show hidden files" in View options.
4. Open `CLAUDE.md` and `.claude/PROJECT_SPEC.md` and fill in the `[bracketed]` placeholders.
5. Start Claude Code in your project — it auto-loads `CLAUDE.md`.

### Option B — Command line
```bash
git clone https://github.com/dmytroSamoilov/claude-code-android-template.git
cp -R claude-code-android-template/template/. /path/to/your/android/project/
```
The trailing `/.` copies everything inside `template/` (including the hidden `.claude/` folder) without wrapping it in an extra directory.

That's it. No cherry-picking, no "which files do I need?" — the whole folder is the template.

## What Gets Copied

```
template/
├── CLAUDE.md                    # Claude Code instructions (auto-loaded)
└── .claude/
    └── PROJECT_SPEC.md          # Project spec template (architecture, conventions, tech stack)
```

**`CLAUDE.md`** — The main instructions file. Claude Code reads this automatically. Contains:
- Build, test, and lint commands
- Architecture summary (Clean Architecture + MVVM)
- Code style rules (SOLID, no dead code, self-documenting)
- Testing requirements
- **Security rules** (secrets, networking, crypto, IPC, WebView, memory-leak prevention, LLM anti-patterns)
- Behavioral rules (suggest better solutions, auto-build after changes)

**`.claude/PROJECT_SPEC.md`** — Fill-in template for your specific project. Covers:
- App overview, target SDK, tech stack
- Module and package structure
- Layer boundaries and ViewModel patterns
- Compose conventions, error handling, networking, performance
- Project-specific gotchas and fragile areas

## Repo Layout

```
.
├── README.md          # You are here — describes the repo
├── LICENSE            # GPL-3.0
└── template/          # ← the only folder you copy into your project
    ├── CLAUDE.md
    └── .claude/
        └── PROJECT_SPEC.md
```

Files at the repo root (`README.md`, `LICENSE`, `.gitignore`) are repo metadata — they do **not** belong in your Android project.

## Tech Stack (Default Template)

The template is pre-configured for a modern Android stack:

- **Kotlin** + **Jetpack Compose**
- **MVVM + Clean Architecture** (domain → data → presentation)
- **Koin** for dependency injection
- **Retrofit** / **Ktor** for networking
- **Room** / **DataStore** for persistence
- **Coroutines + Flow** for async
- **JUnit5 + MockK + Turbine** for testing

Swap any of these in `PROJECT_SPEC.md` to match your project.

## Customization

This is a starting point. Adapt it to your needs:

- **Different DI?** Replace Koin references with Hilt/Dagger
- **Multi-module?** Update the package structure section
- **Different testing stack?** Adjust the testing section in both files
- **KMP project?** Restructure the architecture rules for shared modules

## Key Principles Enforced

- **Clean Architecture** — strict layer boundaries, no domain → Android dependencies
- **SOLID** — single responsibility, composition over inheritance
- **Security by default** — hardcoded secrets forbidden, HTTPS-only, Keystore for sensitive data, memory-leak checklist, explicit LLM anti-pattern list
- **No dead code** — unused variables, speculative code, or code comments
- **Strings in resources** — no hardcoded strings, no duplicates
- **Build verification** — Claude runs `assembleDebug` after every change
- **Suggest-first** — Claude suggests better approaches before implementing

## Contributing

Found a way to improve the template? PRs welcome. Keep it opinionated but practical — this isn't meant to cover every possible setup, just provide a strong default.

## License

[GPL-3.0](LICENSE)
