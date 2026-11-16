---
trigger: always_on
---

# 03 — Platforms

## Web
- Mobile-first responsive design, clear breakpoints, and accessible touch targets.
- Semantic HTML, meaningful `alt` text, ARIA attributes, and complete keyboard navigation.
- **Performance:** Optimize Core Web Vitals (LCP, FID/INP, CLS), lazy loading, code splitting, image optimization (WebP/AVIF with explicit dimensions), font loading (`font-display: swap`/preload), and HTTP caching.
- **SEO:** Complete meta tags, sequential heading hierarchy, `sitemap.xml`, `robots.txt`, canonical URLs, Open Graph tags, and structured data (JSON-LD).
- **PWA:** Service worker for asset/API caching, complete `manifest.json`, offline support, and respectful push notifications.
- **Security:** Enforce HTTPS, strict CSP, HSTS, `X-Frame-Options` / `frame-ancestors`, CORS allowlists, SRI for external scripts, and `HttpOnly` + `Secure` + `SameSite` cookies.
- **State Management & UX:** Validate on both client and server. Always handle loading, empty, and error states. Manage URL state (query params), `localStorage` (non-sensitive data only), and `IndexedDB` (large offline datasets) cleanly.
- **Testing & Browser Compatibility:** E2E testing (Playwright/Cypress), accessibility testing (`axe`), and visual regression testing. Verify compatibility via `caniuse` and configure transpilation + polyfills to match target browsers.

## Android
- **Jetpack & Architecture:** Clean Architecture + MVVM/MVI with lifecycle-aware components.
- **Compose:** Small, reusable Composables, state hoisting, `remember` / `rememberSaveable`, `LaunchedEffect` for side effects, and `collectAsStateWithLifecycle` for observing Flows.
- **Navigation:** Type-safe routes, verified deep links & App Links, and proper back stack management.
- **Room & DataStore:** Room (Entity, DAO, versioned migrations, reactive `Flow` queries). Use DataStore (Preferences/Proto) for key-value storage instead of plain `SharedPreferences`; store tokens exclusively in Encrypted storage / Android Keystore.
- **WorkManager:** Use for reliable background tasks with appropriate constraints (network/battery), retry + backoff policies, and expedited work for urgent tasks.
- **Hilt / Koin:** Consistent Dependency Injection with clear scoping (`@Singleton`, `@ViewModelScoped`, etc.).
- **UX, Target SDK & Security:** Target the latest stable SDK with an appropriate `minSdk`. Request permissions in context with a clear rationale. Support Dark Mode and localization. Design offline-first with conflict resolution. Enable R8/ProGuard in release builds.
- **Performance & Testing:** Keep APK/AAB size small, ship Baseline Profiles, measure with Macrobenchmark, detect memory leaks with LeakCanary, and enable crash reporting. Comprehensive testing: JUnit, MockK, Turbine (Flow testing), Compose UI testing, and Paparazzi (screenshot testing).
- **Play Console:** Complete the Data Safety form, provide an accurate privacy policy, and use staged rollouts for production releases.

## iOS
- **Architecture & HIG:** Follow Apple Human Interface Guidelines (HIG) and MVVM/Clean Architecture. Prevent retain cycles.
- **SwiftUI:** Use SwiftUI for new projects. Leverage `@Observable` (iOS 17+), `@State`, `@Binding`, and `@Environment`. Support Dark Mode, Dynamic Type, and VoiceOver.
- **Concurrency:** Use `async/await`, `Task`, `Actor` for state isolation, and `@MainActor` for all UI updates. Use `BGTaskScheduler` for background processing.
- **Networking:** Use `URLSession` with `async/await`, parse JSON with `Codable`, and keep App Transport Security (ATS) enabled at all times.
- **Persistence & Security:** Use SwiftData or Core Data for structured persistence. Use `UserDefaults` only for lightweight preferences—**never for tokens or secrets** (store credentials exclusively in the Keychain).
- **Push Notifications:** Integrate APNs, request permissions in context, and keep notification payloads secure (never include raw sensitive PII in push payloads).
- **Testing & Performance:** Write unit and UI tests with XCTest, and add snapshot tests for UI components. Profile memory leaks, allocations, and launch time using Xcode Instruments.
- **App Store:** Include a complete Privacy Manifest (`PrivacyInfo.xcprivacy`), clear permission usage descriptions in `Info.plist`, accurate App Privacy labels in App Store Connect, and test via TestFlight prior to release.

