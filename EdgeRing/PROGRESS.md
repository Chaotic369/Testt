# PROGRESS

## Status
- Zip number / milestone: 2 - M01 (overlay + trigger spike)
- Date/time of this checkpoint: 2026-10-08
- Build status: Gradle NOT RUN in sandbox (no Gradle/Maven access). M00 built and installed via the user's GitHub Actions workflow (WebPaper.yml, `gradle assembleDebug`) and ran on a real phone. M01 not yet built by CI.
- Tests: 20/20 written (CellGridTest 10, GestureTrackerTest 10); run here with kotlinc 2.0.21 + a minimal JUnit shim, NOT via `./gradlew test`. Lint: not run
- Known issues: none known
- CI status: M00 PASS (assembleDebug + install on device); M01 NOT RUN
- Verified here: geometry + tracker unit tests (shim); all non-Compose Kotlin (overlay/, service/) compiles with 0 warnings against android-34 stubs jar with small stubs for R, NotificationCompat, ContextCompat; all XML passes xmllint
- Verified by CI/device: M00 shell only (screenshot: Compose settings screen, version patched to 1.0.0)
- Not verified: Compose files (MainActivity, SettingsScreen) compile; Gradle build of M01; lint; every runtime behaviour of the overlay on a device
- Unverified assumptions: (1) startActivity from the service works while the overlay window is visible (background-activity-launch rules); (2) systemGestureExclusionRects on the trigger window stops the back gesture; (3) startForeground with FOREGROUND_SERVICE_TYPE_SPECIAL_USE + manifest property is accepted on Android 14/15; (4) `<queries>` MAIN/LAUNCHER intent exposes launcher apps; (5) full-screen NO_LIMITS overlay at (0,0) so raw coords == view coords (code converts via getLocationOnScreen anyway); (6) latency number = event time to first onDraw, excludes GPU/vsync

## Done (checked off, with file paths)
- [x] M00 foundation, flavours, CI, empty shell - see CHANGELOG
- [x] Pure hit-test + gesture logic with tests - `overlay/geometry/CellGrid.kt`, `GestureTracker.kt`, `app/src/test/.../geometry/`
- [x] Trigger strip view, overlay view, controller - `overlay/TriggerView.kt`, `OverlayView.kt`, `OverlayController.kt` (written, compile-checked, not run on device)
- [x] Foreground service + panic paths (notification Stop action, in-app switch, onDestroy cleanup, 20 s idle watchdog) - `service/EdgeService.kt`, `EdgeState.kt`
- [x] Permission UI (overlay + notifications), side choice, latency readout - `MainActivity.kt`, `ui/settings/SettingsScreen.kt` (not compile-checked)

## In progress / partially done
- [ ] M01 exit criteria still needing a device: overlay always removable (try Stop action, switch, revoke permission while running), no crash with permission missing, record latency in this file
- [ ] `SpikeAppSource.kt` is a STUB (first 12 launcher apps A-Z); replaced by the cached index in M03

## Next steps (ordered, each small enough for one session)
1. Run CI for M01; fix any compile/lint errors from the log (Compose files are the unverified part).
2. Device test M01 (checklist in README "M01 device test"); report latency, whether launch works, whether back gesture conflicts.
3. M02 data layer (Room entities, DataStore settings, repositories, migration test harness, backup JSON model).

## Decisions and conventions
- App name EdgeRing; package `com.example.edgering` (placeholder; sideload flavour adds `.sideload`)
- minSdk 26, target/compile SDK 35, JDK 17, Kotlin 2.0.21, AGP 8.7.3, Gradle 8.10.2 (AGP needs >= 8.9), Compose BOM 2024.10.01, kotlinx-coroutines-android 1.9.0
- UI: Jetpack Compose + Material3; overlay uses plain custom Views (no Compose in the gesture path)
- Overlay design: trigger window (24dp x 40% height, centred on chosen edge) receives the whole touch stream; full-screen overlay window is FLAG_NOT_TOUCHABLE, pre-added INVISIBLE, drawn only during a gesture; launch happens before hide
- Service returns START_NOT_STICKY for now (panic safety); revisit with boot/restart handling in M08
- Concurrency: coroutines only (service scope = SupervisorJob + Main.immediate); no AsyncTask/Handler
- Clean-room: never copy code/strings/layouts/icons/DB format from the reference app; no paywall bypass
- No signing config or keystores in repo; `allWarningsAsErrors` still off until CI shows real warning list
- Side selection and trigger geometry are hard-coded/in-memory until M02/M06

## Feature checklist (mirrors section 2 of the prompt)
- [ ] 2.1 Triggers (partial: one fixed trigger, left/right)  - [ ] 2.2 Overlay/gesture (partial: spike, not device-verified)
- [ ] 2.3 Zones  - [ ] 2.4 Shortcuts grid (spike 3x4 only)  - [ ] 2.5 Folders  - [ ] 2.6 Action shortcuts
- [ ] 2.7 Other launch types  - [ ] 2.8 Apps index A-Z  - [ ] 2.9 Small-hand mode  - [ ] 2.10 Appearance/theming
- [ ] 2.11 Behaviour/background  - [ ] 2.12 Onboarding/permissions (partial: permission screen)
- [ ] 2.13 Backup/restore  - [ ] 2.14 Monetisation  - [ ] 2.15 i18n/misc  - [ ] 2.16 File-system folders

## Open questions / UNKNOWN items still to verify
- Prompt section 8 items (folder hover/nesting, hotspot/lock-orientation, icon packs, free-tier limits, haptics...) - untouched
- Final package id and branding (placeholder)
- Monetisation model and free-tier limits (decide before M14)
- Which edge the user prefers by default; whether the 40% strip length feels right

## How to build and test
- `./gradlew assembleDebug lint test` (JDK 17, Android SDK 35); user's repo builds via `.github/workflows/WebPaper.yml` (assembleDebug only; add `test`/`lint` if wanted)
- Install: `./gradlew installPlayDebug` or `installSideloadDebug`
- Local pure-Kotlin test run used here: kotlinc + shim (not a replacement for `./gradlew test`)
