---
name: flutter
description: "Flutter mobile app development for iOS and Android. Riverpod, Hooks, GoRouter, Retrofit, Dio, Freezed, ScreenUtil, flutter_gen, ARB localization, SharedPreferences, build_runner code generation. Actions: create, build, add, implement, fix, refactor, review, optimize, test, debug, deploy Flutter code. Projects: mobile app, iOS app, Android app, cross-platform app, Flutter project, pubspec.yaml, .dart files. Elements: screen, widget, provider, notifier, model, API client, route, theme, localization, form, list, bottom sheet, dialog. Patterns: feature-first architecture, code generation, state management, dependency injection, typed routing, responsive UI, pull-to-refresh, pagination, infinite scroll, search, auth guard, error handling, testing, caching, WebSocket, deep linking, accessibility. TRIGGER when the current project is a Flutter project (has pubspec.yaml with flutter dependency) or the user asks to create/work with a Flutter app."
user-invocable: true
---

# Flutter Mobile Development Skill

Opinionated Flutter architecture for iOS/Android apps. Feature-first structure, Riverpod+Hooks state management, code-generated everything.

---

## When to Apply

**Must Use** — always activate:
- Project has `pubspec.yaml` with `flutter` dependency
- Creating a new Flutter project
- Adding a screen, widget, provider, model, or API client
- Fixing or refactoring Flutter/Dart code
- Setting up routing, state management, or networking

**Recommended** — activate if context matches:
- Questions about state management in a mobile context
- Responsive UI for mobile screens
- Routing, navigation, or deep linking
- Code generation setup or troubleshooting

**Skip** — do not activate:
- Pure Dart CLI/server projects (no Flutter dependency)
- Flutter web-only projects (this skill targets mobile)
- User explicitly requests a different architecture (BLoC, GetX, MobX)
- Backend, API, infrastructure, or DevOps tasks

> **Decision rule**: If the task involves a `.dart` file inside a Flutter project → activate. If unsure → check for `pubspec.yaml` with `flutter` dependency.

---

## Priority Table

| Priority | Category | Impact | Rule IDs | Key Check |
|----------|----------|--------|----------|-----------|
| 1 | Code Generation | CRITICAL | `CG-1`..`CG-4` | build_runner after every model/provider/route change |
| 2 | State Management | CRITICAL | `SM-1`..`SM-6` | annotation-based providers, watch vs read |
| 3 | Testing | CRITICAL | `TT-1`..`TT-5` | unit tests for notifiers, widget tests with overrides |
| 4 | Project Structure | HIGH | `PS-1`..`PS-5` | feature-first, correct file placement |
| 5 | Networking & Models | HIGH | `NM-1`..`NM-5` | Retrofit + Freezed, no raw dio/json |
| 6 | Error Handling | HIGH | `EH-1`..`EH-4` | sealed exceptions, error mapping, user-friendly UI |
| 7 | Routing | HIGH | `RT-1`..`RT-4` | typed GoRouter, code-generated |
| 8 | Responsive UI | HIGH | `RU-1`..`RU-3` | every dimension uses ScreenUtil |
| 9 | Localization & Assets | MEDIUM | `LA-1`..`LA-4` | no hardcoded strings or asset paths |
| 10 | Theming & Styling | MEDIUM | `TS-1`..`TS-4` | AppColors/AppTheme, GoogleFonts |
| 11 | Performance | MEDIUM | `PF-1`..`PF-5` | no allocations in build, RepaintBoundary |
| 12 | Accessibility | MEDIUM | `AC-1`..`AC-3` | Semantics, tap targets, labels |
| 13 | Coding Style | LOW | `CS-1`..`CS-8` | const, trailing commas, SafeArea |

---

## Quick Reference

### Code Generation (CRITICAL)
- `CG-1` — Run `dart run build_runner build --delete-conflicting-outputs` after ANY file with `part '*.g.dart'` or `part '*.freezed.dart'`
- `CG-2` — Never manually edit `.g.dart` or `.freezed.dart` files
- `CG-3` — Always add `part` directives BEFORE running build_runner
- `CG-4` — Always use `--delete-conflicting-outputs` flag

### State Management (CRITICAL)
- `SM-1` — Always use class-based Notifier with `build()` method (`@riverpod class X extends _$X`), never functional providers or manual `Provider()` constructors
- `SM-2` — Screens with local state → `HookConsumerWidget`
- `SM-3` — Stateless widgets with providers → `ConsumerWidget`
- `SM-4` — `ref.watch()` in build for reactive UI, `ref.listen()` for side effects, `ref.read()` in callbacks only
- `SM-5` — Default auto-dispose (`@riverpod`); use `@Riverpod(keepAlive: true)` only for singletons
- `SM-6` — Handle async states with `.when()` or `.maybeWhen()`, never force-unwrap `.value!`

### Testing (CRITICAL)
- `TT-1` — Unit test every Notifier: test `build()` returns correct initial state + all public methods mutate state correctly
- `TT-2` — Widget tests use `ProviderScope(overrides: [...])` to inject mock providers, never depend on real API/storage
- `TT-3` — Use `mocktail` for mocking, never hand-roll mock classes
- `TT-4` — Test file mirrors source: `test/features/[name]/...` matches `lib/features/[name]/...`
- `TT-5` — Integration tests in `integration_test/`, one per critical user flow (auth, CRUD, navigation)

### Project Structure (HIGH)
- `PS-1` — Feature-first: `lib/features/[name]/presentation/{screens,widgets,controllers}`
- `PS-2` — Shared code: `lib/core/{models,network,providers,routing,theme,utils,widgets}`
- `PS-3` — Screen files: `[name]_screen.dart` → `[Name]Screen`
- `PS-4` — Widget files: `[feature]_[name]_widget.dart` → `[Feature][Name]Widget`
- `PS-5` — Feature-specific data layer optional: `features/[name]/data/{api,models}`

### Networking & Models (HIGH)
- `NM-1` — Retrofit abstract class for API definitions, Dio for HTTP client
- `NM-2` — Freezed for all data models, `@JsonKey(name:)` for snake_case mapping
- `NM-3` — API clients exposed as Riverpod providers, not service locators
- `NM-4` — Use `@Default([])` for list fields, avoid nullable lists
- `NM-5` — Dio interceptors for auth headers and error handling

### Error Handling (HIGH)
- `EH-1` — Sealed `AppException` via Freezed for all domain errors (network, server, auth, validation)
- `EH-2` — Dio interceptor maps `DioException` → `AppException` before it reaches providers
- `EH-3` — Notifiers catch raw exceptions and expose only typed `AppException` through `AsyncValue.error()`
- `EH-4` — UI error branch uses `AppErrorWidget` with localized user-friendly messages, never raw `e.toString()`

### Routing (HIGH)
- `RT-1` — GoRouter with typed routes + `with $RouteName` mixin on every route class
- `RT-2` — Navigation: `.push(context)` for forward, `.pushReplacement(context)` for replace, `context.pop()` for back. Never use `Navigator.*` or `.go()`
- `RT-3` — `StatefulShellRoute` for bottom navigation tabs
- `RT-4` — Router as Riverpod provider with redirect guards for auth

### Responsive UI (HIGH)
- `RU-1` — Every numeric dimension uses ScreenUtil: `.w`, `.h`, `.sp`, `.r`
- `RU-2` — Design base: 390x844 (iPhone)
- `RU-3` — Spacing: `N.verticalSpace` / `N.horizontalSpace`, not `SizedBox(height: N)`

### Localization & Assets (MEDIUM)
- `LA-1` — All user-facing strings via `context.l10n.*`, never hardcoded
- `LA-2` — ARB files in `lib/l10n/`, generated output in `lib/generated/l10n/`
- `LA-3` — Asset references via `Assets.*` (flutter_gen), never string paths
- `LA-4` — `context.l10n` extension in `core/utils/app_localizations_ext.dart`

### Theming & Styling (MEDIUM)
- `TS-1` — `AppColors` with private constructor, `static const` fields
- `TS-2` — `AppTheme` with private constructor, static getters for light/dark `ThemeData`
- `TS-3` — `GoogleFonts` for typography with `.sp` sizing
- `TS-4` — `.withValues(alpha: 0.X)` not deprecated `.withOpacity()`

### Performance (MEDIUM)
- `PF-1` — Never create objects (`TextStyle()`, `EdgeInsets()`, `BoxDecoration()`) inside `build()` — use `const` or static fields
- `PF-2` — Wrap expensive subtrees in `RepaintBoundary` (charts, maps, complex animations)
- `PF-3` — Use `AutomaticKeepAliveClientMixin` for TabBarView pages that should survive tab switches
- `PF-4` — Long lists always use `ListView.builder` / `SliverList`, never `Column(children: items.map(...))`
- `PF-5` — Thumbnail images: use `cacheWidth` / `cacheHeight` to limit decode size, not full-res decode

### Accessibility (MEDIUM)
- `AC-1` — Every interactive element (`GestureDetector`, `InkWell`, icon button) must have `Semantics` label or `Tooltip`
- `AC-2` — Minimum tap target: 48x48 logical pixels (Material guideline)
- `AC-3` — Decorative images use `excludeFromSemantics: true`, meaningful images get `semanticLabel`

### Coding Style (LOW)
- `CS-1` — Always `const` constructors where possible
- `CS-2` — Always trailing commas
- `CS-3` — Wrap main content in `SafeArea`
- `CS-4` — Prefer `CrossAxisAlignment.stretch` in Column
- `CS-5` — Decompose screens into feature-prefixed widget files
- `CS-6` — `Platform.isIOS` / `Platform.isAndroid` for platform-specific behavior
- `CS-7` — Pure Riverpod DI, no GetIt or other service locators
- `CS-8` — `ProviderScope` wraps root widget in `main()`

