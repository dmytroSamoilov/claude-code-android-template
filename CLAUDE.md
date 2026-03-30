# Claude Code Instructions

## Project
This is [APP NAME]
See ./claude/@PROJECT_SPEC.md for full project overview, tech stack, and architecture.

## Behavior
- If you know a better solution (different API, library, pattern) — suggest it BEFORE implementing. Don't silently use a suboptimal approach.
- After every code change, run `./gradlew assembleDebug` and fix errors before finishing.
- When adding new files, auto `git add` them (only files related to current task).

## Build & Test
- Build: `./gradlew assembleDebug`
- Unit tests: `./gradlew testDebugUnitTest`
- Lint: `./gradlew lintDebug`
- Run all: `./gradlew assembleDebug testDebugUnitTest lintDebug`

## Architecture (summary)
- Three layers: domain/ → data/ → presentation/
- Use cases wrap repository calls. When adding a new use case, refactor all direct repo calls to use it.
- ViewModels expose UI state via StateFlow. No LiveData.
- Each screen: single UiState data class (content only — no isLoading/error, BaseViewModel handles those).
- UiState uses UI-specific models, not domain entities. Use mappers.
- All screens wrapped with BaseComposeScreen. All ViewModels extend BaseViewModel.
- Full architecture rules: /claude/@PROJECT_SPEC.md#Architecture

## Code Style
- SOLID principles — strictly enforced
- Composition over inheritance
- Functions: single responsibility, <20 lines
- No code comments — self-documenting through naming
- No dead code, unused variables, or speculative code
- Explicit imports only (no wildcards)
- Strings go in strings.xml — no hardcoded, no duplicates

## Testing
- Unit tests mandatory for all use cases with domain logic
- Pattern: Arrange-Act-Assert
- Mock external dependencies
- Tests: isolated, repeatable, fast

## Resources
- String names must have entries in strings.xml
- No duplicate string resources