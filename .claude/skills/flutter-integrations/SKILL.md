---
name: flutter-integrations
description: "Integration guides for Flutter projects using the flutter skill architecture. Sentry crash reporting, Firebase Push Notifications (FCM), Supabase backend and auth, Firebase Auth with Google/Apple Sign-In, Amplitude analytics, Firebase Analytics, Analytics Facade pattern. TRIGGER when the user asks to integrate, connect, add, or set up: Sentry, Firebase, Supabase, Amplitude, push notifications, crash reporting, analytics, authentication, Google Sign-In, Apple Sign-In, or any third-party service in a Flutter project."
user-invocable: true
---

# Flutter Integration Guides

Optional service integrations for Flutter projects using the **flutter skill** architecture. Each guide shows how to connect the service correctly within the existing architecture (Riverpod providers, AppException, GoRouter).

> **Prerequisite**: The project must follow the flutter skill architecture (Riverpod, GoRouter, Freezed, AppException). These guides build on top of it.

> **API keys**: Use `--dart-define` for compile-time constants, not `.env` files, for service keys that ship in the binary. Example: `flutter run --dart-define=SENTRY_DSN=https://...`

---

## Sentry — Crash Reporting

```dart
// main.dart — Sentry wraps the entire app
Future<void> main() async {
  WidgetsFlutterBinding.ensureInitialized();
  await Firebase.initializeApp(); // if using Firebase too

  await SentryFlutter.init(
    (options) {
      options.dsn = const String.fromEnvironment('SENTRY_DSN');
      options.tracesSampleRate = 0.2;
      options.environment =
          const String.fromEnvironment('ENV', defaultValue: 'dev');
    },
    appRunner: () => runApp(ProviderScope(child: const MyApp())),
  );
}

// Dio integration — add to DioClient provider
dio.interceptors.add(SentryDioClientInterceptor());

// Report AppException to Sentry (skip expected errors)
class AppErrorInterceptor extends Interceptor {
  // ... existing onError ...

  @override
  void onError(DioException err, ErrorInterceptorHandler handler) {
    final mapped = mapDioException(err);

    // Report unexpected errors to Sentry
    if (mapped is! UnauthorizedException && mapped is! ValidationException) {
      Sentry.captureException(mapped, stackTrace: err.stackTrace);
    }

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

// Route tracking — add observer to GoRouter
GoRouter(
  observers: [SentryNavigatorObserver()],
  routes: $appRoutes,
  // ...
)

// Catch Flutter framework errors
FlutterError.onError = (details) {
  Sentry.captureException(details.exception, stackTrace: details.stack);
};
```

Packages: `sentry_flutter: ^8.12.0`, `sentry_dio: ^8.12.0`

---

## Firebase Push Notifications (FCM)

```dart
// Background handler — MUST be top-level function
@pragma('vm:entry-point')
Future<void> _firebaseMessagingBackgroundHandler(RemoteMessage message) async {
  await Firebase.initializeApp();
  // Parse and store notification — no UI access here
}

// main.dart
Future<void> main() async {
  WidgetsFlutterBinding.ensureInitialized();
  await Firebase.initializeApp();
  FirebaseMessaging.onBackgroundMessage(_firebaseMessagingBackgroundHandler);
  runApp(ProviderScope(child: const MyApp()));
}

// Provider — manages permission, token, and message handling
@Riverpod(keepAlive: true)
class PushNotifications extends _$PushNotifications {
  @override
  Future<String?> build() async {
    final messaging = FirebaseMessaging.instance;

    // Request permission (iOS shows dialog, Android auto-grants)
    final settings = await messaging.requestPermission(
      alert: true,
      badge: true,
      sound: true,
    );
    if (settings.authorizationStatus != AuthorizationStatus.authorized) {
      return null;
    }

    // Foreground messages — show in-app banner
    FirebaseMessaging.onMessage.listen(_handleForeground);

    // Notification tap — app was in background
    FirebaseMessaging.onMessageOpenedApp.listen(_handleTap);

    // Notification tap — app was terminated
    final initial = await messaging.getInitialMessage();
    if (initial != null) _handleTap(initial);

    // Token management
    final token = await messaging.getToken();
    messaging.onTokenRefresh.listen((newToken) {
      ref.read(someApiProvider).updateFcmToken(newToken);
    });

    return token;
  }

  void _handleForeground(RemoteMessage message) {
    // Show local notification or snackbar
  }

  void _handleTap(RemoteMessage message) {
    // Navigate via GoRouter based on notification payload
    final route = message.data['route'];
    final id = message.data['id'];
    if (route != null) {
      // ref.read(routerProvider).push(route);
    }
  }
}
```