---

## Workflow

### Creating a New Feature

1. **Identify placement** — New feature → `lib/features/[name]/`. Check if shared models/APIs already exist in `core/`.
2. **Create models** — Freezed classes in `core/models/` (shared) or `features/[name]/data/models/` (feature-specific). Add `part` directives.
3. **Create error types** — If the feature has domain-specific errors, add factory constructors to `AppException` or create feature-scoped sealed error class.
4. **Create API layer** — Retrofit abstract class + Riverpod provider to expose the API client.
5. **Create providers** — Annotation-based providers in `features/[name]/presentation/controllers/`. Wire up API calls and state. Catch exceptions and map to `AppException`.
6. **Create screen** — `HookConsumerWidget` in `features/[name]/presentation/screens/`. Hooks for local state, `ref.watch()` for providers.
7. **Extract widgets** — Break large trees into `features/[name]/presentation/widgets/`. Prefix with feature name.
8. **Add route** — Typed GoRoute class, register in router.
9. **Add localization** — New keys in ARB files for user-facing strings.
10. **Run build_runner** — `dart run build_runner build --delete-conflicting-outputs`. ALWAYS.
11. **Write tests** — Unit tests for every Notifier, widget tests for screens with provider overrides.
12. **Verify** — `dart analyze` for errors, `flutter test` for regressions.

### Modifying Existing Code

1. **Read first** — Understand the existing feature structure before changing anything.
2. **Follow existing patterns** — Do not introduce new architectural patterns (e.g., BLoC into a Riverpod project).
3. **Run build_runner** — If any generated files are affected.
4. **Update tests** — If behavior changed, update existing tests. If new behavior added, add new tests.
5. **Verify** — `dart analyze` + `flutter test`.

---

## Project Structure

```
lib/
├── core/                          # Shared/global layer
│   ├── errors/                    # Error handling
│   │   ├── app_exception.dart     # Sealed AppException (Freezed)
│   │   ├── app_exception.freezed.dart
│   │   └── error_mapper.dart      # DioException → AppException mapper
│   ├── models/                    # Shared data models (Freezed)
│   │   └── [entity]/
│   │       ├── [entity].dart
│   │       ├── [entity].freezed.dart
│   │       └── [entity].g.dart
│   ├── network/
│   │   └── api/
│   │       └── [service]/
│   │           ├── [service]_api.dart       # Retrofit abstract class
│   │           └── [service]_api.g.dart
│   ├── providers/                 # Global DI & state providers (Riverpod)
│   │   ├── dio_provider.dart      # Dio HTTP client setup
│   │   ├── storage_provider.dart  # SharedPreferences provider
│   │   └── ...
│   ├── routing/
│   │   ├── app_router.dart        # GoRouter with typed routes
│   │   └── app_router.g.dart
│   ├── services/                  # Singleton services (analytics, etc.)
│   │   └── [service_name]/
│   ├── theme/
│   │   ├── app_theme.dart         # ThemeData configuration
│   │   └── app_colors.dart        # Color palette (private constructor class)
│   ├── utils/
│   │   ├── constants.dart         # API URLs, keys, config
│   │   ├── app_localizations_ext.dart  # context.l10n extension
│   │   └── ...
│   └── widgets/                   # Shared reusable widgets
│       └── app_error_widget.dart  # Unified error display widget
├── features/                      # Feature modules
│   └── [feature_name]/
│       ├── data/                  # (optional) Feature-specific data layer
│       │   ├── api/
│       │   └── models/
│       └── presentation/
│           ├── controllers/       # Feature Riverpod providers
│           │   ├── [name]_provider.dart
│           │   └── [name]_provider.g.dart
│           ├── screens/           # Full-page screens
│           │   └── [name]_screen.dart
│           └── widgets/           # Feature-scoped widgets
│               └── [feature]_[widget_name]_widget.dart
├── gen/                           # Generated assets (flutter_gen)
│   └── assets.gen.dart
├── generated/                     # Generated localizations
│   └── l10n/
├── l10n/                          # ARB localization source files
│   └── app_[locale].arb
└── main.dart                      # App entry point

test/                              # Unit & widget tests (mirrors lib/)
├── core/
│   └── errors/
│       └── error_mapper_test.dart
├── features/
│   └── [feature_name]/
│       └── presentation/
│           ├── controllers/
│           │   └── [name]_provider_test.dart
│           └── screens/
│               └── [name]_screen_test.dart
├── helpers/                       # Shared test utilities
│   ├── mocks.dart                 # Mocktail mock classes
│   ├── pump_app.dart              # Helper to wrap widget in MaterialApp + ProviderScope
│   └── fakes.dart                 # Fake data factories
└── flutter_test_config.dart       # Global test config (optional)

integration_test/                  # End-to-end tests
└── [flow_name]_test.dart
```

### Naming Conventions

| Type | File | Class |
|------|------|-------|
| Screen | `[name]_screen.dart` | `[Name]Screen` |
| Widget | `[feature]_[name]_widget.dart` | `[Feature][Name]Widget` |
| Provider | `[name]_provider.dart` | `[Name]Provider` / `[Name]Notifier` |
| Model | `[name].dart` | `[Name]` (Freezed) |
| API | `[service]_api.dart` | `[Service]Api` (Retrofit) |
| Exception | `app_exception.dart` | `AppException` (Freezed sealed) |
| Test | `[name]_test.dart` | mirrors source file name |
| Mock | `mocks.dart` | `Mock[ClassName]` (Mocktail) |

---

## Tech Stack Reference

### Code Generation — build_runner

All generators managed via `build_runner`. After creating or modifying any file with `part '*.g.dart'` or `part '*.freezed.dart'`:

```bash
dart run build_runner build --delete-conflicting-outputs
```

### State Management — Riverpod + Hooks

**Widget types:**

```dart
// Screen with hooks — preferred for screens
class HomeScreen extends HookConsumerWidget {
  const HomeScreen({super.key});

  @override
  Widget build(BuildContext context, WidgetRef ref) {
    final scrollController = useScrollController();
    final someState = useState(false);

    useEffect(() {
      void listener() { /* ... */ }
      scrollController.addListener(listener);
      return () => scrollController.removeListener(listener);
    }, [scrollController]);

    final data = ref.watch(someProvider);

    return /* widget tree */;
  }
}

// Simple widget without hooks
class SomeWidget extends ConsumerWidget {
  const SomeWidget({super.key});

  @override
  Widget build(BuildContext context, WidgetRef ref) {
    return /* widget tree */;
  }
}
```

**Provider patterns** — always class-based Notifier with `build()` method:

```dart
import 'package:riverpod_annotation/riverpod_annotation.dart';

part '[filename].g.dart';

// Auto-dispose provider (default)
@riverpod
class SomeProvider extends _$SomeProvider {
  @override
  SomeType build() {
    return /* ... */;
  }
}

// Keep-alive provider (singleton)
@Riverpod(keepAlive: true)
class SomeNotifier extends _$SomeNotifier {
  @override
  SomeType build() {
    return /* initial state */;
  }

  void doSomething() {
    state = /* new state */;
  }
}

// Async provider
@Riverpod(keepAlive: true)
class AppStorage extends _$AppStorage {
  @override
  Future<bool> build() async {
    final prefs = await ref.watch(sharedPreferencesProvider.future);
    return prefs.getBool('key') ?? false;
  }

  Future<void> setValue(bool value) async {
    final prefs = await ref.read(sharedPreferencesProvider.future);
    await prefs.setBool('key', value);
    state = AsyncValue.data(value);
  }
}

// Provider with parameters (family)
@Riverpod(keepAlive: true)
class SomeData extends _$SomeData {
  @override
  FutureOr<SomeResult> build(String param) async {
    // ...
  }
}
```

### Dependency Injection — Pure Riverpod

No GetIt or service locators. All services exposed through providers:

```dart
// Dio HTTP client
@riverpod
class DioClient extends _$DioClient {
  @override
  Dio build() {
    final dio = Dio();
    dio.options = BaseOptions(
      baseUrl: baseUrl,
      connectTimeout: Duration(seconds: 5),
      receiveTimeout: Duration(seconds: 100),
    );
    dio.interceptors.addAll([
      AppErrorInterceptor(ref),
      InterceptorsWrapper(
        onRequest: (options, handler) {
          // Add auth headers
          handler.next(options);
        },
      ),
      PrettyDioLogger(),
    ]);
    return dio;
  }
}

// API client depends on Dio
@riverpod
class SomeApiClient extends _$SomeApiClient {
  @override
  SomeApi build() {
    final dio = ref.watch(dioClientProvider);
    return SomeApi(dio);
  }
}
```

### Networking — Retrofit + Dio

```dart
import 'package:dio/dio.dart';
import 'package:retrofit/retrofit.dart';

part '[service]_api.g.dart';

@RestApi(baseUrl: '/api/v2/[service]')
abstract class SomeApi {
  factory SomeApi(Dio dio, {String? baseUrl}) = _SomeApi;

  @GET('/items')
  Future<List<Item>> getItems(@Query('lang') String? lang);

  @POST('/items')
  Future<Item> createItem(@Body() CreateItemRequest request);

  @POST('/upload')
  Future<Item> upload(
    @Part(name: 'files') List<MultipartFile> files,
    @Query('lang') String? lang,
  );
}
```

### Models — Freezed + JSON Serializable

```dart
import 'package:freezed_annotation/freezed_annotation.dart';

part '[model].freezed.dart';
part '[model].g.dart';

@freezed
abstract class SomeModel with _$SomeModel {
  const factory SomeModel({
    required String id,
    required String name,
    @JsonKey(name: 'created_at') required String createdAt,
    String? description,
    @Default([]) List<String> tags,
  }) = _SomeModel;

  factory SomeModel.fromJson(Map<String, dynamic> json) =>
      _$SomeModelFromJson(json);
}
```

