# Claude Code Instructions
<!-- Version: 2 -->
Version: v2

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

## Security (MANDATORY)
Every solution you implement MUST be secure by default. Before finishing any change, mentally walk through this checklist against the code you wrote. If a rule conflicts with convenience, the rule wins — raise the concern, do not silently weaken security.

### Secrets & sensitive data
- NEVER hardcode API keys, tokens, passwords, signing keys, URLs of private endpoints, or any secret in source, `BuildConfig`, `strings.xml`, or comments. Load from `local.properties` (gitignored) → `BuildConfig` at build time, or from a secure backend.
- NEVER commit `.env`, `google-services.json` for prod, keystores, or `local.properties`. Verify `.gitignore` covers them before `git add`.
- NEVER log tokens, passwords, PII, full request/response bodies, auth headers, or device identifiers. `Timber`/`Log` in release builds must be stripped via ProGuard/R8 or guarded by `BuildConfig.DEBUG`.
- Passwords and tokens in memory: prefer `CharArray`/`ByteArray` over `String` and zero them after use. `String` is immutable and sits in the heap until GC.
- Sensitive data in `SharedPreferences` → use `EncryptedSharedPreferences` (Jetpack Security) or `DataStore` with encryption. Plaintext `SharedPreferences` is forbidden for anything sensitive.
- Tokens on disk → Android Keystore-backed keys only. Never store private keys in files or prefs.
- Do not put sensitive data in `Intent` extras that cross app boundaries, clipboard, notifications, or URL query strings.
- Screens showing sensitive data must set `FLAG_SECURE` to block screenshots and recents thumbnails.

### Networking
- HTTPS only. `android:usesCleartextTraffic="false"` and a restrictive `network_security_config.xml`. Never add a custom `TrustManager`/`HostnameVerifier` that accepts all certs — if SSL fails, fix the cert, don't bypass.
- Pin certificates for production APIs where feasible (OkHttp `CertificatePinner`).
- `OkHttp` logging interceptor: `Level.BODY` only in debug. Auth headers must be redacted via `redactHeader(...)`.
- Validate every server response before use. Never `!!` on network data. Treat all remote input as untrusted.
- Use Retrofit/Ktor with KotlinX Serialization (already in stack) — avoid reflection-based parsers on untrusted input.

### Input validation & injection
- Room: use `@Query` with parameterized arguments. NEVER build SQL via string concatenation or `SimpleSQLiteQuery` with interpolated input.
- Deep links / `Intent` extras / `Uri` params: validate scheme, host, path, and every parameter. Reject unknown. Sanitize before use in file paths, WebViews, or queries.
- File operations from external sources: canonicalize paths (`File.canonicalPath`) and verify they are inside an allowed directory to prevent path traversal (Zip-Slip, arbitrary write).
- Never pass untrusted input to `Runtime.exec`, `ProcessBuilder`, reflection, or dynamic class loading.

### Android components & IPC
- `AndroidManifest.xml`: `android:allowBackup="false"`, `android:debuggable` never set true manually, `android:exported` explicit on every activity/service/receiver/provider. Default to `exported="false"` unless there is a documented reason otherwise.
- Exported components must validate calling package/permission and sanitize all incoming `Intent` data.
- `ContentProvider`: enforce permissions, never return data for arbitrary URIs, use selection args (no string concat).
- `PendingIntent`: always use `FLAG_IMMUTABLE` (required on API 31+) unless mutability is essential and justified.
- Request the minimum set of runtime permissions. Do not add permissions speculatively.

### WebView
- Avoid WebView entirely if the task does not require it. If required:
  - `setJavaScriptEnabled(true)` only when necessary.
  - `setAllowFileAccess(false)`, `setAllowContentAccess(false)`, `setAllowFileAccessFromFileURLs(false)`, `setAllowUniversalAccessFromFileURLs(false)`.
  - `addJavascriptInterface` only with `@JavascriptInterface`-annotated methods, only for trusted origins, never exposing sensitive APIs.
  - Load only HTTPS URLs from an allowlist. Never load arbitrary URLs from intents.

### Cryptography
- Use `androidx.security.crypto` or `javax.crypto` with vetted parameters: AES-256-GCM (never ECB, never CBC without HMAC), RSA-OAEP, HMAC-SHA-256+. MD5 and SHA-1 are forbidden for security purposes.
- Keys → Android Keystore. Never hardcoded, never derived from constants, never checked in.
- Randomness for security → `SecureRandom`. Never `Random`, `Math.random()`, or `ThreadLocalRandom`.
- IV/nonce: unique per encryption, never reused with the same key, never hardcoded.
- Don't roll your own crypto. If unsure, ask before implementing.

