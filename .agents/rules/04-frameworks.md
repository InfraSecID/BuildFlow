---
trigger: always_on
---

# 04 — Frameworks

## React
- Use Function Components + Hooks with strict TypeScript (`props` and state without `any`). Split components exceeding ~200 lines, use stable ID `key`s, and wrap feature boundaries in an `ErrorBoundary`.
- Leverage **Server Components (RSC)**, `Suspense` for streaming/loading boundaries, and *Concurrent rendering* primitives (`useTransition`, `useDeferredValue`) to keep the UI responsive.
- **State Management:** Prefer local and derived state first. Use **TanStack Query** for server state/caching, **Zustand** or **Jotai** for lightweight client state, and **Redux Toolkit** for complex global state.
- **Forms, Styling & Testing:** Use **React Hook Form + Zod** for form validation, **Tailwind CSS** or **CSS Modules** for styling, and test with **React Testing Library (RTL)** + **Vitest** + **Playwright**.

## Next.js
- Use the **App Router** with Server Components by default; add `'use client'` only to leaf components that require browser interactivity. Use **Turbopack** for fast local development.
- Leverage **Server Actions** (always validate inputs with `zod` and verify auth), **Middleware** for edge auth/redirects, and **Parallel Routes** (`@folder`) & **Intercepting Routes** (`(.)folder`) for complex modals and layouts.
- Optimize performance with **Streaming SSR**, **Partial Prerendering (PPR)**, `next/image`, `next/font`, and granular caching/revalidation (`revalidatePath`, `revalidateTag`).
- Use the **Metadata API** (`generateMetadata`, `opengraph-image`, `sitemap.ts`) for comprehensive SEO, strictly separate `NEXT_PUBLIC_*` variables from server-only modules (`server-only` package), and write unit + E2E tests.

## Vue / Nuxt
- Use the Composition API (`<script setup lang="ts">`) with strictly typed `defineProps` & `defineEmits`, small components with `slot`s, and **Pinia** + composables (`useX`) for state and logic reuse.
- **Nuxt 3 Details:** Leverage the **Nitro** server engine, validated *server routes* (`server/api/`), and understand when to use `useFetch` (convenience wrapper for URL fetching) vs `useAsyncData` (wrapping custom async logic/SDK calls) to prevent double-fetching during SSR hydration.
- Test components and user flows with **Vitest** + `@nuxt/test-utils` + **Playwright**.

## Svelte / SvelteKit
- **Svelte 5 Runes:** Use `$state`, `$derived`, `$effect` (sparingly, avoiding hidden side effects), and `$props` with strict TypeScript. Use Stores for cross-module global state.
- **SvelteKit Details:** Use server `load` functions (`+page.server.ts` / `+layout.server.ts`) for secure data fetching, **Form Actions** for mutations with *progressive enhancement* (`use:enhance`), and select the appropriate deployment **adapter** (`adapter-node`, `adapter-auto`, `adapter-static`, edge).
- Ensure code inside `$lib/server` never leaks to the client bundle, and test with **Vitest** + **Playwright**.

## Angular
- Use **Standalone Components**, `ChangeDetectionStrategy.OnPush`, lazy-loaded routes (`loadComponent`/`loadChildren`), and `strictTemplates` + strict TypeScript.
- **Signals Details:** Leverage `signal()`, `computed()`, `effect()`, and signal-based `input()`/`output()` for synchronous, fine-grained reactivity with minimal change detection overhead.
- **Essential RxJS Operators:** Choose the right flattening/filtering operators: `switchMap` (cancels stale requests, ideal for live search), `mergeMap` (runs concurrently), `concatMap` (queues sequentially), `exhaustMap` (ignores duplicate clicks during form submission), `debounceTime`, and `distinctUntilChanged`. Always clean up subscriptions using `AsyncPipe` or `takeUntilDestroyed()`.
- Encapsulate business logic in Injectable Services and test with **TestBed** / **Jest** / **Cypress**.

## Astro
- Follow **Islands Architecture**: ship static HTML with zero client-side JS by default and hydrate interactive components selectively using client directives (`client:load`, `client:visible`, `client:idle`).
- Use **Content Collections** with `zod` schema validation for type-safe Markdown/MDX content.
- Choose the appropriate rendering mode (**SSG** static, **SSR** on-demand, or per-page hybrid) and configure the official **adapter** (Node, Cloudflare, Vercel, Netlify) for your target platform.

