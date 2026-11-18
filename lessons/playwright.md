# Playwright

**While recording, `routeFromHAR` passes requests on to the routes registered before it.** Context routes run in reverse order of registration, and a recording HAR route calls `fallback()`. So a catch-all route that blocks unknown hosts must let the recorded hosts through too, or recording aborts them. (verified: Playwright 1.65.1, 2026-11)

**Parallel tests that record to the same HAR overwrite each other.** Record one HAR per test (named by `testInfo.testId`) and merge them in global teardown, or record with `--workers=1`. (verified: Playwright 1.65.1, 2026-11)

**Headless Chromium can draw WebGL without a GPU** with `--use-angle=swiftshader --enable-unsafe-swiftshader`, on a laptop and on GitHub's Ubuntu runners alike. Expect it to be several times slower than a real GPU. (verified: Playwright 1.65.1, 2026-11)
