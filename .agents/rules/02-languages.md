---
trigger: always_on
---

# 02 — Languages

## TypeScript
- Enable strict mode (`strict: true`). Never use `any`; use `unknown` with type narrowing / type guards instead.
- Use `interface` for public object shapes and `type` for unions/intersections. Avoid `enum`; prefer string literal unions (`'pending' | 'success'`) or `as const` objects.
- Use `readonly` / `Readonly<T>` for immutable data structures and `as const` for literal type inference.
- Use `satisfies` to validate object shapes without widening inferred literal types, and use explicit `import type` and `export type` for type-only imports/exports.
- Handle `null`/`undefined` strictly. Always handle `Promise` rejections using `async/await` with `try/catch` (no floating or unhandled promises).

## JavaScript
- Use ESM (`import`/`export`) for new projects. Default to `const`, use `let` only when reassignment is needed, and never use `var`.
- Always use strict equality (`===`, `!==`), optional chaining (`?.`), nullish coalescing (`??`), and destructuring.
- Prefer `async/await` with `try/catch` over long `.then()` chains. Understand the event loop (*microtasks* like Promises vs *macrotasks* like `setTimeout`) to avoid blocking the thread.
- Avoid direct mutation whenever possible; prefer immutable array/object operations (`map`, `filter`, `toSorted`, spread) and `Object.freeze` for immutable constants/configs.

## Python
- Use type hints across all functions, including advanced `typing` constructs (`Protocol`, `TypedDict`, `Generic`, `TypeVar`, `Literal`). Run `ruff` + `mypy` + `black`.
- Never use wildcard imports (`from x import *`). Use f-strings, specific custom exceptions, and isolate dependencies in virtual environments (`venv`/`uv`/`poetry`).
- Use `async/await` + `asyncio` for concurrent I/O-bound workloads, and context managers (`with` / `async with`) for automatic resource cleanup.
- Use generators (`yield`) and iterators to process large datasets with minimal memory footprint (lazy evaluation).
- Use `dataclass` or `pydantic` v2 for fast, strict data schema validation.
- Write tests with `pytest`, leveraging modular `fixture`s and `@pytest.mark.parametrize` for table-driven test scenarios.

## Go
- Must pass `gofmt` + `go vet` + `golangci-lint`. Always return and check `error`, wrap context with `fmt.Errorf("...: %w", err)`, and inspect errors using `errors.Is` and `errors.As`.
- Use `defer` for resource cleanup (files, connections, mutex unlocks). Reserve `panic`/`recover` strictly for fatal startup invariants or top-level recovery middleware at the boundary.
- Pass `context.Context` as the first parameter for cancellation, deadlines, and timeouts. Define small interfaces on the consumer side.
- Manage concurrency safely with `goroutine` + `channel` + `select` (always ensure an exit path to prevent goroutine leaks) and the `sync` package (`Mutex`, `RWMutex`, `WaitGroup`, `Once`, `atomic`).
- Use Generics (Go 1.18+) for type-safe data structures and utility functions without sacrificing readability.
- Write table-driven tests (`t.Run`), run tests with the `-race` flag, and write benchmarks (`testing.B`) while profiling performance with `pprof`.

## Kotlin
- Avoid the non-null assertion operator (`!!`); use safe calls (`?.`), the Elvis operator (`?:`), or smart casts. Default to `val` and immutable collections.
- Use `suspend` functions with structured `CoroutineScope` (never use `GlobalScope`), and model reactive streams with `Flow`, `StateFlow` (UI state), and `SharedFlow` (one-off events).
- Leverage `data class`, `sealed class`, and `sealed interface` with exhaustive `when` expressions.
- Use `inline` functions with `reified` type parameters when runtime generic type access is needed, and `@JvmInline value class` for zero-allocation primitive wrappers.
- Use property delegation (`by lazy`, `by viewModels()`, custom delegates) and extension functions for clean utilities.
- Maintain safe Java interop: handle Java *platform types* (`T!`) defensively and annotate with `@JvmStatic`, `@JvmOverloads`, and `@JvmField` when called from Java.