## Backend
- **API Design:** Choose REST vs GraphQL vs gRPC based on use case requirements. Enforce API versioning (`/v1/...`), boundary input validation, consistent response envelopes (`data`, `error`, `meta`), and pagination (cursor/offset).
- **Auth & Security:** Implement JWT (pinned algorithms, short expiration), OAuth2/OIDC, secure sessions, and MFA for sensitive operations. Enforce server-side authorization on every endpoint (anti-BOLA/IDOR).
- **Rate Limiting:** Apply rate limiting (token bucket / sliding window) per user, IP, or API key to prevent brute-force attacks and application-layer DDoS.
- **Caching:** Use Redis (explicit TTLs, connection pooling, graceful fallback), HTTP cache headers (`ETag`, `Cache-Control`), and CDNs for static assets/content.
- **Queue & Async:** Use RabbitMQ / Kafka / SQS for asynchronous processing. Enforce idempotent consumers, retries with exponential backoff + jitter, and Dead Letter Queues (DLQ).
- **Database:** Use versioned, backward-compatible migrations, index filtered/joined columns, prevent N+1 queries, and configure connection pools and timeouts properly.
- **Observability:** Emit structured JSON logs (free of PII/secrets), metrics (Prometheus), distributed traces (OpenTelemetry), and cross-service `correlation_id`s. Provide `/healthz` and `/readyz` probes and handle graceful shutdown.
- **Testing & Deployment:** Write unit, integration, contract, and load tests (k6/Locust). Deploy safely using blue-green or canary releases backed by a fast rollback plan.

## Network & Edge
- **Segmentation & Zero Trust:** Separate public, private application, and isolated database subnets. Enforce mTLS and Zero Trust Network Access.
- **Edge Protection:** Enable WAF, DDoS protection, CDN origin shield, DNSSEC, and modern TLS 1.2/1.3.

## Desktop
- **Electron:** Enforce strict security settings (`contextIsolation: true`, `sandbox: true`, `nodeIntegration: false`), restrict IPC channels through a validated preload script, and secure auto-updates.
- **Tauri:** Use a secure Rust backend, restrict command allowlists and permission scopes to the minimum required, and keep binary size small.
- **Qt:** Use clean signal/slot connections, decouple QML UI from C++/Python business logic, and package deployments and dependencies cleanly.
- **Cross-Platform & UX:** Integrate native OS APIs (menu bar, keyboard shortcuts, system tray, file system dialogs, native notifications) and ensure smooth offline operation.
- **Auto-Update & Distribution:** Enforce code signing and notarization (macOS/Windows), support delta updates to save bandwidth, provide a rollback mechanism, and keep installer size small.
- **Testing:** Write unit tests for core logic, IPC integration tests, and desktop E2E tests (Playwright / WebdriverIO / Spectron).

## Game (Unity/Godot)
- **General Architecture:** Decouple gameplay/simulation logic from rendering and input. Use State Machines or Behavior Trees for AI and animation.
- **Unity:** Leverage `ScriptableObject` for modular config/event data, `Addressables` for dynamic asset memory management, and DOTS (`ECS` + `Job System` + Burst) for heavy parallel computation.
- **Godot:** Structure Scenes and Nodes modularly via composition, use `signal`s for decoupled communication (call down, signal up), and choose `GDScript` for rapid gameplay iteration or `C#` / GDExtension for heavy computation.
- **Performance:** Use object pooling for frequently spawned/destroyed entities, apply LOD (Level of Detail) and draw call batching (static/dynamic/GPU instancing), avoid per-frame allocations inside `Update`/`_process`, and profile regularly.
- **Asset Pipeline:** Configure platform-appropriate texture compression (ASTC/BCn/ETC2), audio formats (Vorbis/ADPCM, streaming for BGM, in-memory for short SFX), and sprite atlases to minimize draw calls.
- **Save System:** Design versioned save formats (for smooth migration across updates), encrypt or checksum save files against corruption/tampering, and handle cloud sync conflict resolution.
- **Multiplayer:** Use a server-authoritative netcode architecture, mitigate latency (client-side prediction, entity interpolation, lag compensation, server reconciliation), and never trust client state.
- **Testing:** Write unit tests for math/gameplay logic, Play Mode tests, and automated smoke/bot tests for level scenarios.

## CLI / Terminal
- **Argument Parsing:** Use standard argument parsers per language: `clap` (Rust), `argparse`/`typer`/`click` (Python), `commander`/`yargs` (JS/TS), `cobra` (Go).
- **UX & Documentation:** Provide `--help`, `--version`, intuitive subcommands, shell completion scripts (`bash`, `zsh`, `fish`), and `man` pages.
- **Output & Streams:** Write primary data output to `stdout` and errors/logs/progress to `stderr`. Return proper exit codes (`0` for success, specific non-`0` codes for errors). Detect TTY before rendering ANSI colors (respect `NO_COLOR`), progress bars/spinners, or tables; provide `--json` and `--quiet` flags for safe piping and CI usage.
- **Configuration:** Support layered configuration with a clear precedence order: CLI Flags > Environment Variables > Project/User Config Files (`.toml`/`.yaml`/`.json`) > Defaults.
- **Testing & Distribution:** Test with unit tests, CLI integration tests, and terminal output snapshot tests. Distribute cross-platform via Homebrew, npm, pip/uv, `cargo install`, APT/RPM, or precompiled binary releases (GitHub Releases + checksums).