Packages: `firebase_core: ^3.12.0`, `firebase_messaging: ^15.2.0`

---

## Supabase — Backend

```dart
// main.dart
await Supabase.initialize(
  url: const String.fromEnvironment('SUPABASE_URL'),
  anonKey: const String.fromEnvironment('SUPABASE_ANON_KEY'),
);

// Provider for Supabase client
@Riverpod(keepAlive: true)
class SupabaseClientNotifier extends _$SupabaseClientNotifier {
  @override
  SupabaseClient build() => Supabase.instance.client;
}

// Repository — replaces Retrofit for Supabase projects
@riverpod
class ItemsRepository extends _$ItemsRepository {
  @override
  Future<List<Item>> build() async {
    final client = ref.watch(supabaseClientNotifierProvider);
    final response = await client
        .from('items')
        .select()
        .order('created_at', ascending: false);
    return response.map((json) => Item.fromJson(json)).toList();
  }

  Future<void> create(Item item) async {
    final client = ref.read(supabaseClientNotifierProvider);
    await client.from('items').insert(item.toJson());
    ref.invalidateSelf();
  }

  Future<void> delete(String id) async {
    final client = ref.read(supabaseClientNotifierProvider);
    await client.from('items').delete().eq('id', id);
    ref.invalidateSelf();
  }
}

// Supabase Realtime — subscribe to table changes
@riverpod
class RealtimeMessages extends _$RealtimeMessages {
  @override
  Stream<ChatMessage> build(String roomId) async* {
    final client = ref.watch(supabaseClientNotifierProvider);
    final controller = StreamController<ChatMessage>();

    final channel = client.channel('room:$roomId')
      ..onPostgresChanges(
        event: PostgresChangeEvent.insert,
        schema: 'public',
        table: 'messages',
        filter: PostgresChangeFilter(
          type: PostgresChangeFilterType.eq,
          column: 'room_id',
          value: roomId,
        ),
        callback: (payload) {
          controller.add(ChatMessage.fromJson(payload.newRecord));
        },
      )
      ..subscribe();

    ref.onDispose(() {
      channel.unsubscribe();
      controller.close();
    });

    yield* controller.stream;
  }
}
```

**Note**: With Supabase, you typically don't need Retrofit + Dio for database operations. Use Retrofit only for external third-party APIs. Supabase client handles auth headers and retries internally.

Package: `supabase_flutter: ^2.9.0`

---

## Firebase Auth