### Error Handling — Sealed AppException

```dart
// core/errors/app_exception.dart
import 'package:freezed_annotation/freezed_annotation.dart';

part 'app_exception.freezed.dart';

@freezed
sealed class AppException with _$AppException implements Exception {
  /// No internet or timeout
  const factory AppException.network({String? message}) = NetworkException;

  /// Server returned 5xx
  const factory AppException.server({required int code, String? message}) = ServerException;

  /// 401 — token expired or invalid
  const factory AppException.unauthorized() = UnauthorizedException;

  /// 422 — field-level validation errors from server
  const factory AppException.validation({
    required Map<String, String> fields,
  }) = ValidationException;

  /// 404 — resource not found
  const factory AppException.notFound({String? resource}) = NotFoundException;

  /// Catch-all for unexpected errors
  const factory AppException.unknown({Object? error}) = UnknownException;
}

// core/errors/error_mapper.dart
AppException mapDioException(DioException e) {
  if (e.type == DioExceptionType.connectionTimeout ||
      e.type == DioExceptionType.receiveTimeout ||
      e.type == DioExceptionType.connectionError) {
    return const AppException.network(message: 'Connection failed');
  }

  final statusCode = e.response?.statusCode;
  final data = e.response?.data;

  return switch (statusCode) {
    401 => const AppException.unauthorized(),
    404 => AppException.notFound(resource: e.requestOptions.path),
    422 => AppException.validation(
        fields: (data is Map<String, dynamic>)
            ? (data['errors'] as Map<String, dynamic>?)
                    ?.map((k, v) => MapEntry(k, v.toString())) ??
                {}
            : {},
      ),
    >= 500 && < 600 => AppException.server(
        code: statusCode!,
        message: data is Map ? data['message']?.toString() : null,
      ),
    _ => AppException.unknown(error: e),
  };
}

// Dio interceptor that maps errors
class AppErrorInterceptor extends Interceptor {
  AppErrorInterceptor(this.ref);
  final Ref ref;

  @override
  void onError(DioException err, ErrorInterceptorHandler handler) {
    final mapped = mapDioException(err);

    // Auto-logout on 401
    if (mapped is UnauthorizedException) {
      ref.read(authProvider.notifier).logout();
    }

    handler.reject(DioException(
      requestOptions: err.requestOptions,
      error: mapped,
      type: err.type,
      response: err.response,
    ));
  }
}
```

**User-friendly error widget:**

```dart
// core/widgets/app_error_widget.dart
class AppErrorWidget extends StatelessWidget {
  const AppErrorWidget({super.key, required this.error, this.onRetry});

  final Object error;
  final VoidCallback? onRetry;

  @override
  Widget build(BuildContext context) {
    final message = switch (error) {
      NetworkException() => context.l10n.errorNetwork,
      ServerException() => context.l10n.errorServer,
      UnauthorizedException() => context.l10n.errorUnauthorized,
      NotFoundException() => context.l10n.errorNotFound,
      ValidationException(:final fields) => fields.values.join('\n'),
      AppException() => context.l10n.errorUnknown,
      _ => context.l10n.errorUnknown,
    };

    return Center(
      child: Padding(
        padding: EdgeInsets.all(24.w),
        child: Column(
          mainAxisSize: MainAxisSize.min,
          children: [
            Assets.icons.error.svg(width: 80.w, height: 80.h),
            16.verticalSpace,
            Text(
              message,
              textAlign: TextAlign.center,
              style: GoogleFonts.inter(fontSize: 14.sp, color: AppColors.textSecondary),
            ),
            if (onRetry != null) ...[
              24.verticalSpace,
              ElevatedButton(
                onPressed: onRetry,
                child: Text(context.l10n.retry),
              ),
            ],
          ],
        ),
      ),
    );
  }
}

// Usage in .when() error branch:
someAsyncValue.when(
  loading: () => const Center(child: CircularProgressIndicator()),
  error: (e, st) => AppErrorWidget(
    error: e,
    onRetry: () => ref.invalidate(someProvider),
  ),
  data: (data) => /* ... */,
)
```

### Testing — Unit, Widget, Integration

**Test helpers:**

```dart
// test/helpers/mocks.dart
import 'package:mocktail/mocktail.dart';

class MockSomeApi extends Mock implements SomeApi {}
class MockSharedPreferences extends Mock implements SharedPreferences {}

// test/helpers/pump_app.dart
extension PumpApp on WidgetTester {
  Future<void> pumpApp(
    Widget widget, {
    List<Override> overrides = const [],
  }) async {
    await pumpWidget(
      ProviderScope(
        overrides: overrides,
        child: MaterialApp(
          localizationsDelegates: AppLocalizations.localizationsDelegates,
          supportedLocales: AppLocalizations.supportedLocales,
          home: widget,
        ),
      ),
    );
  }
}

// test/helpers/fakes.dart
Item fakeItem({String? id, String? name}) => Item(
  id: id ?? 'test-id',
  name: name ?? 'Test Item',
  createdAt: '2026-01-01',
);
```

**Unit testing a Notifier:**

```dart
// test/features/items/presentation/controllers/items_provider_test.dart
import 'package:flutter_test/flutter_test.dart';
import 'package:hooks_riverpod/hooks_riverpod.dart';
import 'package:mocktail/mocktail.dart';

void main() {
  late MockSomeApi mockApi;
  late ProviderContainer container;

  setUp(() {
    mockApi = MockSomeApi();
    container = ProviderContainer(
      overrides: [
        someApiClientProvider.overrideWithValue(mockApi),
      ],
    );
    addTearDown(container.dispose);
  });

  group('ItemsListNotifier', () {
    test('build() fetches initial page', () async {
      when(() => mockApi.getItems(page: 0, limit: 20))
          .thenAnswer((_) async => [fakeItem()]);

      final items = await container.read(itemsListProvider.future);

      expect(items, hasLength(1));
      expect(items.first.id, 'test-id');
      verify(() => mockApi.getItems(page: 0, limit: 20)).called(1);
    });

    test('loadMore() appends next page', () async {
      when(() => mockApi.getItems(page: any(named: 'page'), limit: 20))
          .thenAnswer((_) async => [fakeItem()]);

      await container.read(itemsListProvider.future);
      await container.read(itemsListProvider.notifier).loadMore();

      final items = container.read(itemsListProvider).valueOrNull;
      expect(items, hasLength(2));
    });

    test('build() maps DioException to AppException', () async {
      when(() => mockApi.getItems(page: 0, limit: 20))
          .thenThrow(DioException(
            requestOptions: RequestOptions(),
            type: DioExceptionType.connectionTimeout,
          ));

      final state = container.read(itemsListProvider);

      // After awaiting the future, state should be error
      await expectLater(
        container.read(itemsListProvider.future),
        throwsA(isA<NetworkException>()),
      );
    });
  });
}
```

**Widget testing with provider overrides:**

```dart
// test/features/items/presentation/screens/items_screen_test.dart
import 'package:flutter_test/flutter_test.dart';
import 'package:hooks_riverpod/hooks_riverpod.dart';

void main() {
  group('ItemsScreen', () {
    testWidgets('shows loading indicator while fetching', (tester) async {
      await tester.pumpApp(
        const ItemsScreen(),
        overrides: [
          itemsListProvider.overrideWith(
            () => ItemsListNotifier()..state = const AsyncValue.loading(),
          ),
        ],
      );

      expect(find.byType(CircularProgressIndicator), findsOneWidget);
    });

    testWidgets('shows items when loaded', (tester) async {
      await tester.pumpApp(
        const ItemsScreen(),
        overrides: [
          itemsListProvider.overrideWith(
            () => ItemsListNotifier()
              ..state = AsyncValue.data([fakeItem(name: 'Hello')]),
          ),
        ],
      );
      await tester.pumpAndSettle();

      expect(find.text('Hello'), findsOneWidget);
    });

    testWidgets('shows error widget on failure', (tester) async {
      await tester.pumpApp(
        const ItemsScreen(),
        overrides: [
          itemsListProvider.overrideWith(
            () => ItemsListNotifier()
              ..state = AsyncValue.error(
                const AppException.network(),
                StackTrace.current,
              ),
          ),
        ],
      );
      await tester.pumpAndSettle();

      expect(find.byType(AppErrorWidget), findsOneWidget);
    });
  });
}
```

### Routing — GoRouter (typed, code-generated)

```dart
import 'package:go_router/go_router.dart';
import 'package:riverpod_annotation/riverpod_annotation.dart';

part 'app_router.g.dart';

// Shell route with bottom navigation (tabs)
@TypedStatefulShellRoute<AppShellRoute>(
  branches: [
    TypedStatefulShellBranch(routes: [TypedGoRoute<HomeRoute>(path: '/home')]),
    TypedStatefulShellBranch(routes: [TypedGoRoute<SearchRoute>(path: '/search')]),
    TypedStatefulShellBranch(routes: [TypedGoRoute<ProfileRoute>(path: '/profile')]),
  ],
)
class AppShellRoute extends StatefulShellRouteData {
  @override
  Widget builder(BuildContext context, GoRouterState state, StatefulNavigationShell navigationShell) {
    return Scaffold(
      body: navigationShell,
      bottomNavigationBar: BottomNavigationBar(
        currentIndex: navigationShell.currentIndex,
        items: [/* items */],
        onTap: (index) {
          navigationShell.goBranch(index, initialLocation: index == navigationShell.currentIndex);
        },
      ),
    );
  }
}

// Tab routes — always use `with $RouteName` mixin
class HomeRoute extends GoRouteData with $HomeRoute {
  @override
  Widget build(BuildContext context, GoRouterState state) => const HomeScreen();
}

// Standalone routes with parameters
@TypedGoRoute<DetailRoute>(path: '/detail')
class DetailRoute extends GoRouteData with $DetailRoute {
  const DetailRoute({required this.id});
  final String id;

  @override
  Widget build(BuildContext context, GoRouterState state) => DetailScreen(id: id);
}

// Router provider
@riverpod
class Router extends _$Router {
  @override
  GoRouter build() {
    return GoRouter(
      initialLocation: '/home',
      routes: $appRoutes,
      redirect: (context, state) async {
        // Auth/onboarding guards
        return null;
      },
    );
  }
}
```