## Remix
- Leverage **Nested Routing** so layouts and data fetching execute in parallel across URL segments without request waterfalls.
- Use `loader` functions for server reads and `action` functions for form mutations built around **Progressive Enhancement** (forms work before JS loads, enhanced with `useFetcher`/`useNavigation`).
- Define route-level `ErrorBoundary` components to isolate failures without crashing the entire page.

## SolidJS
- Leverage **Fine-Grained Reactivity** without a Virtual DOM: components execute once during setup rather than re-rendering on every state change.
- Use `createSignal`, `createMemo`, and `createEffect` primitives. Never destructure `props` directly so reactive property getters remain intact, and use `<Show>` and `<For>` for control flow.

## Qwik
- Leverage **Resumability** (resuming execution in the browser directly from serialized SSR state without hydration overhead).
- Use the `$` suffix (`component$`, `useTask$`) so the **Qwik Optimizer** automatically splits and lazy-loads JavaScript per user interaction.

## Flutter
- Break UI into small, reusable widgets with `const` constructors, use `ListView.builder` / `SliverList` for long lists, and keep business logic out of `build()`.
- **State Management:** Standardize consistently on **Riverpod** (compile-safe, declarative), **Bloc** (structured event-state streams for large teams), or **Provider**.
- **Core Ecosystem:** Use **go_router** for declarative navigation and deep links, **Freezed** for immutable models and sealed union states, **Dio** (configured with auth/retry/timeout interceptors) for networking, and **Drift** / **Isar** / **Hive** for local persistence.
- Write comprehensive unit tests, widget tests (`pumpWidget`), and integration tests.

## React Native
- Build modular components with `StyleSheet`, use `FlatList` / `FlashList` for large lists, and enable the **Hermes** engine for optimal startup time and memory footprint.
- Use **React Navigation** or **Expo Router** for native stack/tab navigation, and run animations on the UI thread with **Reanimated** + Gesture Handler.
- Leverage the **New Architecture** (TurboModules / JSI & Fabric) when building or integrating **Native Modules** to eliminate legacy bridge serialization bottlenecks.

## Expo
- Prefer the **Managed Workflow** / Continuous Native Generation (CNG / `prebuild`) for smooth SDK upgrades.
- Use **Config Plugins** (`app.config.ts`) to customize native Android/iOS configurations reproducibly without manually editing `android`/`ios` directories, and leverage **EAS** (Build, Submit, OTA Update).

## Jetpack Compose (Android)
- Build small, stateless, reusable Composables via *state hoisting*. Use `remember`, `rememberSaveable`, `derivedStateOf`, `collectAsStateWithLifecycle()`, and `LaunchedEffect` for side effects.
- **Architecture & Libraries:** Use **Hilt** (`@HiltViewModel`) for DI, **type-safe Navigation Compose** (Kotlin Serialization routes), **Paging 3** (`LazyPagingItems`) for paginated lists, and **Room + DataStore** for local persistence.
- **Performance & Testing:** Provide `@Preview` annotations with Material 3, eliminate frame jank using **Baseline Profiles + Macrobenchmark**, and test UIs with `ComposeTestRule` and screenshot tests (`Paparazzi` / `Roborazzi`).

## SwiftUI (iOS)
- Use `@Observable` (iOS 17+ Observation framework, more efficient than legacy `@StateObject`/`ObservableObject`) for ViewModels, and `@State` / `@Binding` / `@Environment` for view state.
- Use **NavigationStack + NavigationPath** for type-safe, programmatic navigation and deep linking.
- Choose **SwiftData** (modern, declarative) or **Core Data** for local persistence.
- Run asynchronous work using `async/await` + `.task {}` + `Actor`s, ensure UI updates run on `@MainActor`, and test with **XCTest** / **Swift Testing** + UI/snapshot tests.

## Tauri
- Build a lightweight, secure **Rust** backend: expose functions to the frontend via `#[tauri::command]` handlers that strictly validate all arguments.
- Manage multi-view **Windows** declaratively, harden **Security** through minimal Capabilities/ACLs + CSP, and enable the official **Updater** with cryptographic signature verification.

## Hono
- Optimize for **Edge Runtimes** (Cloudflare Workers, Deno, Bun, Node.js) with an ultra-small footprint built on Web Standard APIs (`Request`/`Response`).
- Use composable **Middleware** (CORS, JWT, logger, rate-limit), route-level **Validators** (`@hono/zod-validator`), and **Hono RPC** (`hc`) to share end-to-end API type contracts with clients without code generation.