```dart
@Riverpod(keepAlive: true)
class AuthNotifier extends _$AuthNotifier {
  @override
  Stream<User?> build() {
    return FirebaseAuth.instance.authStateChanges();
  }

  User? get currentUser => FirebaseAuth.instance.currentUser;

  Future<void> signInWithGoogle() async {
    final googleUser = await GoogleSignIn().signIn();
    if (googleUser == null) return; // User cancelled

    final googleAuth = await googleUser.authentication;
    final credential = GoogleAuthProvider.credential(
      accessToken: googleAuth.accessToken,
      idToken: googleAuth.idToken,
    );
    await FirebaseAuth.instance.signInWithCredential(credential);
  }

  Future<void> signInWithApple() async {
    final appleProvider = AppleAuthProvider()
      ..addScope('email')
      ..addScope('fullName');
    await FirebaseAuth.instance.signInWithProvider(appleProvider);
  }

  Future<void> signInWithEmail(String email, String password) async {
    try {
      await FirebaseAuth.instance.signInWithEmailAndPassword(
        email: email,
        password: password,
      );
    } on FirebaseAuthException catch (e) {
      throw switch (e.code) {
        'user-not-found' || 'wrong-password' => const AppException.unauthorized(),
        'too-many-requests' => const AppException.server(code: 429, message: 'Too many attempts'),
        _ => AppException.unknown(error: e),
      };
    }
  }

  Future<void> signOut() async {
    await GoogleSignIn().signOut();
    await FirebaseAuth.instance.signOut();
  }
}

// GoRouter integration — watches auth stream
@riverpod
class Router extends _$Router {
  @override
  GoRouter build() {
    final authState = ref.watch(authNotifierProvider);
    final isLoggedIn = authState.valueOrNull != null;

    return GoRouter(
      initialLocation: '/home',
      routes: $appRoutes,
      redirect: (context, state) {
        final isAuthRoute = ['/login', '/register']
            .contains(state.matchedLocation);
        if (!isLoggedIn && !isAuthRoute) return '/login';
        if (isLoggedIn && isAuthRoute) return '/home';
        return null;
      },
    );
  }
}
```

Packages: `firebase_auth: ^5.5.0`, `google_sign_in: ^6.2.2`

---

## Supabase Auth

```dart
@Riverpod(keepAlive: true)
class AuthNotifier extends _$AuthNotifier {
  @override
  Stream<AuthState> build() {
    return Supabase.instance.client.auth.onAuthStateChange;
  }

  bool get isLoggedIn =>
      Supabase.instance.client.auth.currentSession != null;

  Future<void> signInWithEmail(String email, String password) async {
    try {
      await Supabase.instance.client.auth.signInWithPassword(
        email: email,
        password: password,
      );
    } on AuthException catch (e) {
      throw const AppException.unauthorized();
    }
  }

  Future<void> signInWithGoogle() async {
    await Supabase.instance.client.auth.signInWithOAuth(
      OAuthProvider.google,
      redirectTo: 'com.example.app://callback',
    );
  }

  Future<void> signInWithApple() async {
    await Supabase.instance.client.auth.signInWithOAuth(
      OAuthProvider.apple,
      redirectTo: 'com.example.app://callback',
    );
  }

  Future<void> signUp(String email, String password) async {
    try {
      await Supabase.instance.client.auth.signUp(
        email: email,
        password: password,
      );
    } on AuthException catch (e) {
      throw AppException.validation(fields: {'email': e.message});
    }
  }

  Future<void> signOut() async {
    await Supabase.instance.client.auth.signOut();
  }
}

// GoRouter integration — same pattern as Firebase Auth
@riverpod
class Router extends _$Router {
  @override
  GoRouter build() {
    final authState = ref.watch(authNotifierProvider);
    // Supabase emits AuthState with event + session
    final isLoggedIn = authState.valueOrNull?.session != null;

    return GoRouter(
      initialLocation: '/home',
      routes: $appRoutes,
      redirect: (context, state) {
        final isAuthRoute = ['/login', '/register']
            .contains(state.matchedLocation);
        if (!isLoggedIn && !isAuthRoute) return '/login';
        if (isLoggedIn && isAuthRoute) return '/home';
        return null;
      },
    );
  }
}
```

**OAuth redirect setup**: Add `com.example.app://callback` to Supabase dashboard → Authentication → URL Configuration → Redirect URLs. Configure deep link in iOS (Info.plist) and Android (AndroidManifest.xml) accordingly.

Package: `supabase_flutter: ^2.9.0` (same as backend)

---

## Amplitude — Analytics

