# Project Specification

## Overview
[APP NAME] is a [type] Android application that [main purpose].
Target audience: [who uses it]
Min SDK: [version] | Target SDK: [version]

## Tech Stack
- Language: Kotlin
- UI: Jetpack Compose 
- Architecture: MVVM + Clean Architecture
- DI: Koin
- Networking: Retrofit + KotlinX Serialization / Ktor
- Database: Room / DataStore
- Image loading: Coil / Glide
- Navigation: Compose Navigation
- Async: Kotlin Coroutines + Flow
- Testing: JUnit5 + MockK + Turbine

## Module Structure
[Description your modules and their responsibilities, Single module vs multi-module]

## Package Structure
```
app/src/main/java/com/[package]/
  domain/
    model/          # Domain entities
    usecase/        # Use cases (one per business action)
    repository/     # Repository interfaces
  data/
    repository/     # Repository implementations
    remote/         # API services, DTOs
    local/          # Room DAOs, entities
    mapper/         # Data <-> Domain mappers
  presentation/
    feature_name/
      FeatureScreen.kt
      FeatureViewModel.kt
      components/   # Screen-specific composables
    common/
      BaseViewModel.kt
      BaseComposeScreen.kt
      components/   # Shared UI components
  di/               # DI modules
```

# Architecture Rules 

## Layer Boundaries
- domain/ has ZERO Android dependencies
- data/ implements domain interfaces
- presentation/ depends on domain/ only (never on data/ directly)
- No circular dependencies between features

## ViewModel Pattern
- Every ViewModel extends BaseViewModel
- Expose single StateFlow<FeatureUiState>
- UiState is a data class with default values
- UiState contains ONLY content state (no loading/error)
- BaseViewModel provides: loading state, error handling, safe coroutine launching
- Use viewModelScope for all coroutines

## Compose Conventions
- State hoisting: composables receive state + callbacks, don't hold state
- Modifier: always first optional parameter
- @Preview for every screen composable
- Avoid side effects in composition — use LaunchedEffect/SideEffect
- No business logic in composables — delegate to ViewModel
- remember{} for expensive calculations only

## Error Handling
- Domain layer: use Result<T> or sealed class for expected errors
- Data layer: catch exceptions, map to domain errors
- Presentation: BaseViewModel catches and displays generic errors
- Network errors: show user-friendly message + retry option

## Networking
- Base URL in BuildConfig or DI module (not hardcoded)
- OkHttp interceptors for: logging (debug only), auth headers
- API response DTOs separate from domain models
- Always map DTO -> domain model in repository

## Performance
- No blocking calls on main thread
- Use loadAsync func for disk/network operations, BaseViewModel provided
- Lazy initialization for expensive objects
- Avoid unnecessary recompositions (stable types, remember)

## Gotchas / Never Touch
- [List files or modules that are fragile or have special handling]
- [Any workarounds that exist and why]
- [Third-party SDK quirks]