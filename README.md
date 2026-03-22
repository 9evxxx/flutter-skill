# Flutter Skill for Claude Code

Stop prompting Claude how to write Flutter. Just install this skill and get production-ready code from the first message.

## The Problem

Without a skill, Claude Code generates Flutter apps with random architecture choices every time. StatefulWidget here, BLoC there, manual JSON parsing, hardcoded strings, no responsive sizing, forgotten `build_runner`. You end up spending more time correcting Claude than writing code yourself.

## The Solution

This skill locks Claude into a single, opinionated, production-grade Flutter architecture. Every response follows the same conventions. Every file lands in the right place. Every provider uses code-gen. Every dimension is responsive. No debates — just consistent, shippable code.

**What changes when the skill is active:**

- Claude follows a **strict workflow** — models first, then API layer, then providers, then UI, then routes, then build_runner. Every time.
- Claude checks **37 prioritized rules** before writing code — code generation rules are CRITICAL, styling rules are LOW. It knows what matters most.
- Claude avoids **26 specific anti-patterns** — no `ref.read()` in `build()`, no `StatefulWidget` for shared state, no hardcoded dimensions, no manual JSON parsing.
- Claude has **12 ready-made patterns** for the things you actually build — pagination, forms, search with debounce, auth guards, image uploads, pull-to-refresh.
- Claude runs a **pre-delivery checklist** before handing code back — build_runner, dart analyze, ScreenUtil on all dimensions, localization, file placement.

## Stack

| Layer | Technology |
|-------|-----------|
| State Management | Riverpod + Hooks (code-gen) |
| Routing | GoRouter (typed, code-gen) |
| HTTP Client | Dio |
| API Layer | Retrofit (code-gen) |
| Models | Freezed + JSON Serializable |
| Responsive UI | FlutterScreenUtil (390x844) |
| DI | Riverpod providers (no GetIt) |
| Localization | ARB / flutter_localizations |
| Assets | flutter_gen (code-gen) |
| Fonts | GoogleFonts |
| Local Storage | SharedPreferences |
| Code Generation | build_runner |

## What's Inside

### Behavioral Controls
- **When to Apply** — Must Use / Recommended / Skip triggers so Claude activates only when relevant
- **Priority Table** — 9 rule categories ranked CRITICAL to LOW — Claude checks the important things first
- **Step-by-Step Workflow** — deterministic process for creating features and modifying code

### Reference
- **37 Quick Reference Rules** — compact rules with IDs (CG-*, SM-*, PS-*, NM-*, RT-*, RU-*, LA-*, TS-*, CS-*)
- **Project Structure** — feature-first architecture with complete directory tree and naming conventions
- **Full Tech Stack Reference** — code examples for every layer of the stack

### Guard Rails
- **26 Anti-Patterns** — architecture, state management, UI, networking, and code generation mistakes to avoid
- **Pre-Delivery Checklist** — 17 quality gates across Build, Architecture, UI, State, and Navigation

### Ready-Made Patterns
12 copy-paste solutions for real-world tasks:

| # | Pattern | What it solves |
|---|---------|---------------|
| 1 | Async Data Screen | Loading / Error / Data states with `.when()` |
| 2 | Pull-to-Refresh | `RefreshIndicator` + `ref.refresh()` |
| 3 | Infinite Scroll | Pagination with scroll listener + family provider |
| 4 | Form with Validation | `HookConsumerWidget` + `GlobalKey<FormState>` |
| 5 | Bottom Sheet | Modal with ScreenUtil sizing and drag handle |
| 6 | Search with Debounce | `Timer` debounce + search provider |
| 7 | Auth Guard | GoRouter redirect + auth notifier |
| 8 | Local Storage Toggle | SharedPreferences + Riverpod for theme/locale |
| 9 | Dio Error Interceptor | Global 401/500 handling |
| 10 | Image Upload | Multipart upload with progress tracking |
| 11 | Skeleton Loading | Shimmer animation for loading states |
| 12 | Empty State | Placeholder UI when data list is empty |

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
