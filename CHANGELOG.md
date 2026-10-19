# Changelog

Published builds only. Engineering detail for each build is in [BUILD.md](BUILD.md).

## 0.2.0 - 2026-10-19 (alpha)

Same application code as 0.1.0, re-signed with the **production release key**
and versioned so the two cannot be confused.

- Signed with `CN=TradComm, OU=Release`; the earlier build used the Android
  debug key, which would have stranded anyone who installed it.
- Android refuses to update across those keys, so if you installed 0.1.0 you
  must uninstall before installing this build. 0.1.0 is kept only for the
  record and is marked pre-release.

## 0.1.0 - 2026-10-19 (pre-release, superseded)

First public build.

- 300-question bank across fifteen NEC topics, bundled in the APK and usable
  offline.
- NEC 2020 / 2023 / 2026 cycle toggle filtering study, mock tests and the
  dashboard.
- Worked steps on every calculation item: rule, formula, substituted numbers,
  result and citation.
- 25-question mock exam with a 90-minute countdown, per-topic scoring and
  resume after the app is closed.
- Progress dashboard: accuracy per topic, attempts, mean drill time, weakest
  three topics.
- Timed "find the article" drills, timed separately from quiz accuracy.
- Free tier of 40 questions, counted by question.

Known limitations at this version: signed with a development key; no reachable
server, so content sync and licence redemption do not work; citations not yet
independently reviewed. See [README.md](README.md) and [BUILD.md](BUILD.md).
