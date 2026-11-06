# Test plan — apk-creator (Zerok Container)

## Scope

Testing covers the Python encrypted-blob core (`zerok/` — blob encrypt/decrypt
round-trips, blob-id behaviour, nonce randomness), and the Android debug build
of the thin app shell (`android/`, `io.zerok.container.MainActivity`) that this
repository's APK pipeline produces. The GUI (`gui/app.py`) and the storage
server (`server/app.py`) are not covered by automated tests today; they are
exercised manually per `initial_use.md`.

## Suites

| Suite | Covers | Run it | Runs in CI |
|---|---|---|---|
| pytest suite (`tests/test_blob_integrity.py`, `tests/test_invariant.py`) | XChaCha20-Poly1305 encrypt/decrypt round-trips via `zerok.crpyto`, blob encrypt/decrypt and blob-id non-determinism via `zerok.blob` | `pip install cryptography blake3 argon2-cffi requests pytest && pytest tests/ -q` | Yes — `Tests` workflow (`.github/workflows/tests.yml`) |
| Android debug build | Manifest merge, resource/link and compile of the Android shell at compileSdk/targetSdk 34 | `cd android && ./gradlew assembleDebug` (CI downloads Gradle 8.2 directly) | Yes — `Android APK Build` workflow (`.github/workflows/main.yml`) |
| CodeQL static analysis | Python and Java/Kotlin surfaces | GitHub CodeQL | Yes — `CodeQL` workflow (`.github/workflows/codeql.yml`), push/PR + weekly |

## CI gates

- Full pytest suite green on every push and pull request to `main`.
- `Android APK Build` green on every push to `main` and on `v*` tags; the
  workflow surfaces the Gradle "What went wrong" section as a check annotation
  on failure so the reason is readable from the run.
- CodeQL runs on push/PR and weekly; findings are dispositioned (fixed, or
  dismissed with a specific reason) on the house SLA.

## Cadence

- Every push / pull request: pytest suite, Android APK build, CodeQL.
- Weekly: CodeQL scheduled run (Monday 05:23 UTC).
- Every release candidate: the release-readiness run (checker + evidence
  reads), per the house Release-Readiness Standard.

## Coverage bar

No numeric floor; new functionality lands with tests per CONTRIBUTING.md.