**Navigation usage — GoRouter only, never Navigator.*:**

```dart
DetailRoute(id: '123').push(context);              // Forward navigation
CoinRoute(id: id).pushReplacement(context);        // Replace current screen
context.pop();                                      // Back navigation
```

### Responsive UI — FlutterScreenUtil

Design base: **390x844** (iPhone). Initialize in root widget:

```dart
ScreenUtilInit(
  designSize: const Size(390, 844),
  minTextAdapt: true,
  splitScreenMode: true,
  child: /* MaterialApp */,
)
```

**Always use ScreenUtil extensions:**

```dart
width: 100.w,          // responsive width
height: 60.h,          // responsive height
fontSize: 16.sp,       // responsive font size
borderRadius: BorderRadius.circular(16.r),  // responsive radius

50.verticalSpace,      // SizedBox(height: 50.h)
12.horizontalSpace,    // SizedBox(width: 12.w)

EdgeInsets.symmetric(horizontal: 20.w, vertical: 12.h)
EdgeInsets.all(10.sp)
```

### Theming & Styling

```dart
// Color palette — private constructor, static const
class AppColors {
  AppColors._();

  static const Color primary = Color(0xFF...);
  static const Color secondary = Color(0xFF...);
  static const Color background = Color(0xFF...);
  static const Color surface = Color(0xFF...);
  static const Color textPrimary = Color(0xFF...);
  static const Color textSecondary = Color(0xFF...);
}

// Theme — private constructor, static getters
class AppTheme {
  AppTheme._();

  static ThemeData get dark => ThemeData(
    useMaterial3: true,
    brightness: Brightness.dark,
    scaffoldBackgroundColor: AppColors.background,
    colorScheme: ColorScheme.dark(
      primary: AppColors.primary,
      secondary: AppColors.secondary,
      surface: AppColors.surface,
    ),
    appBarTheme: const AppBarTheme(
      backgroundColor: Colors.transparent,
      elevation: 0,
      surfaceTintColor: Colors.transparent,
    ),
    pageTransitionsTheme: const PageTransitionsTheme(
      builders: {
        TargetPlatform.iOS: CupertinoPageTransitionsBuilder(),
        TargetPlatform.android: OpenUpwardsPageTransitionsBuilder(),
      },
    ),
  );
}

// Typography — GoogleFonts with ScreenUtil
Text(
  'Title',
  style: GoogleFonts.epilogue(
    fontSize: 34.sp,
    fontWeight: FontWeight.w900,
    color: AppColors.textPrimary,
  ),
),
```

### Asset Management — flutter_gen

```dart
// Images
Assets.images.splash.image(height: 250.h)

// SVG icons
Assets.icons.home.svg(width: 24.w, height: 24.h)
Assets.icons.home.svg(color: AppColors.primary)
```

Never use string-based asset paths. Always `Assets.*`.

### Localization — ARB

Setup in `l10n.yaml`:

```yaml
arb-dir: lib/l10n
template-arb-file: app_en.arb
output-localization-file: app_localizations.dart
output-dir: lib/generated/l10n
synthetic-package: false
```

Extension for convenient access:

```dart
// core/utils/app_localizations_ext.dart
extension AppLocalizationsExt on BuildContext {
  AppLocalizations get l10n => AppLocalizations.of(this)!;
}

// Usage
Text(context.l10n.home)
Text(context.l10n.settings)
```

### Local Storage — SharedPreferences

```dart
@Riverpod(keepAlive: true)
class SharedPrefs extends _$SharedPrefs {
  @override
  Future<SharedPreferences> build() async {
    return SharedPreferences.getInstance();
  }
}

@Riverpod(keepAlive: true)
class SomeSettings extends _$SomeSettings {
  @override
  FutureOr<String> build() async {
    final prefs = await ref.read(sharedPreferencesProvider.future);
    return prefs.getString('key') ?? 'default';
  }

  Future<void> setValue(String value) async {
    final prefs = await ref.read(sharedPreferencesProvider.future);
    await prefs.setString('key', value);
    state = AsyncValue.data(value);
  }
}
```

### Secure Storage — flutter_secure_storage

Use for tokens, keys, and sensitive data. **Never store auth tokens in SharedPreferences.**

```dart
@Riverpod(keepAlive: true)
class SecureStorage extends _$SecureStorage {
  static const _storage = FlutterSecureStorage();

  @override
  FlutterSecureStorage build() => _storage;

  Future<String?> getToken() => _storage.read(key: 'auth_token');

  Future<void> setToken(String token) =>
      _storage.write(key: 'auth_token', value: token);

  Future<void> clearToken() => _storage.delete(key: 'auth_token');
}
```

### App Entry Point

```dart
void main() async {
  WidgetsFlutterBinding.ensureInitialized();
  await SharedPreferences.getInstance();

  runApp(ProviderScope(child: const MyApp()));
}

class MyApp extends ConsumerWidget {
  const MyApp({super.key});

  @override
  Widget build(BuildContext context, WidgetRef ref) {
    final router = ref.watch(routerProvider);
    final locale = ref.watch(selectedLocaleProvider);

    return ScreenUtilInit(
      designSize: const Size(390, 844),
      minTextAdapt: true,
      splitScreenMode: true,
      child: MaterialApp.router(
        routerConfig: router,
        themeMode: ThemeMode.dark,
        theme: AppTheme.light,
        darkTheme: AppTheme.dark,
        locale: locale,
        localizationsDelegates: AppLocalizations.localizationsDelegates,
        supportedLocales: AppLocalizations.supportedLocales,
      ),
    );
  }
}
```

### Environment Configuration

```dart
// main.dart
try {
  await dotenv.load(fileName: '.env');
} catch (_) {
  // env optional
}

// Usage
final apiKey = dotenv.env['API_KEY'] ?? '';
```

Add `.env` to `assets` in `pubspec.yaml` and to `.gitignore`. Provide `.env.example` with empty values.

---

## Anti-Patterns

### Architecture
| ID | Wrong | Right |
|----|-------|-------|
| `ANTI-01` | `StatefulWidget` + `setState` for shared state | Riverpod provider/notifier |
| `ANTI-02` | `GetIt`, `get_it`, or any service locator | Riverpod providers for DI |
| `ANTI-03` | BLoC, GetX, MobX patterns in Riverpod project | Stick to Riverpod |
| `ANTI-04` | Feature-specific code in `core/` | `features/[name]/data/` |
| `ANTI-05` | Dump-all `utils.dart` with unrelated functions | Split by purpose into separate files |
| `ANTI-06` | Circular provider dependencies | Restructure dependency graph |

### State Management
| ID | Wrong | Right |
|----|-------|-------|
| `ANTI-07` | `ref.read()` inside `build()` | `ref.watch()` for reactive UI |
| `ANTI-08` | `ref.watch()` inside `onPressed` / callbacks | `ref.read()` in callbacks |
| `ANTI-09` | `.value!` on async provider | `.when()` / `.maybeWhen()` |
| `ANTI-10` | Manual `StateProvider()`, `StateNotifierProvider()` | `@riverpod` / `@Riverpod` class-based Notifier |
| `ANTI-10b` | Functional providers `someProvider(Ref ref)` | Class-based `class X extends _$X` with `build()` |
| `ANTI-11` | Business logic inside widget `build()` | Logic in providers/notifiers |
| `ANTI-12` | `ChangeNotifier` | Riverpod `Notifier` with code-gen |

### UI
| ID | Wrong | Right |
|----|-------|-------|
| `ANTI-13` | Hardcoded `16` for font size | `16.sp` (ScreenUtil) |
| `ANTI-14` | `SizedBox(height: 20)` for spacing | `20.verticalSpace` |
| `ANTI-15` | `'assets/images/logo.png'` string path | `Assets.images.logo.image()` |
| `ANTI-16` | `Text('Hello')` hardcoded string | `Text(context.l10n.hello)` |
| `ANTI-17` | `.withOpacity(0.5)` | `.withValues(alpha: 0.5)` |
| `ANTI-18` | Widget tree deeper than 5 levels | Extract into separate widget files |
| `ANTI-19` | `MediaQuery.of(context).size.width` | ScreenUtil `.w` / `.h` |

### Networking
| ID | Wrong | Right |
|----|-------|-------|
| `ANTI-20` | `dio.get('/items')` raw calls | Retrofit abstract class |
| `ANTI-21` | `json['name']` manual parsing | Freezed `fromJson` |
| `ANTI-22` | Missing `@JsonKey` for snake_case fields | `@JsonKey(name: 'field_name')` |
| `ANTI-23` | `Dio()` created inside widget | Dio as Riverpod provider |

### Routing
| ID | Wrong | Right |
|----|-------|-------|
| `ANTI-27` | `Navigator.push()`, `Navigator.pop()` | GoRouter `.push(context)`, `context.pop()` |
| `ANTI-28` | `.go(context)` (clears entire stack) | `.push(context)` or `.pushReplacement(context)` |
| `ANTI-29` | Route class without `with $RouteName` mixin | `class XRoute extends GoRouteData with $XRoute` |