## Swift
- Default to `let` and value types (`struct`, `enum`), and embrace *protocol-oriented programming*.
- Use `guard let` / `if let` for safe unwrapping; never force unwrap (`!`) in production code.
- Use modern Swift Concurrency: `async/await`, `Task` (with cooperative cancellation checks), `Actor` to isolate mutable state from data races, and `@MainActor` for all UI updates.
- Use `Codable` + `JSONDecoder`/`JSONEncoder` for serialization, and `Combine` or `AsyncSequence` for reactive streams.
- Apply property wrappers appropriately (`@State`, `@Binding`, `@Published`, `@Environment`) and manage ARC memory using `[weak self]` (use `unowned` only when lifetime is 100% guaranteed to outlive the closure).
- Use `@available` attributes and `#available` checks to maintain backward compatibility across OS versions.

## Dart
- Enable sound null safety, use `final` and `const` consistently, and use `late` only for lazy initialization where initialization before read is guaranteed.
- Use `async/await` + `Future` for single asynchronous operations and `Stream` for event sequences; offload heavy CPU work to an `Isolate` (`Isolate.run`) to keep UI rendering at 60/120 FPS.
- Leverage `mixin` and `extension` to share behavior without deep inheritance hierarchies, and use `sealed class` (Dart 3+) or `freezed` for union states and exhaustive pattern matching.
- Break UI into small widgets, keep business logic out of `build()`, use consistent state management (**Riverpod** / **Bloc**), and enforce `lints` + `dart analyze`.

## SQL
- Use `snake_case` naming, enforce parameterized queries, define explicit constraints (`PRIMARY KEY`, `FOREIGN KEY`, `UNIQUE`, `NOT NULL`, `CHECK`), and avoid `SELECT *`.
- Choose the appropriate `JOIN` type (`INNER`, `LEFT`, `RIGHT`, `FULL`, `CROSS`) and use CTEs (`WITH`) to break down complex queries for readability and maintainability.
- Leverage Window Functions (`ROW_NUMBER()`, `RANK()`, `DENSE_RANK()`, `LAG()`, `LEAD()`, `SUM() OVER (...)`) for analytics and ranking without expensive self-joins.
- Select index types based on query patterns: `B-tree` (default range/equality), `Hash` (exact equality), `GIN` (JSONB/arrays/full-text), or `GiST` (geospatial/ranges). Apply table partitioning (range/list/hash) for massive tables.
- Wrap critical mutations in a `TRANSACTION` with the appropriate *isolation level* (`READ COMMITTED`, `REPEATABLE READ`, `SERIALIZABLE`), version all migrations, and verify query plans with `EXPLAIN ANALYZE`.
- In ORMs: prevent N+1 queries with eager loading (`JOIN` / `preload` / `Include`) and use read-only tracking modes such as `AsNoTracking` (EF Core) for read queries.

## Rust
- Must pass `cargo fmt` and `cargo clippy`. Never use `.unwrap()` in production code; use `Result`, `Option`, the `?` operator, `thiserror` (for library errors), and `anyhow` (for application errors).
- Respect *lifetimes* and the *borrow checker*: prefer borrowing (`&T`, `&mut T`) over unnecessary `.clone()` calls.
- Use `trait` + `impl` for zero-cost static polymorphism and `dyn Trait` only when dynamic dispatch is required.
- Use `async/await` with the `tokio` runtime (never run blocking operations inside async tasks; offload to `spawn_blocking` when needed).
- Use macros (`macro_rules!` or procedural macros) judiciously to reduce boilerplate without obscuring diagnostics.
- **`unsafe` Guidelines:** Use `unsafe` only when strictly necessary (FFI, low-level primitives), keep the `unsafe` block as small as possible, and **always** document a `// SAFETY:` comment proving why memory invariants hold.
- Organize large projects with Cargo Workspaces and modular *feature flags*, and write both unit tests (`#[test]`) and integration tests in `tests/`.

## C#
- Enable Nullable Reference Types (`?`, use the null-forgiving `!` operator only when proven safe, and apply null-state attributes like `[NotNull]`, `[NotNullWhen]`) alongside Roslyn analyzers and Source Generators.
- Use `async/await` end-to-end with `Task`, `ValueTask` (for allocation-free hot paths), and `CancellationToken`; never block with `.Result` or `.Wait()`.
- Use `record` (reference type) and `record struct` (value type) for immutable data models, and leverage modern *pattern matching* (`switch` expressions, property/positional/list patterns).
- Understand LINQ semantics: distinguish *deferred execution* from immediate execution, and understand `IEnumerable<T>` (in-memory) vs `IQueryable<T>` (translated to SQL by the provider).
- Use built-in Dependency Injection (`Microsoft.Extensions.DependencyInjection`) with proper service lifetimes (`Transient`, `Scoped`, `Singleton`).
- In EF Core: version all migrations, prevent N+1 queries with `.Include()` or `.Select()` projections, and always use `.AsNoTracking()` for read-only queries.