## tRPC
- Build end-to-end **Type-Safe APIs** for full-stack TypeScript applications without intermediate schema compilation.
- Organize modular **Routers** per domain, validate inputs and outputs on every **Procedure** with `zod`, use **Middleware** to separate `publicProcedure` from `protectedProcedure` (authenticated context), and integrate the **Client** with TanStack Query.

## Fastify
- Leverage encapsulated **Plugins** (`fastify-plugin`) and **Decorators** to extend server instances in a type-safe manner.
- Define **JSON Schemas** (or TypeBox/Zod compilers) on requests and responses for automatic validation and high-speed JSON serialization (`fast-json-stringify`), and manage request lifecycles via **Hooks** (`onRequest`, `preHandler`, `onError`).

## Express / NestJS
- **Express:** Configure **middleware order** properly (security headers `helmet` → CORS → rate limiter → body parser → routes → 404 handler → 4-argument **error handler** last). Use `express-async-errors` (or Express 5 native async error handling) and schema validation (`zod`/`joi`); keep business logic out of route handlers.
- **NestJS:** Structure applications modularly using **Modules**, **Providers/Services** (DI), **Guards** (auth/RBAC), **Interceptors** (logging/transforms/timeouts), **Pipes** (`ValidationPipe` + DTOs), global **ExceptionFilters**, and **Custom Decorators** (`@CurrentUser()`). Test with Jest + `supertest`.

## AdonisJS
- Follow a clean TypeScript **MVC** architecture: keep controllers thin and delegate business logic to services.
- Use **Lucid ORM** (with versioned migrations, model hooks, and `preload` to prevent N+1 queries), **VineJS Validator** for fast request schema validation, and the built-in **Auth** module (sessions/access tokens/guards).

## RedwoodJS
- Maintain a clear separation between the frontend (`web`) and backend (`api`) workspaces. Use **Cells** for declarative data fetching (handling `Loading`, `Empty`, `Failure`, and `Success` states automatically).
- Encapsulate business logic inside backend **Services** exposed via **GraphQL**, and secure every query and mutation with **Directives** (`@requireAuth`, `@skipAuth`).

## Prisma / Drizzle
- **Schema & Migrations:** Define database schemas declaratively and run versioned migrations (`prisma migrate deploy` / `drizzle-kit migrate`); never use `db push` in production.
- **Query Builder & Type Safety:** Leverage end-to-end type inference, project only required columns (`select`) to avoid over-fetching, wrap multi-step writes in atomic transactions, and monitor generated SQL to prevent N+1 queries.

## FastAPI
- **Pydantic v2 Details:** Use `BaseModel`, `ConfigDict`, `field_validator`, and explicit `response_model` definitions for fast validation and serialization without leaking internal fields.
- **Database & Async:** Use **SQLAlchemy 2.0 Async** (`AsyncSession`, `select()` statements) + **Alembic** for versioned migrations. Use `async def` only with non-blocking async I/O libraries.
- **Auth, Background Jobs & Testing:** Implement **OAuth2 / JWT** auth via `Depends()`, use `BackgroundTasks` for lightweight tasks or **Celery / ARQ** (Redis) for heavy jobs, and test with `pytest` + `httpx.AsyncClient`.

## Django & DRF
- **Serializers & Views:** Enforce strict validation in DRF `Serializer`s (`validate_<field>`, `read_only_fields`), keep views thin, and place complex business logic in a service layer.
- **Advanced ORM:** Prevent N+1 queries using `select_related` and `prefetch_related` (`Prefetch`), and push computation to the database using `F()` expressions, complex `Q()` filters, `annotate()`, `aggregate()`, and `Subquery`/`OuterRef`.
- Use **Celery** for distributed task queues, **Django Channels** (ASGI) for WebSockets/realtime features, and test with `pytest-django` + `factory_boy`.

## Laravel
- **Eloquent Details:** Define relationships with explicit return types, enable `Model::shouldBeStrict()` in non-production environments (to catch lazy-loading N+1 queries and unfillable attributes), and validate/authorize via `FormRequest` + `Policy`.
- **Queues, Auth & Stack:** Offload heavy jobs to **Queues** monitored with **Laravel Horizon**, use **Sanctum** (SPA/mobile tokens) or **Passport** (full OAuth2) for authentication, build reactive UIs with **Livewire** or **Inertia.js**, and test with **Pest** / **PHPUnit**.