### Code Generation
| ID | Wrong | Right |
|----|-------|-------|
| `ANTI-24` | Forgetting `build_runner` after model/provider/route change | Always run after changes |
| `ANTI-25` | Manually editing `.g.dart` / `.freezed.dart` | Edit source, regenerate |
| `ANTI-26` | Missing `part` directive before build_runner | Add `part` first, then generate |

### Error Handling
| ID | Wrong | Right |
|----|-------|-------|
| `ANTI-30` | `catch (e) { print(e); }` — swallowing errors | Catch, map to `AppException`, propagate |
| `ANTI-31` | `Text(e.toString())` — raw exception to user | `AppErrorWidget` with localized messages |
| `ANTI-32` | Untyped `catch (e)` without mapping | `on DioException catch (e) { throw mapDioException(e); }` |
| `ANTI-33` | Auth tokens in `SharedPreferences` | Use `flutter_secure_storage` for tokens/secrets |

### Testing
| ID | Wrong | Right |
|----|-------|-------|
| `ANTI-34` | No tests / "will add tests later" | Write tests alongside feature code |
| `ANTI-35` | Testing internal state (checking private fields) | Test behavior: inputs → outputs |
| `ANTI-36` | Mocking everything including models and value objects | Only mock boundaries: API clients, storage, services |
| `ANTI-37` | Tests depend on real network / real storage | `ProviderScope(overrides: [...])` with mocks |

### Performance
| ID | Wrong | Right |
|----|-------|-------|
| `ANTI-38` | `Column(children: items.map((i) => ...).toList())` for 50+ items | `ListView.builder` / `SliverList` |
| `ANTI-39` | `TextStyle(...)` / `BoxDecoration(...)` created inside `build()` | `const` or `static` fields |
| `ANTI-40` | Full-size image decode for 40x40 thumbnail | `Image.asset(..., cacheWidth: 80)` (2x for retina) |

---

## Common Patterns

### 1. Async Data Screen (Loading / Error / Data)

```dart
class ItemsScreen extends HookConsumerWidget {
  const ItemsScreen({super.key});

  @override
  Widget build(BuildContext context, WidgetRef ref) {
    final items = ref.watch(itemsProvider);

    return Scaffold(
      appBar: AppBar(title: Text(context.l10n.items)),
      body: items.when(
        loading: () => const Center(child: CircularProgressIndicator()),
        error: (e, st) => AppErrorWidget(
          error: e,
          onRetry: () => ref.invalidate(itemsProvider),
        ),
        data: (data) => ListView.builder(
          padding: EdgeInsets.symmetric(horizontal: 16.w, vertical: 12.h),
          itemCount: data.length,
          itemBuilder: (context, index) => ItemTile(item: data[index]),
        ),
      ),
    );
  }
}
```

### 2. Pull-to-Refresh

```dart
RefreshIndicator(
  onRefresh: () => ref.refresh(itemsProvider.future),
  child: ListView.builder(/* ... */),
)
```

### 3. Infinite Scroll / Cursor-Based Pagination

```dart
// Paginated response model
@freezed
abstract class PaginatedResponse<T> with _$PaginatedResponse<T> {
  const factory PaginatedResponse({
    required List<T> items,
    String? nextCursor,
  }) = _PaginatedResponse<T>;
}

// Provider with cursor-based pagination
@riverpod
class ItemsList extends _$ItemsList {
  String? _cursor;
  bool _hasMore = true;
  bool _isLoadingMore = false;

  @override
  Future<List<Item>> build() async {
    _cursor = null;
    _hasMore = true;
    _isLoadingMore = false;
    return _fetchPage(null);
  }

  bool get hasMore => _hasMore;
  bool get isLoadingMore => _isLoadingMore;

  Future<List<Item>> _fetchPage(String? cursor) async {
    final api = ref.read(itemsApiProvider);
    final response = await api.getItems(cursor: cursor, limit: 20);
    _cursor = response.nextCursor;
    _hasMore = response.nextCursor != null;
    return response.items;
  }

  Future<void> loadMore() async {
    if (!_hasMore || _isLoadingMore) return;
    _isLoadingMore = true;

    final current = state.valueOrNull ?? [];
    try {
      final next = await _fetchPage(_cursor);
      state = AsyncValue.data([...current, ...next]);
    } on DioException catch (e) {
      // Don't lose existing data on loadMore failure
      throw mapDioException(e);
    } finally {
      _isLoadingMore = false;
    }
  }
}

// Screen with scroll listener + loading indicator
class ItemsScreen extends HookConsumerWidget {
  const ItemsScreen({super.key});

  @override
  Widget build(BuildContext context, WidgetRef ref) {
    final scrollController = useScrollController();
    final items = ref.watch(itemsListProvider);
    final notifier = ref.watch(itemsListProvider.notifier);

    useEffect(() {
      void listener() {
        if (scrollController.position.pixels >=
            scrollController.position.maxScrollExtent * 0.8) {
          notifier.loadMore();
        }
      }
      scrollController.addListener(listener);
      return () => scrollController.removeListener(listener);
    }, [scrollController]);

    return RefreshIndicator(
      onRefresh: () => ref.refresh(itemsListProvider.future),
      child: items.when(
        loading: () => const Center(child: CircularProgressIndicator()),
        error: (e, st) => AppErrorWidget(
          error: e,
          onRetry: () => ref.invalidate(itemsListProvider),
        ),
        data: (data) => ListView.builder(
          controller: scrollController,
          itemCount: data.length + (notifier.hasMore ? 1 : 0),
          itemBuilder: (context, index) {
            if (index == data.length) {
              return Center(
                child: Padding(
                  padding: EdgeInsets.all(16.w),
                  child: const CircularProgressIndicator(),
                ),
              );
            }
            return ItemTile(item: data[index]);
          },
        ),
      ),
    );
  }
}
```

### 4. Form with Validation

```dart
class CreateItemScreen extends HookConsumerWidget {
  const CreateItemScreen({super.key});

  @override
  Widget build(BuildContext context, WidgetRef ref) {
    final formKey = useMemoized(() => GlobalKey<FormState>());
    final nameController = useTextEditingController();
    final descController = useTextEditingController();
    final isLoading = useState(false);

    Future<void> submit() async {
      if (!formKey.currentState!.validate()) return;
      isLoading.value = true;
      try {
        await ref.read(itemsApiProvider).createItem(
          CreateItemRequest(
            name: nameController.text.trim(),
            description: descController.text.trim(),
          ),
        );
        if (context.mounted) context.pop();
      } on DioException catch (e) {
        final mapped = mapDioException(e);
        if (context.mounted) {
          ScaffoldMessenger.of(context).showSnackBar(
            SnackBar(content: Text(_errorMessage(context, mapped))),
          );
        }
      } finally {
        isLoading.value = false;
      }
    }

    return Scaffold(
      appBar: AppBar(title: Text(context.l10n.createItem)),
      body: Form(
        key: formKey,
        child: Padding(
          padding: EdgeInsets.all(16.w),
          child: Column(
            children: [
              TextFormField(
                controller: nameController,
                decoration: InputDecoration(labelText: context.l10n.name),
                validator: (v) => (v == null || v.trim().isEmpty)
                    ? context.l10n.fieldRequired
                    : null,
              ),
              16.verticalSpace,
              TextFormField(
                controller: descController,
                decoration: InputDecoration(labelText: context.l10n.description),
                maxLines: 3,
              ),
              24.verticalSpace,
              SizedBox(
                width: double.infinity,
                child: ElevatedButton(
                  onPressed: isLoading.value ? null : submit,
                  child: isLoading.value
                      ? const CircularProgressIndicator()
                      : Text(context.l10n.save),
                ),
              ),
            ],
          ),
        ),
      ),
    );
  }
}
```

### 5. Bottom Sheet

```dart
void showCustomBottomSheet(BuildContext context) {
  showModalBottomSheet(
    context: context,
    isScrollControlled: true,
    shape: RoundedRectangleBorder(
      borderRadius: BorderRadius.vertical(top: Radius.circular(20.r)),
    ),
    builder: (context) => SafeArea(
      child: Padding(
        padding: EdgeInsets.fromLTRB(20.w, 16.h, 20.w, 20.h),
        child: Column(
          mainAxisSize: MainAxisSize.min,
          children: [
            Container(
              width: 40.w,
              height: 4.h,
              decoration: BoxDecoration(
                color: AppColors.textSecondary.withValues(alpha: 0.3),
                borderRadius: BorderRadius.circular(2.r),
              ),
            ),
            16.verticalSpace,
            // Content
          ],
        ),
      ),
    ),
  );
}
```

### 6. Search with Debounce

```dart
class SearchScreen extends HookConsumerWidget {
  const SearchScreen({super.key});

  @override
  Widget build(BuildContext context, WidgetRef ref) {
    final controller = useTextEditingController();
    final query = useState('');

    useEffect(() {
      Timer? timer;
      void listener() {
        timer?.cancel();
        timer = Timer(const Duration(milliseconds: 400), () {
          query.value = controller.text.trim();
        });
      }
      controller.addListener(listener);
      return () {
        timer?.cancel();
        controller.removeListener(listener);
      };
    }, [controller]);

    final results = ref.watch(searchProvider(query.value));

    return Scaffold(
      appBar: AppBar(
        title: TextField(
          controller: controller,
          decoration: InputDecoration(
            hintText: context.l10n.search,
            border: InputBorder.none,
          ),
        ),
      ),
      body: results.when(
        loading: () => const Center(child: CircularProgressIndicator()),
        error: (e, st) => AppErrorWidget(error: e),
        data: (data) => ListView.builder(
          itemCount: data.length,
          itemBuilder: (context, index) => ListTile(title: Text(data[index].name)),
        ),
      ),
    );
  }
}
```