## PHP
- Always declare `declare(strict_types=1);`, follow **PSR-12** coding style and **PSR-4** autoloading via Composer (with explicit version constraints and Composer scripts for linting/testing).
- Use complete type declarations (union types `A|B`, intersection types, `mixed`, `never`, `void`), `enum` (PHP 8.1+), `readonly` properties/classes (8.1/8.2+), and `#[Attribute]` (8.0+).
- Prefer `match` expressions (strict comparison and exhaustive) over legacy `switch` statements.
- Always use PDO or Query Builder with prepared statements, throw specific `Exception` subclasses (never use the `@` error suppression operator), and test with **PHPUnit** or **Pest**.

## C/C++
- Enforce `clang-format` and `clang-tidy`, maintain strict `const` correctness, and leverage compile-time evaluation (`constexpr`, `consteval`, `constinit`).
- Apply **RAII** strictly using smart pointers (`std::unique_ptr` by default, `std::shared_ptr` for shared ownership, and `std::weak_ptr` to break cyclic references).
- Master *move semantics* (`std::move`, rvalue references `&&`, Rule of Zero/Five) to avoid expensive copies, and constrain templates with C++20 `concept`s.
- Manage builds and dependencies with **CMake** + **Conan** / **vcpkg**.
- Enforce bounds checking (`std::span`, `.at()`), run sanitizers (**ASan**, **UBSan**, **TSan**) + Valgrind in CI, and write tests with **GoogleTest** or **Catch2**.

## Ruby
- Always include the `# frozen_string_literal: true` magic comment, enforce style with **RuboCop**, and manage gems with **Bundler** (`Gemfile` with explicit version constraints + committed `Gemfile.lock`).
- Understand the behavioral differences between a `block`, `Proc`, and `lambda` (especially argument arity checking and `return` semantics).
- Use `Module`s and *mixins* (`include`, `prepend`, `extend`) to compose behavior cleanly.
- Use *metaprogramming* (`define_method`, `method_missing`) sparingly and avoid monkey-patching core classes unless strictly necessary.
- Write comprehensive test suites using **RSpec** or **Minitest**.

## Java
- Use explicit types (never raw types) and modern Java features (Java 17/21+): `record`, `sealed class`/`interface`, and *pattern matching* for `instanceof` and `switch`.
- Use `Optional<T>` strictly for method return types that may be absent (never use `Optional` for class fields or method parameters).
- Use the **Stream API** judiciously (understand *lazy evaluation*; avoid `.parallelStream()` for I/O-bound tasks or shared thread pools) and use `CompletableFuture` or Virtual Threads for asynchronous workloads.
- Tune the JVM (heap sizing `-Xms`/`-Xmx` and Garbage Collector selection such as G1GC or ZGC) based on workload profiles.
- Manage dependencies centrally with Maven or Gradle Version Catalogs (`libs.versions.toml`), and test with **JUnit 5**, **Mockito**, and **Testcontainers**.