## Rails (Ruby on Rails)
- Follow **MVC** conventions with thin controllers and focused models (extract complex workflows into Service, Form, or Query Objects).
- Use **ActiveRecord** with versioned migrations, database-level constraints, and `includes`/`preload` to eliminate N+1 queries.
- Use **ActiveJob** (Sidekiq/Solid Queue) for background jobs and **ActionCable** / **Hotwire (Turbo + Stimulus)** for interactive realtime features.

## Phoenix (Elixir)
- Build low-latency, interactive realtime web applications using **Phoenix LiveView** without maintaining a separate SPA state layer.
- Use **Ecto** (`Changeset` for explicit data validation and `Multi` for composable transactions), and leverage **Channels**, **PubSub**, and **Presence** on the BEAM VM for millions of concurrent connections.

## Go Web Frameworks (Gin / Echo / Fiber / Chi / Stdlib)
- Enforce strict layering: `Handler` → `Service/Usecase` → `Repository`. Validate request structs at the boundary, propagate `context.Context` throughout the call chain, and install standard middleware (panic recovery, request ID, structured logging, timeout, rate limiting).

## Axum / Actix (Rust)
- Use type-safe **Extractors** (`Json`, `Path`, `Query`, `State`) to parse and validate incoming requests before entering handler functions.
- Share application **State** (`Arc<AppState>`) safely across threads, compose **Middleware** (Tower layers in Axum for tracing/timeouts/CORS/auth), and map custom domain errors to consistent HTTP responses via `IntoResponse` / `ResponseError`.

## Ktor (Kotlin)
- Organize **Routing** modularly per feature and leverage Ktor's **Plugin** system (`install()`) for cross-cutting concerns.
- Use `ContentNegotiation` with type-safe `kotlinx.serialization`, configure **Authentication** (JWT/OAuth/Session) and `StatusPages` for centralized exception handling, and run non-blocking handlers on Kotlin Coroutines.

## Vapor (Swift)
- Group type-safe **Routing** definitions inside `RouteCollection` implementations and validate request payloads using `Content` (`Codable` + `Validatable`).
- Use **Fluent ORM** (with versioned migrations and async queries), attach **Middleware** for error handling/CORS/rate-limiting, and protect endpoints with **Auth** guards (`Authenticatable`, JWT/Bearer).

## Spring Boot (Java / Kotlin)
- Separate `Controller` → `Service` (`@Transactional`) → `Repository` layers using constructor injection and immutable DTO/`record` types.
- **Spring Security Details:** Configure declarative `SecurityFilterChain` beans, **JWT / OAuth2 Resource Server** authentication, and granular **Method Security** (`@PreAuthorize`).
- **Spring Data JPA & Cloud:** Prevent N+1 queries using `@EntityGraph`, `JOIN FETCH`, or DTO projections; use **Spring Cloud** (Config, Gateway, Resilience4j, OpenFeign) for microservices; and test with `@WebMvcTest`, `@DataJpaTest`, and **Testcontainers**.

## .NET / ASP.NET Core
- **Minimal APIs vs Controllers:** Choose consistently (Minimal APIs with `TypedResults` and endpoint groups for lean microservices, or Controllers for large enterprise APIs) backed by built-in DI.
- **EF Core Details:** Version all migrations, always use `.AsNoTracking()` for read queries, prevent N+1 queries with `.Include()` or `.Select()` projections, and ensure raw SQL queries (`FromSqlInterpolated`) are parameterized.
- **Auth & Testing:** Use **ASP.NET Core Identity / JWT Bearer / OAuth2** with policy-based authorization (`RequireAuthorization`), and run integration tests using `WebApplicationFactory` + xUnit.

## gRPC
- Define service contracts in **Protobuf** (`.proto` v3) following strict backward/forward compatibility rules (never reuse or renumber released field tags).
- Use unary and **Streaming** RPCs (server, client, bidirectional) appropriately, enforce explicit deadlines/timeouts on every call, and use **Interceptors** for auth, structured logging, OpenTelemetry tracing, and standard gRPC status codes.

## GraphQL
- Design a clear, domain-oriented **Schema**, enforce query depth and complexity limits, and disable introspection in production for non-public APIs.
- Keep **Resolvers** thin (delegate to the service layer), always use **DataLoader** to batch and cache lookups against N+1 query amplification, and use **Apollo Federation** when composing multiple microservice subgraphs.