### 7. Auth Guard / Protected Routes

```dart
@riverpod
class Router extends _$Router {
  @override
  GoRouter build() {
    final isLoggedIn = ref.watch(authProvider);

    return GoRouter(
      initialLocation: '/home',
      routes: $appRoutes,
      redirect: (context, state) {
        final loggingIn = state.matchedLocation == '/login';
        if (!isLoggedIn && !loggingIn) return '/login';
        if (isLoggedIn && loggingIn) return '/home';
        return null;
      },
    );
  }
}

@Riverpod(keepAlive: true)
class Auth extends _$Auth {
  @override
  bool build() => false;

  void login() => state = true;
  void logout() => state = false;
}
```

### 8. Local Storage Toggle (Theme / Locale)

```dart
@Riverpod(keepAlive: true)
class SelectedLocale extends _$SelectedLocale {
  @override
  FutureOr<Locale> build() async {
    final prefs = await ref.read(sharedPreferencesProvider.future);
    final code = prefs.getString('locale') ?? 'en';
    return Locale(code);
  }

  Future<void> setLocale(Locale locale) async {
    final prefs = await ref.read(sharedPreferencesProvider.future);
    await prefs.setString('locale', locale.languageCode);
    state = AsyncValue.data(locale);
  }
}
```

### 9. Dio Error Interceptor (Global)

```dart
// See Error Handling section for full AppErrorInterceptor implementation.
// Minimal version for reference:
InterceptorsWrapper(
  onError: (error, handler) {
    final mapped = mapDioException(error);
    if (mapped is UnauthorizedException) {
      ref.read(authProvider.notifier).logout();
    }
    handler.reject(DioException(
      requestOptions: error.requestOptions,
      error: mapped,
      type: error.type,
      response: error.response,
    ));
  },
)
```

### 10. Image Upload with Progress

```dart
@riverpod
class ImageUploader extends _$ImageUploader {
  @override
  double build() => 0.0;

  Future<String> upload(File file) async {
    final dio = ref.read(dioClientProvider);
    final formData = FormData.fromMap({
      'file': await MultipartFile.fromFile(file.path),
    });
    final response = await dio.post(
      '/upload',
      data: formData,
      onSendProgress: (sent, total) {
        state = sent / total;
      },
    );
    state = 1.0;
    return response.data['url'];
  }
}
```

### 11. Service Singleton (Analytics, Payments, etc.)

```dart
// Abstract interface
abstract class AnalyticService {
  Future<void> initialize();
  Future<void> logEvent(String name, [Map<String, Object>? properties]);
  Future<String?> getUserId();
}

// Singleton implementation
class FirebaseAnalyticsService implements AnalyticService {
  FirebaseAnalyticsService._();
  static final FirebaseAnalyticsService instance = FirebaseAnalyticsService._();

  FirebaseAnalytics? _analytics;

  @override
  Future<void> initialize() async {
    if (_analytics != null) return;
    _analytics = FirebaseAnalytics.instance;
  }

  @override
  Future<void> logEvent(String name, [Map<String, Object>? properties]) async {
    await _analytics?.logEvent(name: name, parameters: properties);
  }

  @override
  Future<String?> getUserId() async {
    return _analytics?.appInstanceId;
  }
}

// Initialize in main()
await FirebaseAnalyticsService.instance.initialize();
```

### 12. Heavy Computation in Isolate (`compute()`)

```dart
import 'package:flutter/foundation.dart';

// Provider that offloads work to isolate
@riverpod
class ImageProcessor extends _$ImageProcessor {
  @override
  Uint8List? build() => null;

  Future<Uint8List> processImage(Uint8List bytes, Rect cropRect) async {
    final result = await compute(_processInIsolate, {
      'bytes': bytes,
      'left': cropRect.left,
      'top': cropRect.top,
      'width': cropRect.width,
      'height': cropRect.height,
    });
    state = result;
    return result;
  }
}

// Top-level function (required for compute())
Uint8List _processInIsolate(Map<String, dynamic> data) {
  final bytes = data['bytes'] as Uint8List;
  final decoded = img.decodeImage(bytes)!;
  final baked = img.bakeOrientation(decoded);
  final cropped = img.copyCrop(
    baked,
    x: data['left'].toInt(),
    y: data['top'].toInt(),
    width: data['width'].toInt(),
    height: data['height'].toInt(),
  );
  return Uint8List.fromList(img.encodeJpg(cropped, quality: 80));
}
```

**Rule**: Any operation >16ms (image processing, JSON parsing large payloads, crypto) MUST run in `compute()` to avoid UI jank. The function passed to `compute()` must be top-level or static.

### 13. Skeleton Loading

```dart
// Inside .when() loading branch
Container(
  width: double.infinity,
  height: 80.h,
  margin: EdgeInsets.symmetric(horizontal: 16.w, vertical: 8.h),
  decoration: BoxDecoration(
    color: AppColors.surface,
    borderRadius: BorderRadius.circular(12.r),
  ),
).animate(onPlay: (c) => c.repeat())
  .shimmer(duration: 1200.ms, color: AppColors.textSecondary.withValues(alpha: 0.1)),
```

### 14. Empty State

```dart
// Inside .when() data branch, when data.isEmpty
Center(
  child: Column(
    mainAxisSize: MainAxisSize.min,
    children: [
      Assets.icons.empty.svg(width: 120.w, height: 120.h),
      16.verticalSpace,
      Text(
        context.l10n.noItems,
        style: GoogleFonts.inter(fontSize: 16.sp, color: AppColors.textSecondary),
      ),
    ],
  ),
)
```

### 15. Cache-First with TTL

```dart
// Provider that serves stale data instantly, then refreshes in background
@riverpod
class CachedItems extends _$CachedItems {
  DateTime? _lastFetch;
  static const _ttl = Duration(minutes: 5);

  @override
  Future<List<Item>> build() async {
    // If cache is fresh, ref.keepAlive prevents auto-dispose
    final link = ref.keepAlive();

    // Auto-dispose after TTL
    final timer = Timer(_ttl, link.close);
    ref.onDispose(timer.cancel);

    return _fetch();
  }

  Future<List<Item>> _fetch() async {
    final api = ref.read(itemsApiProvider);
    final items = await api.getItems();
    _lastFetch = DateTime.now();
    return items;
  }

  /// Force refresh even if cache is fresh
  Future<void> refresh() async {
    state = const AsyncValue.loading();
    state = await AsyncValue.guard(() => _fetch());
  }

  bool get isStale =>
      _lastFetch == null || DateTime.now().difference(_lastFetch!) > _ttl;
}
```

### 16. Optimistic Update

```dart
@riverpod
class TodoList extends _$TodoList {
  @override
  Future<List<Todo>> build() async {
    final api = ref.read(todosApiProvider);
    return api.getTodos();
  }

  Future<void> toggle(Todo todo) async {
    final previous = state.valueOrNull ?? [];
    final toggled = todo.copyWith(isDone: !todo.isDone);

    // 1. Optimistic: update UI immediately
    state = AsyncValue.data(
      previous.map((t) => t.id == todo.id ? toggled : t).toList(),
    );

    try {
      // 2. Send to server
      await ref.read(todosApiProvider).updateTodo(toggled);
    } on DioException catch (e) {
      // 3. Rollback on failure
      state = AsyncValue.data(previous);
      throw mapDioException(e);
    }
  }

  Future<void> delete(String id) async {
    final previous = state.valueOrNull ?? [];

    // Optimistic remove
    state = AsyncValue.data(previous.where((t) => t.id != id).toList());

    try {
      await ref.read(todosApiProvider).deleteTodo(id);
    } on DioException catch (e) {
      state = AsyncValue.data(previous);
      throw mapDioException(e);
    }
  }
}
```

### 17. WebSocket with Reconnect (raw WebSocket)

```dart
@Riverpod(keepAlive: true)
class ChatConnection extends _$ChatConnection {
  WebSocketChannel? _channel;
  Timer? _reconnectTimer;
  int _retryCount = 0;
  static const _maxRetries = 5;

  @override
  Stream<ChatMessage> build() async* {
    _retryCount = 0;
    yield* _connect();
  }

  Stream<ChatMessage> _connect() async* {
    try {
      final token = await ref.read(secureStorageProvider.notifier).getToken();
      _channel = WebSocketChannel.connect(
        Uri.parse('wss://api.example.com/ws?token=$token'),
      );

      _retryCount = 0;

      yield* _channel!.stream
          .map((data) => ChatMessage.fromJson(jsonDecode(data as String)))
          .handleError((e) {
        _scheduleReconnect();
      });
    } catch (e) {
      _scheduleReconnect();
    }
  }

  void _scheduleReconnect() {
    if (_retryCount >= _maxRetries) return;
    _retryCount++;
    final delay = Duration(seconds: math.pow(2, _retryCount).toInt());
    _reconnectTimer?.cancel();
    _reconnectTimer = Timer(delay, () => ref.invalidateSelf());
  }

  void send(String message) {
    _channel?.sink.add(jsonEncode({'text': message}));
  }

  @override
  void dispose() {
    _reconnectTimer?.cancel();
    _channel?.sink.close();
    super.dispose();
  }
}

// Usage in screen
class ChatScreen extends HookConsumerWidget {
  const ChatScreen({super.key});

  @override
  Widget build(BuildContext context, WidgetRef ref) {
    final messages = useState(<ChatMessage>[]);
    final controller = useTextEditingController();

    ref.listen(chatConnectionProvider, (_, next) {
      next.whenData((msg) {
        messages.value = [...messages.value, msg];
      });
    });

    return Scaffold(
      body: Column(
        children: [
          Expanded(
            child: ListView.builder(
              reverse: true,
              itemCount: messages.value.length,
              itemBuilder: (context, index) {
                final msg = messages.value[messages.value.length - 1 - index];
                return ChatBubble(message: msg);
              },
            ),
          ),
          SafeArea(
            child: Row(
              children: [
                Expanded(
                  child: TextField(
                    controller: controller,
                    decoration: InputDecoration(hintText: context.l10n.typeMessage),
                  ),
                ),
                IconButton(
                  icon: const Icon(Icons.send),
                  onPressed: () {
                    final text = controller.text.trim();
                    if (text.isEmpty) return;
                    ref.read(chatConnectionProvider.notifier).send(text);
                    controller.clear();
                  },
                ),
              ],
            ),
          ),
        ],
      ),
    );
  }
}
```

