# Contributing to Zerok Container (apk-creator)

## Process (PR-flow discipline)

- Work happens on feature branches; **no direct pushes to main**.
- Open a **draft PR** -> get tests green (see below) -> the owner merges.
  Contributors do not merge.
- Each PR adds a CHANGELOG entry under `## [Unreleased]` and bumps the version
  (patch = fix, minor = feature) in the `VERSION` file.
- Merge commits reference the PR number; releases are tagged `vX.Y.Z` after merge.

## Tests

```bash
pip install cryptography blake3
pytest tests/
```

Android debug build (also what `.github/workflows/main.yml` runs):

```bash
cd android
chmod +x gradlew
./gradlew assembleDebug
```

APK output: `android/app/build/outputs/apk/debug/app-debug.apk`.

## Security

Encryption happens client-side only; keys never leave the client. See
`SECURITY.md` for the threat model before touching `zerok/`.
