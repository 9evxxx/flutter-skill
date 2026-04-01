# Flutter Skill for Claude Code

Stop prompting Claude how to write Flutter. Just install this skill and get production-ready code from the first message.

## The Problem

Without a skill, Claude Code generates Flutter apps with random architecture choices every time. StatefulWidget here, BLoC there, manual JSON parsing, hardcoded strings, no responsive sizing, forgotten `build_runner`. You end up spending more time correcting Claude than writing code yourself.

## The Solution

This skill locks Claude into a single, opinionated, production-grade Flutter architecture. Every response follows the same conventions. Every file lands in the right place. Every provider uses code-gen. Every dimension is responsive. No debates — just consistent, shippable code.

**What changes when the skill is active:**

- Claude follows a **strict workflow** — models first, then error types, then API layer, then providers, then UI, then routes, then tests, then build_runner. Every time.
- Claude checks **60 prioritized rules** before writing code — code generation and testing are CRITICAL, styling rules are LOW. It knows what matters most.
- Claude avoids **41 specific anti-patterns** — no `ref.read()` in `build()`, no `StatefulWidget` for shared state, no hardcoded dimensions, no manual JSON parsing, no untested notifiers, no swallowed errors.
- Claude has **25 ready-made patterns** for the things you actually build — pagination, forms, search with debounce, auth guards, image uploads, WebSocket, caching, optimistic updates, deep linking, multi-step forms, error handling.
- Claude runs a **pre-delivery checklist** of **29 quality gates** before handing code back — build_runner, tests, dart analyze, error handling, accessibility, performance, ScreenUtil on all dimensions, localization, file placement.

## Stack

| Layer | Technology |
|-------|-----------|
| State Management | Riverpod + Hooks (code-gen) |
| Routing | GoRouter (typed, code-gen) |
| HTTP Client | Dio |
| API Layer | Retrofit (code-gen) |
| Models | Freezed + JSON Serializable |
| Error Handling | Sealed AppException (Freezed) |
| Responsive UI | FlutterScreenUtil (390x844) |
| DI | Riverpod providers (no GetIt) |
| Localization | ARB / flutter_localizations |
| Assets | flutter_gen (code-gen) |
| Fonts | GoogleFonts |
| Local Storage | SharedPreferences + flutter_secure_storage |
| Testing | mocktail + ProviderScope overrides |
| Connectivity | connectivity_plus |
| Code Generation | build_runner |
| Crash Reporting | Sentry (optional) |
| Push Notifications | Firebase Messaging (optional) |
| Auth | Firebase Auth / Supabase Auth (optional) |
| Backend | Supabase (optional) |
| Analytics | Amplitude + Firebase Analytics (optional) |

## What's Inside

### Behavioral Controls
- **When to Apply** — Must Use / Recommended / Skip triggers so Claude activates only when relevant
- **Priority Table** — 13 rule categories ranked CRITICAL to LOW — Claude checks the important things first
- **Step-by-Step Workflow** — deterministic process for creating features and modifying code

### Reference
- **60 Quick Reference Rules** — compact rules with IDs (CG-*, SM-*, TT-*, PS-*, NM-*, EH-*, RT-*, RU-*, LA-*, TS-*, PF-*, AC-*, CS-*)
- **Project Structure** — feature-first architecture with complete directory tree and naming conventions
- **Full Tech Stack Reference** — code examples for every layer of the stack

### Guard Rails
- **41 Anti-Patterns** — architecture, state management, UI, networking, routing, code generation, error handling, testing, and performance mistakes to avoid
- **Pre-Delivery Checklist** — 29 quality gates across Build, Testing, Architecture, Error Handling, UI, Performance, State, and Navigation

### Integration Guides (separate skill — loaded on demand)
8 service integrations in a **separate skill** that loads only when you ask to connect a service:
Sentry, Firebase Push Notifications, Supabase (backend + realtime), Firebase Auth, Supabase Auth, Amplitude, Firebase Analytics, Analytics Facade (multi-provider).

Zero context bloat — the integrations don't load when you're just building screens.

### Ready-Made Patterns
25 copy-paste solutions for real-world tasks:

| # | Pattern | What it solves |
|---|---------|---------------|
| 1 | Async Data Screen | Loading / Error / Data states with `.when()` |
| 2 | Pull-to-Refresh | `RefreshIndicator` + `ref.refresh()` |
| 3 | Infinite Scroll | Cursor-based pagination with hasMore/isLoadingMore |
| 4 | Form with Validation | `HookConsumerWidget` + `GlobalKey<FormState>` + error mapping |
| 5 | Bottom Sheet | Modal with ScreenUtil sizing and drag handle |
| 6 | Search with Debounce | `Timer` debounce + search provider |
| 7 | Auth Guard | GoRouter redirect + auth notifier |
| 8 | Local Storage Toggle | SharedPreferences + Riverpod for theme/locale |
| 9 | Dio Error Interceptor | Global error mapping to `AppException` |
| 10 | Image Upload | Multipart upload with progress tracking |
| 11 | Service Singleton | Abstract interface + singleton for analytics, payments, etc. |
| 12 | Heavy Computation | `compute()` isolate for image processing, large JSON, crypto |
| 13 | Skeleton Loading | Shimmer animation for loading states |
| 14 | Empty State | Placeholder UI when data list is empty |
| 15 | Cache-First with TTL | `ref.keepAlive()` with timer-based cache invalidation |
| 16 | Optimistic Update | Instant UI update, server sync, rollback on failure |
| 17 | WebSocket with Reconnect | Stream provider with exponential backoff reconnect |
| 18 | Deep Linking | Query parameters, Universal Links, App Links, GoRouter |
| 19 | Multi-Step Form | Stepper with Freezed form state and step navigation |
| 20 | Debounced Server Validation | Email uniqueness check with loading/success/error UI |
| 21 | Connectivity Awareness | Offline banner + `connectivity_plus` stream |
| 22 | Retry with Backoff | Exponential backoff for transient failures |
| 23 | Family Provider | Parameterized detail screen with auto-dispose cache |
| 24 | Platform-Adaptive Dialog | iOS Cupertino / Android Material dialog switch |
| 25 | Socket.IO Client | Socket.IO events, rooms, auto-reconnect |

## Install

Inside Claude Code, run:

```
/plugin marketplace add 9evxxx/flutter-skill
/plugin install flutter-skill@9evxxx-flutter-skill
```

Or test without installing:

```bash
claude --plugin-dir ./flutter-skill
```

After installation, the skill activates automatically in any Flutter project (detects `pubspec.yaml` with `flutter` dependency). You can also invoke it manually with `/flutter-skill:flutter`.

## License

MIT