### 18. Deep Linking with Query Parameters

```dart
// Route that accepts query parameters for deep links
@TypedGoRoute<ProductRoute>(path: '/product/:id')
class ProductRoute extends GoRouteData with $ProductRoute {
  const ProductRoute({required this.id, this.referral});
  final String id;
  final String? referral; // ?referral=abc123

  @override
  Widget build(BuildContext context, GoRouterState state) =>
      ProductScreen(id: id, referral: referral);
}

// Handling incoming deep links (Universal Links / App Links)
// In app_router.dart — GoRouter handles it automatically when paths match.
// Just register routes and GoRouter parses the URI.

@riverpod
class Router extends _$Router {
  @override
  GoRouter build() {
    final isLoggedIn = ref.watch(authProvider);

    return GoRouter(
      initialLocation: '/home',
      routes: $appRoutes,
      redirect: (context, state) {
        // Deep link lands here — check auth before allowing access
        final isPublic = ['/login', '/register', '/reset-password']
            .contains(state.matchedLocation);
        if (!isLoggedIn && !isPublic) return '/login';
        if (isLoggedIn && state.matchedLocation == '/login') return '/home';
        return null;
      },
      // Handle links that don't match any route
      errorBuilder: (context, state) => const NotFoundScreen(),
    );
  }
}
```

**iOS setup** (Runner/Runner.entitlements):
```xml
<key>com.apple.developer.associated-domains</key>
<array>
  <string>applinks:example.com</string>
</array>
```

**Android setup** (android/app/src/main/AndroidManifest.xml):
```xml
<intent-filter android:autoVerify="true">
  <action android:name="android.intent.action.VIEW" />
  <category android:name="android.intent.category.DEFAULT" />
  <category android:name="android.intent.category.BROWSABLE" />
  <data android:scheme="https" android:host="example.com" />
</intent-filter>
```

### 19. Multi-Step Form (Stepper)

```dart
@riverpod
class SignupForm extends _$SignupForm {
  @override
  SignupFormState build() => const SignupFormState();

  void setEmail(String email) =>
      state = state.copyWith(email: email);

  void setPassword(String password) =>
      state = state.copyWith(password: password);

  void setProfile({String? name, String? bio}) =>
      state = state.copyWith(name: name ?? state.name, bio: bio ?? state.bio);

  Future<void> submit() async {
    state = state.copyWith(isSubmitting: true);
    try {
      await ref.read(authApiProvider).signup(
        email: state.email,
        password: state.password,
        name: state.name,
      );
      state = state.copyWith(isSubmitting: false, isComplete: true);
    } on DioException catch (e) {
      state = state.copyWith(isSubmitting: false, error: mapDioException(e));
    }
  }
}

@freezed
abstract class SignupFormState with _$SignupFormState {
  const factory SignupFormState({
    @Default('') String email,
    @Default('') String password,
    @Default('') String name,
    @Default('') String bio,
    @Default(false) bool isSubmitting,
    @Default(false) bool isComplete,
    AppException? error,
  }) = _SignupFormState;
}

// Screen
class SignupScreen extends HookConsumerWidget {
  const SignupScreen({super.key});

  @override
  Widget build(BuildContext context, WidgetRef ref) {
    final step = useState(0);
    final form = ref.watch(signupFormProvider);
    final notifier = ref.watch(signupFormProvider.notifier);

    final steps = [
      _EmailStep(form: form, notifier: notifier),
      _PasswordStep(form: form, notifier: notifier),
      _ProfileStep(form: form, notifier: notifier),
    ];

    return Scaffold(
      appBar: AppBar(title: Text(context.l10n.signup)),
      body: Padding(
        padding: EdgeInsets.all(16.w),
        child: Column(
          children: [
            // Progress indicator
            LinearProgressIndicator(value: (step.value + 1) / steps.length),
            24.verticalSpace,
            Expanded(child: steps[step.value]),
            Row(
              children: [
                if (step.value > 0)
                  TextButton(
                    onPressed: () => step.value--,
                    child: Text(context.l10n.back),
                  ),
                const Spacer(),
                ElevatedButton(
                  onPressed: form.isSubmitting
                      ? null
                      : () {
                          if (step.value < steps.length - 1) {
                            step.value++;
                          } else {
                            notifier.submit();
                          }
                        },
                  child: Text(step.value < steps.length - 1
                      ? context.l10n.next
                      : context.l10n.submit),
                ),
              ],
            ),
          ],
        ),
      ),
    );
  }
}
```

### 20. Debounced Server Validation

```dart
// Email uniqueness check with debounce
@riverpod
class EmailValidator extends _$EmailValidator {
  Timer? _timer;

  @override
  AsyncValue<bool?> build() => const AsyncValue.data(null);

  void check(String email) {
    _timer?.cancel();
    if (email.isEmpty || !email.contains('@')) {
      state = const AsyncValue.data(null);
      return;
    }

    state = const AsyncValue.loading();
    _timer = Timer(const Duration(milliseconds: 600), () async {
      state = await AsyncValue.guard(() async {
        final api = ref.read(authApiProvider);
        return api.checkEmailAvailable(email);
      });
    });
  }
}

// Usage in TextFormField
TextFormField(
  controller: emailController,
  decoration: InputDecoration(
    labelText: context.l10n.email,
    suffixIcon: emailAvailability.when(
      data: (available) => available == null
          ? null
          : available
              ? const Icon(Icons.check_circle, color: Colors.green)
              : const Icon(Icons.cancel, color: Colors.red),
      loading: () => SizedBox(
        width: 20.w,
        height: 20.h,
        child: const CircularProgressIndicator(strokeWidth: 2),
      ),
      error: (_, __) => const Icon(Icons.error, color: Colors.red),
    ),
  ),
  onChanged: (v) => ref.read(emailValidatorProvider.notifier).check(v),
)
```

### 21. Connectivity Awareness

```dart
@Riverpod(keepAlive: true)
class ConnectivityStatus extends _$ConnectivityStatus {
  @override
  Stream<bool> build() {
    return Connectivity()
        .onConnectivityChanged
        .map((results) => results.any((r) => r != ConnectivityResult.none));
  }
}

// Show offline banner at top of app
class AppShell extends ConsumerWidget {
  const AppShell({super.key, required this.child});
  final Widget child;

  @override
  Widget build(BuildContext context, WidgetRef ref) {
    final isOnline = ref.watch(connectivityStatusProvider).valueOrNull ?? true;

    return Column(
      children: [
        if (!isOnline)
          MaterialBanner(
            content: Text(context.l10n.offlineMessage),
            backgroundColor: Colors.orange,
            actions: [Container()],
          ),
        Expanded(child: child),
      ],
    );
  }
}
```

### 22. Retry with Exponential Backoff

```dart
Future<T> retryWithBackoff<T>(
  Future<T> Function() action, {
  int maxRetries = 3,
  Duration initialDelay = const Duration(seconds: 1),
}) async {
  for (var attempt = 0; attempt <= maxRetries; attempt++) {
    try {
      return await action();
    } catch (e) {
      if (attempt == maxRetries) rethrow;
      final delay = initialDelay * math.pow(2, attempt).toInt();
      await Future.delayed(delay);
    }
  }
  throw StateError('Unreachable');
}

// Usage in a provider
@riverpod
class ReliableData extends _$ReliableData {
  @override
  Future<Data> build() async {
    return retryWithBackoff(
      () => ref.read(someApiProvider).getData(),
      maxRetries: 3,
    );
  }
}
```

### 23. Parameterized Family Provider for List Items

```dart
// Fetch detail by ID — auto-dispose when screen is popped
@riverpod
class ItemDetail extends _$ItemDetail {
  @override
  Future<Item> build(String id) async {
    final api = ref.read(itemsApiProvider);
    return api.getItem(id);
  }

  Future<void> refresh() async {
    state = const AsyncValue.loading();
    state = await AsyncValue.guard(
      () => ref.read(itemsApiProvider).getItem(id),
    );
  }
}

// Usage — each screen instance gets its own cached provider
class DetailScreen extends ConsumerWidget {
  const DetailScreen({super.key, required this.id});
  final String id;

  @override
  Widget build(BuildContext context, WidgetRef ref) {
    final item = ref.watch(itemDetailProvider(id));

    return Scaffold(
      appBar: AppBar(title: Text(context.l10n.detail)),
      body: item.when(
        loading: () => const Center(child: CircularProgressIndicator()),
        error: (e, st) => AppErrorWidget(
          error: e,
          onRetry: () => ref.read(itemDetailProvider(id).notifier).refresh(),
        ),
        data: (data) => ItemDetailContent(item: data),
      ),
    );
  }
}
```

### 24. Platform-Adaptive Dialog