```dart
@Riverpod(keepAlive: true)
class AmplitudeNotifier extends _$AmplitudeNotifier {
  late final Amplitude _amplitude;

  @override
  Amplitude build() {
    _amplitude = Amplitude(Configuration(
      apiKey: const String.fromEnvironment('AMPLITUDE_API_KEY'),
      autocapture: AutocaptureOptions(
        sessions: true,
        appLifecycles: true,
        screenViews: false, // Track manually via GoRouter observer
      ),
    ));
    return _amplitude;
  }

  void identify(String userId, {Map<String, dynamic>? properties}) {
    _amplitude
      ..setUserId(userId)
      ..identify(Identify()..setAll(properties ?? {}));
  }

  void track(String event, [Map<String, dynamic>? properties]) {
    _amplitude.track(BaseEvent(event, eventProperties: properties));
  }

  void reset() => _amplitude.reset(); // On logout
}
```

Package: `amplitude_flutter: ^4.6.0`

---

## Firebase Analytics

```dart
@Riverpod(keepAlive: true)
class FirebaseAnalyticsNotifier extends _$FirebaseAnalyticsNotifier {
  late final FirebaseAnalytics _analytics;

  @override
  FirebaseAnalytics build() {
    _analytics = FirebaseAnalytics.instance;
    return _analytics;
  }

  void setUser(String userId) => _analytics.setUserId(id: userId);

  void track(String event, [Map<String, Object>? properties]) {
    _analytics.logEvent(name: event, parameters: properties);
  }

  void reset() => _analytics.setUserId(id: null); // On logout
}

// Auto screen tracking via built-in observer
GoRouter(
  observers: [
    FirebaseAnalyticsObserver(analytics: FirebaseAnalytics.instance),
  ],
  // ...
)
```

Package: `firebase_analytics: ^11.5.0`

---

## Analytics Facade (when using multiple analytics providers)

When the project uses both Amplitude and Firebase Analytics (or any combination), create a single facade to avoid duplicate calls scattered across the codebase.

```dart
// Typed event names — prevents typos, enables autocomplete
abstract class AnalyticsEvents {
  static const itemViewed = 'item_viewed';
  static const itemCreated = 'item_created';
  static const searchPerformed = 'search_performed';
  static const purchaseStarted = 'purchase_started';
  static const purchaseCompleted = 'purchase_completed';
  static const authLogin = 'auth_login';
  static const authLogout = 'auth_logout';
}

// Facade — single entry point for all analytics
@Riverpod(keepAlive: true)
class Analytics extends _$Analytics {
  @override
  void build() {}

  void identify(String userId) {
    ref.read(amplitudeNotifierProvider.notifier).identify(userId);
    ref.read(firebaseAnalyticsNotifierProvider.notifier).setUser(userId);
  }

  void track(String event, [Map<String, dynamic>? properties]) {
    ref.read(amplitudeNotifierProvider.notifier).track(event, properties);
    ref.read(firebaseAnalyticsNotifierProvider.notifier)
        .track(event, properties?.cast<String, Object>());
  }

  void reset() {
    ref.read(amplitudeNotifierProvider.notifier).reset();
    ref.read(firebaseAnalyticsNotifierProvider.notifier).reset();
  }
}

// Usage — always go through facade
ref.read(analyticsProvider.notifier).track(
  AnalyticsEvents.itemViewed,
  {'item_id': item.id, 'category': item.category},
);
```

**Rule**: Never call Amplitude or Firebase Analytics directly from widgets or notifiers. Always go through the `Analytics` facade. This makes it trivial to add/remove/swap analytics providers.

---

## Package Versions (Integration-specific)

| Package | Version | Purpose |
|---------|---------|---------|
| sentry_flutter | ^8.12.0 | Crash reporting |
| sentry_dio | ^8.12.0 | Sentry Dio integration |
| firebase_core | ^3.12.0 | Firebase core |
| firebase_messaging | ^15.2.0 | Push notifications (FCM) |
| firebase_auth | ^5.5.0 | Firebase authentication |
| firebase_analytics | ^11.5.0 | Firebase analytics |
| google_sign_in | ^6.2.2 | Google Sign-In |
| supabase_flutter | ^2.9.0 | Supabase backend + auth |
| amplitude_flutter | ^4.6.0 | Amplitude analytics |