## Embedded / IoT
- **RTOS & Bare Metal:** Choose the execution model based on determinism requirements: bare metal (superloop + interrupts) or RTOS (`FreeRTOS`, `Zephyr`) with well-defined task priorities and priority-inversion protection.
- **Memory Management:** Under tight memory constraints, prefer static allocation, calculate per-task stack sizes to prevent stack overflow, and avoid runtime dynamic allocation (`malloc`/`free`) to prevent heap fragmentation.
- **Power Management:** Optimize power consumption using deep/light sleep modes, configure efficient wake sources (timers/GPIO interrupts), and power down idle peripherals to maximize battery life.
- **Communication:** Select protocols suited to bandwidth and range: `MQTT` (with TLS and appropriate QoS), `CoAP` (lightweight UDP), `BLE` (low-power GATT profiles), `LoRa`/`LoRaWAN` (long-range low-power), or `Zigbee`/`Thread`.
- **Reliability & Security:** Enable the Hardware Watchdog Timer (WDT) and brownout recovery. Enforce Secure Boot, cryptographic firmware signing, hardware credential storage (Secure Element/TPM), and encrypted OTA updates with dual-bank automatic rollback.
- **Testing:** Run host-based unit tests (with mocked HAL), simulation/emulation (QEMU/Renode), and automated Hardware-in-the-Loop (HIL) tests.

## Browser Extension
- **Manifest V3:** Use Manifest V3, run background logic as an ephemeral Service Worker (never persist state in background global variables), use `declarativeNetRequest` for network blocking/modification, and minimize `host_permissions`.
- **Security:** Enforce a strict CSP, use `sandbox` pages when isolated evaluation is needed, respect Content Script *isolated world* boundaries, never use `innerHTML` or remote code execution, and strictly validate every payload and `sender` in message passing (`runtime.sendMessage` / `postMessage`).
- **Storage:** Use `chrome.storage` (`storage.local` for device data, `storage.sync` for cross-browser preferences, `storage.session` for in-memory data); never store sensitive tokens in web page `localStorage`.
- **Permissions:** Follow least privilege (`activeTab` when sufficient), use `optional_permissions` requested at runtime only when a feature is triggered, and provide a clear privacy policy whenever handling user data.
- **Testing & Distribution:** Write unit tests for core logic, run browser E2E extension tests with Playwright/Puppeteer, and perform a manual security review before publishing to the Chrome Web Store, Firefox Add-ons (AMO), and Edge Add-ons.

## Other Platforms (Additional)
- **PWA (Progressive Web App):** Service worker with appropriate caching strategies (`Stale-While-Revalidate` / `Cache-First` / `Network-First`), complete web app manifest (icons, standalone display, theme color), offline fallback page, and Web Push API.
- **Serverless (Lambda / Cloud Functions / Workers):** Optimize cold starts (small bundle size, lazy connection initialization outside the handler), configure proportional timeouts and memory, ensure handlers are idempotent, and enforce per-function least-privilege IAM roles.
- **Microservices:** Use a service mesh (automatic mTLS, traffic shifting), apply circuit breakers + timeouts + bulkheads to prevent cascading failures, enforce strict API contracts, and implement end-to-end distributed tracing.
- **Blockchain / Web3:** Write secure smart contracts (reentrancy guards, access control, integer safety), integrate wallets with cryptographic signature verification (`EIP-712` / `SIWE`), optimize gas usage, and require static analysis + security audits before mainnet deployment.
- **AR / VR (Spatial Computing):** Use ARKit (iOS/visionOS), ARCore (Android), or WebXR. Maintain high, stable frame rates (72/90/120 FPS) to prevent motion sickness, optimize polygons/shaders, and respect user physical-space privacy.
- **TV (Android TV / tvOS / Smart TV):** Design for a 10-foot viewing distance (*10-foot UI* with large typography and high contrast), provide full D-Pad / remote control navigation (*focus navigation* that is always visible and never trapped), and optimize media streaming playback.
- **Wearable (Wear OS / watchOS):** Focus on micro-interactions (*glanceable UI* < 5 seconds), support *complications* and tiles, conserve battery and network usage (batch sync via companion device or opportunistic Wi-Fi/LTE), and protect health/sensor data.
- **IoT & Edge Computing:** Use lightweight protocols (`MQTT`, `CoAP`, `gRPC`/`Protobuf`), perform initial data filtering and aggregation at the *edge* (reducing latency and cloud bandwidth costs), and manage device fleet provisioning and monitoring centrally.