```dart
Future<bool?> showAdaptiveConfirm(
  BuildContext context, {
  required String title,
  required String message,
}) {
  if (Platform.isIOS) {
    return showCupertinoDialog<bool>(
      context: context,
      builder: (context) => CupertinoAlertDialog(
        title: Text(title),
        content: Text(message),
        actions: [
          CupertinoDialogAction(
            isDestructiveAction: true,
            onPressed: () => Navigator.pop(context, false),
            child: Text(context.l10n.cancel),
          ),
          CupertinoDialogAction(
            onPressed: () => Navigator.pop(context, true),
            child: Text(context.l10n.confirm),
          ),
        ],
      ),
    );
  }

  return showDialog<bool>(
    context: context,
    builder: (context) => AlertDialog(
      title: Text(title),
      content: Text(message),
      actions: [
        TextButton(
          onPressed: () => Navigator.pop(context, false),
          child: Text(context.l10n.cancel),
        ),
        TextButton(
          onPressed: () => Navigator.pop(context, true),
          child: Text(context.l10n.confirm),
        ),
      ],
    ),
  );
}
```

### 25. Socket.IO Client with Reconnect

Use when the backend is a Socket.IO server (Node.js, Python-socketio, etc.). **Do not use `web_socket_channel` with Socket.IO** — it's a different protocol.

```dart
import 'package:socket_io_client/socket_io_client.dart' as io;

@Riverpod(keepAlive: true)
class SocketConnection extends _$SocketConnection {
  io.Socket? _socket;

  @override
  io.Socket build() {
    final token = ref.read(secureStorageProvider.notifier).getTokenSync();

    _socket = io.io(
      'https://api.example.com',
      io.OptionBuilder()
          .setTransports(['websocket'])
          .setAuth({'token': token})
          .enableAutoConnect()
          .enableReconnection()
          .setReconnectionDelay(1000)
          .setReconnectionDelayMax(5000)
          .setReconnectionAttempts(10)
          .build(),
    );

    ref.onDispose(() {
      _socket?.dispose();
    });

    return _socket!;
  }
}

// Event-specific provider — listens to a Socket.IO event as a stream
@riverpod
class ChatMessages extends _$ChatMessages {
  @override
  Stream<ChatMessage> build() {
    final socket = ref.watch(socketConnectionProvider);
    final controller = StreamController<ChatMessage>();

    socket.on('message', (data) {
      controller.add(ChatMessage.fromJson(data as Map<String, dynamic>));
    });

    socket.on('error', (data) {
      controller.addError(AppException.unknown(error: data));
    });

    ref.onDispose(() {
      socket.off('message');
      socket.off('error');
      controller.close();
    });

    return controller.stream;
  }
}

// Emitting events
void sendMessage(WidgetRef ref, String text) {
  ref.read(socketConnectionProvider).emit('message', {'text': text});
}

// Joining/leaving rooms
void joinRoom(WidgetRef ref, String roomId) {
  ref.read(socketConnectionProvider).emit('join', {'room': roomId});
}

// Usage in screen
class ChatScreen extends HookConsumerWidget {
  const ChatScreen({super.key});

  @override
  Widget build(BuildContext context, WidgetRef ref) {
    final messages = useState(<ChatMessage>[]);
    final controller = useTextEditingController();
    final socket = ref.watch(socketConnectionProvider);
    final isConnected = useState(socket.connected);

    useEffect(() {
      void onConnect(_) => isConnected.value = true;
      void onDisconnect(_) => isConnected.value = false;
      socket.on('connect', onConnect);
      socket.on('disconnect', onDisconnect);
      return () {
        socket.off('connect', onConnect);
        socket.off('disconnect', onDisconnect);
      };
    }, [socket]);

    ref.listen(chatMessagesProvider, (_, next) {
      next.whenData((msg) {
        messages.value = [...messages.value, msg];
      });
    });

    return Scaffold(
      appBar: AppBar(
        title: Text(context.l10n.chat),
        actions: [
          Icon(
            isConnected.value ? Icons.cloud_done : Icons.cloud_off,
            color: isConnected.value ? Colors.green : Colors.red,
          ),
          8.horizontalSpace,
        ],
      ),
      body: Column(
        children: [
          Expanded(
            child: ListView.builder(
              reverse: true,
              itemCount: messages.value.length,
              itemBuilder: (context, index) {
                final msg = messages.value[messages.value.length - 1 - index];
                return ChatBubble(message: msg);
              },
            ),
          ),
          SafeArea(
            child: Padding(
              padding: EdgeInsets.all(8.w),
              child: Row(
                children: [
                  Expanded(
                    child: TextField(
                      controller: controller,
                      decoration: InputDecoration(hintText: context.l10n.typeMessage),
                    ),
                  ),
                  IconButton(
                    icon: const Icon(Icons.send),
                    onPressed: () {
                      final text = controller.text.trim();
                      if (text.isEmpty) return;
                      sendMessage(ref, text);
                      controller.clear();
                    },
                  ),
                ],
              ),
            ),
          ),
        ],
      ),
    );
  }
}
```

**Выбор между #17 и #25:**
- Backend использует `ws://` / `wss://` напрямую → **#17 (web_socket_channel)**
- Backend использует Socket.IO (Node.js `socket.io`, Python `python-socketio`) → **#25 (socket_io_client)**
- Если не знаешь — спроси. Подключение `web_socket_channel` к Socket.IO серверу **не работает**.

---

## Pre-Delivery Checklist

### Build (CRITICAL)
- [ ] `dart run build_runner build --delete-conflicting-outputs` — runs clean
- [ ] `dart analyze` — no errors
- [ ] No unresolved `part` directives

### Testing (CRITICAL)
- [ ] Unit tests written for every new/modified Notifier
- [ ] Widget tests for new screens with provider overrides
- [ ] `flutter test` — all tests pass, no regressions
- [ ] Mock only boundaries (API, storage), not models or value objects

### Architecture
- [ ] New files in correct feature directory, not in `core/`
- [ ] No circular provider dependencies
- [ ] Providers use annotation syntax (`@riverpod` / `@Riverpod`)
- [ ] No business logic in widget `build()` methods

### Error Handling
- [ ] API errors mapped through `AppException` sealed class
- [ ] UI error states use `AppErrorWidget`, never raw exception text
- [ ] Auth tokens stored in `flutter_secure_storage`, not `SharedPreferences`

### UI
- [ ] All dimensions use ScreenUtil (`.w`, `.h`, `.sp`, `.r`)
- [ ] No hardcoded user-facing strings (all `context.l10n.*`)
- [ ] Assets via `Assets.*`, not string paths
- [ ] `SafeArea` wraps main content
- [ ] Deep widget trees extracted into separate files
- [ ] Interactive elements have `Semantics` label or minimum 48x48 tap target

### Performance
- [ ] No object allocations (`TextStyle()`, `EdgeInsets()`) inside `build()` — use `const` or static
- [ ] Long lists use `ListView.builder`, not `Column` with `.map()`
- [ ] Heavy computation (>16ms) offloaded to `compute()` isolate

### State
- [ ] `ref.watch()` in build, `ref.read()` in callbacks
- [ ] Async states handled with `.when()` / `.maybeWhen()`
- [ ] No `StatefulWidget` + `setState` for shared state

### Navigation
- [ ] Routes use typed GoRoute classes with `with $RouteName` mixin
- [ ] Navigation via `.push(context)` / `.pushReplacement(context)`, no `.go()` or `Navigator.*`
- [ ] New routes registered in router, build_runner re-run

---

## Package Versions

| Package | Version | Purpose |
|---------|---------|---------|
| hooks_riverpod | ^3.1.0 | State management |
| flutter_hooks | ^0.21.3+1 | Local widget state (hooks) |
| riverpod_annotation | ^4.0.0 | Provider annotations |
| riverpod_generator | ^4.0.0+1 | Provider code-gen (dev) |
| riverpod_lint | ^3.1.0 | Lint rules (dev) |
| custom_lint | ^0.8.1 | Custom lint support (dev) |
| go_router | ^17.1.0 | Routing |
| go_router_builder | ^4.2.0 | Typed route code-gen (dev) |
| dio | ^5.9.1 | HTTP client |
| retrofit | ^4.9.2 | API definitions |
| retrofit_generator | ^10.2.1 | API code-gen (dev) |
| pretty_dio_logger | ^1.4.0 | Network debugging |
| freezed_annotation | ^3.1.0 | Model annotations |
| freezed | ^3.2.3 | Model code-gen (dev) |
| json_annotation | ^4.9.0 | JSON annotations |
| json_serializable | ^6.11.2 | JSON code-gen (dev) |
| flutter_screenutil | ^5.9.3 | Responsive UI |
| flutter_gen_runner | ^5.12.0 | Asset code-gen (dev) |
| google_fonts | ^8.0.2 | Typography |
| shared_preferences | ^2.5.4 | Local storage (non-sensitive) |
| flutter_secure_storage | ^9.2.4 | Secure storage (tokens, secrets) |
| flutter_dotenv | ^6.0.0 | Environment config |
| build_runner | ^2.11.1 | Code-gen orchestrator (dev) |
| flutter_svg | ^2.2.3 | SVG rendering |
| auto_size_text | ^3.0.0 | Auto-sizing text |
| flutter_animate | ^4.5.2 | Animations |
| animate_do | ^4.2.0 | Pre-built animations |
| url_launcher | ^6.3.2 | Open URLs |
| share_plus | ^12.0.1 | Share content |
| path_provider | ^2.1.5 | File system paths |
| permission_handler | ^12.0.1 | Runtime permissions |
| device_info_plus | ^12.3.0 | Device info |
| fl_chart | ^1.1.1 | Charts |
| connectivity_plus | ^6.1.4 | Network connectivity status |
| web_socket_channel | ^3.0.2 | Raw WebSocket client |
| socket_io_client | ^3.0.2 | Socket.IO client (use when backend is Socket.IO) |
| mocktail | ^1.0.4 | Mocking for tests (dev) |
