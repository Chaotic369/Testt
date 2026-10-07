# EdgeRing

An original gesture-driven edge launcher for Android: touch a thin edge strip, keep the finger down,
drag across a ring overlay to a shortcut, release to launch. Built clean-room in Kotlin; no code,
assets, names or icons are taken from any other launcher.

- Package id: `com.example.edgering` (placeholder: change before any public release)
- Min SDK 26, target/compile SDK 35, Kotlin + Jetpack Compose
- Flavours: `play` (Play-safe, SAF only) and `sideload` (may declare broader permissions)

## Build and test
```
./gradlew assembleDebug lint test
```
Requires JDK 17 and the Android SDK (platform 35). CI does this on every push
(`.github/workflows/build.yml`) and uploads APKs and reports as artifacts.

## Status
See `PROGRESS.md` (current milestone, checklist, next steps) and `CHANGELOG.md`.

## M01 device test
1. Open the app, tap **Grant** for Display over other apps, allow notifications (Android 13+).
2. Pick a side, switch **Edge trigger active** on. A faint white line appears on that edge.
3. Open any app, touch the line, keep the finger down, slide onto a cell, release: the app should open.
4. Release outside the grid, or on an empty cell: nothing should open and the overlay should vanish.
5. Panic checks: tap **Stop** in the notification; flip the switch off; revoke the permission while running.
6. Note the "Last open latency" value shown in the app and whether the system back gesture fights the strip.