## Other Languages (Summary)
- **Scala**: Immutability, `Option`/`Either`, pattern matching, Scala 3 features (`given`/`using`, `enum`, `opaque type`), functional effect systems (`ZIO` / `Cats Effect`), `sbt` + `scalafmt`.
- **Elixir**: Immutability, OTP (`GenServer` + `Supervisor`), **Phoenix** + **LiveView**, **Ecto** (database wrapper/changesets), **Oban** (background jobs), `mix format` + `credo`.
- **Erlang**: Pattern matching, OTP behaviours (`gen_server`, `gen_statem`, `supervisor`), static analysis with `dialyzer`, `rebar3` + `erlfmt`.
- **Haskell**: Strong type system, pure functions, `GADT`, `type class`es, *monad transformers* (`mtl`), `lens`/optics, `hlint` + `ormolu`.
- **Clojure**: Immutable persistent data structures, pure functions, `clojure.spec` / `malli`, **re-frame** (ClojureScript state), **Datomic**, `cljfmt` + `clj-kondo`.
- **F#**: Immutability, `Option`/`Result`, *computation expressions* (`async`, `task`), *type providers*, **Giraffe** web framework, `fantomas`.
- **OCaml**: Strong type inference, module system + *functors*, `GADT`s, `ppx` preprocessors, `dune` + `ocamlformat` + `merlin`.
- **R**: `tidyverse` ecosystem (`dplyr`, `ggplot2`), `tidymodels`, interactive `shiny` apps, vectorized operations, `lintr` + `styler` + `testthat`.
- **Julia**: Type stability, *multiple dispatch*, metaprogramming *macros*, dot *broadcasting* (`f.(x)`), GPU computing (`CUDA.jl`), `JuliaFormatter`.
- **MATLAB**: Vectorized matrix operations, array preallocation before loops, **Simulink** modeling, official *Toolboxes*, and C/C++ integration via **MEX**.
- **Perl**: Always `use strict; use warnings;`, modern OOP with **Moose**/**Moo**, parameterized DB access via **DBI**, **PSGI**/Plack web interface, `perltidy` + `perlcritic`.
- **Lua**: Always use `local` variables, manage state with *metatables*, cooperative concurrency with *coroutines*, FFI/performance tuning on **LuaJIT**, `stylua` + `luacheck` + `busted`.
- **Shell/Bash**: Always start with `set -euo pipefail`, quote all variables (`"$var"`), use `trap` for signal/exit cleanup, parse arguments with `getopts`, leverage *process substitution* (`<(...)`) & *here-docs* (`<<'EOF'`), `shellcheck` + `shfmt`, `bats`.
- **PowerShell**: Enable `Set-StrictMode`, use the *object pipeline* (avoid raw text parsing), build advanced *cmdlets* (`CmdletBinding`), declarative **DSC**, `PSScriptAnalyzer` + `Pester`.
- **Solidity**: Pin compiler pragma, prevent reentrancy (CEI pattern + guard), strict access control, *gas optimization* (storage packing, `calldata`, custom errors), secure *proxy / upgradeable patterns* (UUPS/Transparent), test with Foundry/Hardhat, and require security audits.
- **Zig**: Explicit *allocator* passing, compile-time metaprogramming via `comptime`, error unions (`!T`) + `defer`/`errdefer`, native *build system* (`build.zig`), `zig fmt`.
- **Nim**: Static typing, hygienic *templates* & AST *macros*, `async` programming, package management with `nimble` + `nimpretty`, avoid unsafe casts.
- **Crystal**: Type inference + nilable checks, metaprogramming *macros*, lightweight concurrency via *fibers* + *channels* (CSP), `crystal tool format` + `spec`.
- **Groovy**: Use `@CompileStatic` in production, build *type-safe DSLs*, **Gradle** build scripts, and BDD testing with **Spock**.
- **VB.NET**: Always set `Option Strict On` + `Option Explicit On`, leverage **LINQ**, `Async`/`Await`, proper *nullable* handling, and xUnit/NUnit.
- **Fortran**: Always set `implicit none`, avoid `goto`, encapsulate code in `module`s, use efficient *array* slicing, distributed parallel computing with *coarrays*, `pFUnit`.
- **COBOL**: Structured programming (avoid `GO TO`), sequential/indexed *file* record handling (VSAM), *database* integration (embedded SQL/DB2) & **CICS** transactions, test with GnuCOBOL.
- **Assembly**: Document register contracts per block, obey ABI *calling conventions* (caller/callee-saved registers & stack alignment), optimize vector instructions with **SIMD** (SSE/AVX/NEON) & *cache locality*, verify in emulators.
- **Ada**: Strong typing & range constraints, safe concurrency with *tasking* & *protected objects*, formal verification using the **SPARK** subset, `gnatcheck` + `AUnit`.
- **Prolog**: Declarative predicates, use cut (`!`) sparingly, leverage **CLP** (*Constraint Logic Programming*), **DCG** (*Definite Clause Grammars*) for parsing, *tabling* (memoization), and `PLUnit`.
- **Lisp/Scheme**: Immutability, hygienic *macros* (`syntax-rules`/`defmacro`), *first-class continuations* (`call/cc`), **REPL**-driven development, and dialect-standard formatters.
- **Elm**: Pure functions, **The Elm Architecture (TEA)**, manage external effects via `Cmd`/`Sub` (*subscriptions*), safe JS interop via `port`s, `elm-format` + `elm-review` + `elm-test`.
- **ReasonML/ReScript**: Sound type system, powerful *module* & *functor* system, validated *JS interop* bindings (`@val`, `@send`, `@module`), built-in formatter.
- **WebAssembly (Wasm)**: Restrict and validate function *imports/exports*, enforce bounds on *linear memory* and indirect function *tables*, and grant minimal capabilities via **WASI**.
