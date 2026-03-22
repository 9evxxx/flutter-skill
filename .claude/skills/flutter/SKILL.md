---
name: flutter
description: "Flutter mobile app development for iOS and Android. Riverpod, Hooks, GoRouter, Retrofit, Dio, Freezed, ScreenUtil, flutter_gen, ARB localization, SharedPreferences, build_runner code generation. Actions: create, build, add, implement, fix, refactor, review, optimize, test, debug, deploy Flutter code. Projects: mobile app, iOS app, Android app, cross-platform app, Flutter project, pubspec.yaml, .dart files. Elements: screen, widget, provider, notifier, model, API client, route, theme, localization, form, list, bottom sheet, dialog. Patterns: feature-first architecture, code generation, state management, dependency injection, typed routing, responsive UI, pull-to-refresh, pagination, infinite scroll, search, auth guard, error handling. TRIGGER when the current project is a Flutter project (has pubspec.yaml with flutter dependency) or the user asks to create/work with a Flutter app."
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
| 3 | Project Structure | HIGH | `PS-1`..`PS-5` | feature-first, correct file placement |
| 4 | Networking & Models | HIGH | `NM-1`..`NM-5` | Retrofit + Freezed, no raw dio/json |
| 5 | Routing | HIGH | `RT-1`..`RT-4` | typed GoRouter, code-generated |
| 6 | Responsive UI | HIGH | `RU-1`..`RU-3` | every dimension uses ScreenUtil |
| 7 | Localization & Assets | MEDIUM | `LA-1`..`LA-4` | no hardcoded strings or asset paths |
| 8 | Theming & Styling | MEDIUM | `TS-1`..`TS-4` | AppColors/AppTheme, GoogleFonts |
| 9 | Coding Style | LOW | `CS-1`..`CS-8` | const, trailing commas, SafeArea |

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
3. **Create API layer** — Retrofit abstract class + Riverpod provider to expose the API client.
4. **Create providers** — Annotation-based providers in `features/[name]/presentation/controllers/`. Wire up API calls and state.
5. **Create screen** — `HookConsumerWidget` in `features/[name]/presentation/screens/`. Hooks for local state, `ref.watch()` for providers.
6. **Extract widgets** — Break large trees into `features/[name]/presentation/widgets/`. Prefix with feature name.
7. **Add route** — Typed GoRoute class, register in router.
8. **Add localization** — New keys in ARB files for user-facing strings.
9. **Run build_runner** — `dart run build_runner build --delete-conflicting-outputs`. ALWAYS.
10. **Verify** — `dart analyze` for errors.

### Modifying Existing Code

1. **Read first** — Understand the existing feature structure before changing anything.
2. **Follow existing patterns** — Do not introduce new architectural patterns (e.g., BLoC into a Riverpod project).
3. **Run build_runner** — If any generated files are affected.
4. **Verify** — `dart analyze`.

---

## Project Structure

```
lib/
├── core/                          # Shared/global layer
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
```

### Naming Conventions

| Type | File | Class |
|------|------|-------|
| Screen | `[name]_screen.dart` | `[Name]Screen` |
| Widget | `[feature]_[name]_widget.dart` | `[Feature][Name]Widget` |
| Provider | `[name]_provider.dart` | `[Name]Provider` / `[Name]Notifier` |
| Model | `[name].dart` | `[Name]` (Freezed) |
| API | `[service]_api.dart` | `[Service]Api` (Retrofit) |

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
      InterceptorsWrapper(
        onRequest: (options, handler) {
          // Add auth headers
          handler.next(options);
        },
        onError: (error, handler) {
          // Handle 401, etc.
          handler.next(error);
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
        error: (e, st) => Center(child: Text(context.l10n.genericError)),
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

### 3. Infinite Scroll / Pagination

```dart
// Provider
@riverpod
class ItemsList extends _$ItemsList {
  int _page = 0;

  @override
  Future<List<Item>> build() async {
    _page = 0;
    return _fetch(0);
  }

  Future<List<Item>> _fetch(int page) async {
    final api = ref.read(itemsApiProvider);
    return api.getItems(page: page, limit: 20);
  }

  Future<void> loadMore() async {
    final current = state.valueOrNull ?? [];
    _page++;
    final next = await _fetch(_page);
    state = AsyncValue.data([...current, ...next]);
  }
}

// Screen
class ItemsScreen extends HookConsumerWidget {
  const ItemsScreen({super.key});

  @override
  Widget build(BuildContext context, WidgetRef ref) {
    final scrollController = useScrollController();

    useEffect(() {
      void listener() {
        if (scrollController.position.pixels >=
            scrollController.position.maxScrollExtent * 0.8) {
          ref.read(itemsListProvider.notifier).loadMore();
        }
      }
      scrollController.addListener(listener);
      return () => scrollController.removeListener(listener);
    }, [scrollController]);

    final items = ref.watch(itemsListProvider);

    return items.when(
      loading: () => const Center(child: CircularProgressIndicator()),
      error: (e, st) => Center(child: Text(context.l10n.genericError)),
      data: (data) => ListView.builder(
        controller: scrollController,
        itemCount: data.length,
        itemBuilder: (context, index) => ItemTile(item: data[index]),
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
      } catch (e) {
        // Show error
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
        error: (e, st) => Center(child: Text(context.l10n.genericError)),
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
InterceptorsWrapper(
  onError: (error, handler) {
    switch (error.response?.statusCode) {
      case 401:
        // Token expired — redirect to login
        ref.read(authProvider.notifier).logout();
        break;
      case 500:
        // Server error — log
        break;
    }
    handler.next(error);
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

---

## Pre-Delivery Checklist

### Build (CRITICAL)
- [ ] `dart run build_runner build --delete-conflicting-outputs` — runs clean
- [ ] `dart analyze` — no errors
- [ ] No unresolved `part` directives

### Architecture
- [ ] New files in correct feature directory, not in `core/`
- [ ] No circular provider dependencies
- [ ] Providers use annotation syntax (`@riverpod` / `@Riverpod`)
- [ ] No business logic in widget `build()` methods

### UI
- [ ] All dimensions use ScreenUtil (`.w`, `.h`, `.sp`, `.r`)
- [ ] No hardcoded user-facing strings (all `context.l10n.*`)
- [ ] Assets via `Assets.*`, not string paths
- [ ] `SafeArea` wraps main content
- [ ] Deep widget trees extracted into separate files

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
| shared_preferences | ^2.5.4 | Local storage |
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