### Authentication & sessions
- Use Android's `BiometricPrompt` via the AndroidX library, with `CryptoObject` when unlocking Keystore-backed keys.
- Session tokens: short-lived access + rotating refresh, stored in Keystore-wrapped storage. Clear on logout.
- Do not implement custom SSO/OAuth flows from scratch — use AppAuth or the provider SDK.

### Memory leaks (Android-specific, mandatory review)
Every change involving `Context`, coroutines, Compose, listeners, or long-lived objects must pass this check:
- **ViewModel** never holds `Activity`, `View`, `Context` (except `applicationContext` via Hilt/Koin), `NavController`, or any composable lambda. Breaking this rule leaks the Activity across rotation.
- **Coroutines**: use `viewModelScope` (ViewModel) or `lifecycleScope`/`repeatOnLifecycle` (UI). NEVER `GlobalScope`. NEVER `runBlocking` on main. Cancel custom `Job`s in `onCleared`/`onDispose`.
- **Flow collection** in Compose: `collectAsStateWithLifecycle()`, not `collectAsState()`, to stop collection when the screen is not visible.
- **Compose side effects**: `LaunchedEffect` keys must reflect real dependencies. `DisposableEffect` MUST clean up every listener/callback it registers in `onDispose`. `rememberUpdatedState` for captured values in long-lived effects.
- **Listeners, observers, callbacks, broadcast receivers, sensor/location listeners, `BroadcastReceiver`, `ContentObserver`**: always paired registration/unregistration tied to lifecycle. Never register in `onCreate` and unregister in `onDestroy` for things that should be `onStart`/`onStop`.
- **Static fields**: never hold `Context`, `View`, `Activity`, `Fragment`, or anything that transitively references them. Singletons only take `applicationContext`.
- **Inner classes / anonymous classes** inside Activity/Fragment that outlive the host → use `static` equivalent (top-level class or object) with `WeakReference` to the host.
- **Bitmaps, `Cursor`, `InputStream`, `FileChannel`, `MediaPlayer`, `Camera`, `Sensor`**: always `use { }` or `try/finally close()`. Never rely on GC.
- **Compose**: do not capture `Activity`/`Context` inside `remember { }`. Use `LocalContext.current` at read time, not stored.
- Run LeakCanary in debug builds; if adding a new long-lived holder, consider whether it is leak-prone and document ownership.

### Build & ship
- Release builds: `minifyEnabled true`, `shrinkResources true`, ProGuard/R8 rules that strip `Log` and keep crash-reporting line info.
- `debuggable false`, `allowBackup false`, `usesCleartextTraffic false` in release.
- Strip unused permissions, unused exported components, unused dependencies.
- Run `./gradlew lintDebug` — treat security lint warnings as errors.

### LLM-specific anti-patterns to actively avoid
These are mistakes AI assistants (including me) repeatedly make. Treat them as forbidden:
1. Bypassing an SSL error by trusting all certificates "to make it work."
2. Using `GlobalScope.launch` because it compiles without a scope parameter.
3. Catching `Exception` and swallowing it to silence a crash.
4. Storing a token in plaintext `SharedPreferences` "for now."
5. Putting an API key in `BuildConfig` read from a checked-in gradle constant.
6. `exported="true"` left as default on new components.
7. Forgetting `FLAG_IMMUTABLE` on `PendingIntent`.
8. Using `String` to hold a password.
9. Building a SQL `WHERE` clause via `"... = '$input'"`.
10. Passing `this@Activity` into a ViewModel constructor or repository.
11. `collectAsState()` instead of `collectAsStateWithLifecycle()`.
12. Logging the full HTTP request/response in a debug interceptor without redacting auth headers.
13. `setJavaScriptEnabled(true)` + `addJavascriptInterface` + URL from an `Intent` extra.
14. Using `Random` for a token, session ID, or nonce.
15. Adding `READ_EXTERNAL_STORAGE`/`MANAGE_EXTERNAL_STORAGE` when Scoped Storage / SAF / `MediaStore` would work.
16. Parsing a deep link `Uri` and passing parameters straight into a repository or `WebView.loadUrl`.
17. Registering a listener in a composable without a matching `DisposableEffect { onDispose { ... } }`.

If you are about to do any of the above, STOP, flag it, and propose the secure alternative before writing the code.