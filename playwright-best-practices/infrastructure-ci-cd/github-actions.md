# GitHub Actions for Playwright

## Table of Contents

1. [CLI Commands](#cli-commands)
2. [Workflow Patterns](#workflow-patterns)
3. [Scenario Guide](#scenario-guide)
4. [Common Mistakes](#common-mistakes)
5. [Troubleshooting](#troubleshooting)
6. [Related](#related)

> **When to use**: Automating Playwright tests on pull requests, main branch merges, or scheduled runs.

## CLI Commands

```bash
npx playwright install --with-deps    # browsers + OS dependencies
npx playwright test --reporter=github # PR annotations
```

## Workflow Patterns

### Basic Workflow

**Use when**: Starting a new project or running a small test suite.

```yaml
# .github/workflows/e2e.yml
name: E2E Tests

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

concurrency:
  group: e2e-${{ github.ref }}
  cancel-in-progress: true

env:
  CI: true

jobs:
  test:
    timeout-minutes: 30
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - run: npm ci

      - name: Cache browsers
        id: browser-cache
        uses: actions/cache@v4
        with:
          path: ~/.cache/ms-playwright
          key: pw-${{ runner.os }}-${{ hashFiles('package-lock.json') }}

      - name: Install browsers
        if: steps.browser-cache.outputs.cache-hit != 'true'
        run: npx playwright install --with-deps

      - name: Install OS dependencies
        if: steps.browser-cache.outputs.cache-hit == 'true'
        run: npx playwright install-deps

      - run: npx playwright test

      - name: Upload report
        uses: actions/upload-artifact@v4
        if: ${{ !cancelled() }}
        with:
          name: test-report
          path: playwright-report/
          retention-days: 14

      - name: Upload traces
        uses: actions/upload-artifact@v4
        if: failure()
        with:
          name: traces
          path: test-results/
          retention-days: 7
```

### Container-Based Execution

**Use when**: Reproducible environment matching local Docker setup, or runner OS dependencies cause issues.
**Avoid when**: Standard `ubuntu-latest` with `--with-deps` works fine.

```yaml
# .github/workflows/e2e-container.yml
name: E2E Tests (Container)

on:
  pull_request:
    branches: [main]

jobs:
  test:
    timeout-minutes: 30
    runs-on: ubuntu-latest
    container:
      image: mcr.microsoft.com/playwright:v1.48.0-noble

    steps:
      - uses: actions/checkout@v4

      - run: npm ci

      - name: Run tests
        run: npx playwright test
        env:
          HOME: /root

      - uses: actions/upload-artifact@v4
        if: ${{ !cancelled() }}
        with:
          name: test-report
          path: playwright-report/
          retention-days: 14
```

### Environment Secrets

**Use when**: Tests target staging/production with credentials.
**Avoid when**: Tests only run against local dev server.

```yaml
# .github/workflows/e2e-staging.yml
name: Staging Tests

on:
  push:
    branches: [main]
  workflow_dispatch:

jobs:
  test:
    timeout-minutes: 30
    runs-on: ubuntu-latest
    environment: staging

    env:
      CI: true
      BASE_URL: ${{ vars.STAGING_URL }}
      TEST_USER_PASSWORD: ${{ secrets.TEST_USER_PASSWORD }}
      API_TOKEN: ${{ secrets.API_TOKEN }}

    steps:
      - uses: actions/checkout@v4

      - run: npm ci

      - name: Cache browsers
        id: browser-cache
        uses: actions/cache@v4
        with:
          path: ~/.cache/ms-playwright
          key: pw-${{ runner.os }}-${{ hashFiles('package-lock.json') }}

      - name: Install browsers
        if: steps.browser-cache.outputs.cache-hit != 'true'
        run: npx playwright install --with-deps

      - name: Install OS dependencies
        if: steps.browser-cache.outputs.cache-hit == 'true'
        run: npx playwright install-deps

      - name: Run smoke tests
        run: npx playwright test --grep @smoke

      - uses: actions/upload-artifact@v4
        if: ${{ !cancelled() }}
        with:
          name: staging-report
          path: playwright-report/
          retention-days: 14
```

### Scheduled Runs

**Use when**: Full regression suite is too slow for every PR—run nightly instead.
**Avoid when**: Suite runs under 15 minutes and can run on every PR.

```yaml
# .github/workflows/nightly.yml
name: Nightly Regression

on:
  schedule:
    - cron: '0 3 * * 1-5'
  workflow_dispatch:

jobs:
  test:
    timeout-minutes: 60
    runs-on: ubuntu-latest

    env:
      CI: true
      BASE_URL: ${{ vars.STAGING_URL }}

    steps:
      - uses: actions/checkout@v4

      - run: npm ci

      - name: Install browsers
        run: npx playwright install --with-deps

      - name: Run full regression
        run: npx playwright test --grep @regression

      - uses: actions/upload-artifact@v4
        if: ${{ !cancelled() }}
        with:
          name: nightly-${{ github.run_number }}
          path: playwright-report/
          retention-days: 30

```

### Reusable Workflow

**Use when**: Multiple repositories share the same Playwright setup.
**Avoid when**: Single repo with one workflow.

```yaml
# .github/workflows/pw-reusable.yml
name: Playwright Reusable

on:
  workflow_call:
    inputs:
      node-version:
        type: string
        default: 'lts/*'
      test-command:
        type: string
        default: 'npx playwright test'
    secrets:
      BASE_URL:
        required: false
      TEST_PASSWORD:
        required: false

jobs:
  test:
    timeout-minutes: 30
    runs-on: ubuntu-latest

    env:
      CI: true
      BASE_URL: ${{ secrets.BASE_URL }}
      TEST_PASSWORD: ${{ secrets.TEST_PASSWORD }}

    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-node@v4
        with:
          node-version: ${{ inputs.node-version }}
          cache: npm

      - run: npm ci

      - name: Cache browsers
        id: browser-cache
        uses: actions/cache@v4
        with:
          path: ~/.cache/ms-playwright
          key: pw-${{ runner.os }}-${{ hashFiles('package-lock.json') }}

      - name: Install browsers
        if: steps.browser-cache.outputs.cache-hit != 'true'
        run: npx playwright install --with-deps

      - name: Install OS dependencies
        if: steps.browser-cache.outputs.cache-hit == 'true'
        run: npx playwright install-deps

      - name: Run tests
        run: ${{ inputs.test-command }}

      - uses: actions/upload-artifact@v4
        if: ${{ !cancelled() }}
        with:
          name: test-report
          path: playwright-report/
          retention-days: 14
```

**Calling the reusable workflow:**

```yaml
# .github/workflows/ci.yml
name: CI
on:
  pull_request:
    branches: [main]

jobs:
  e2e:
    uses: ./.github/workflows/pw-reusable.yml
    with:
      node-version: 'lts/*'
    secrets:
      BASE_URL: ${{ secrets.STAGING_URL }}
      TEST_PASSWORD: ${{ secrets.TEST_PASSWORD }}
```

## Scenario Guide

| Scenario | Approach |
|---|---|
| Cross-browser on PRs | Chromium only on PRs; all browsers on main |
| Staging/prod smoke tests | Separate workflow with `environment:` |
| Nightly full regression | `schedule` trigger + `workflow_dispatch` |
| Multiple repos, same setup | Reusable workflow with `workflow_call` |
| Reproducible env needed | Container job with Playwright image |

## Common Mistakes

| Mistake | Problem | Fix |
|---|---|---|
| No `concurrency` group | Duplicate runs waste minutes | Add `concurrency: { group: ..., cancel-in-progress: true }` |
| No browser caching | 60-90 seconds wasted per run | Cache `~/.cache/ms-playwright` |
| No `timeout-minutes` | Stuck jobs run for 6 hours | Set explicit timeout: 20-30 minutes |
| Artifacts only on failure | No report when tests pass | Use `if: ${{ !cancelled() }}` |
| Hardcoded secrets | Security risk | Use GitHub Secrets and Environments |
| All browsers on every PR | 3x CI cost | Chromium on PR; cross-browser on main |
| No artifact retention | Default 90-day fills storage | Set `retention-days: 7-14` |
| Missing `--with-deps` | Browser launch failures | Always use `npx playwright install --with-deps` |

## Troubleshooting

### Browser launch fails: "Missing dependencies"

**Cause**: Browsers restored from cache but OS dependencies weren't cached.

**Fix**: Run `npx playwright install-deps` on cache hit:

```yaml
- name: Install OS dependencies
  if: steps.browser-cache.outputs.cache-hit == 'true'
  run: npx playwright install-deps
```

### Tests pass locally but timeout in CI

**Cause**: CI runners have fewer resources than dev machines.

**Fix**: Reduce workers and increase timeouts:

```typescript
// playwright.config.ts
import { defineConfig } from '@playwright/test';

export default defineConfig({
  workers: process.env.CI ? '50%' : undefined,
  use: {
    actionTimeout: process.env.CI ? 15_000 : 10_000,
    navigationTimeout: process.env.CI ? 30_000 : 15_000,
  },
});
```

### `webServer` fails: "port already in use"

**Cause**: Zombie process from previous run.

**Fix**: Kill stale processes before starting:

```yaml
- name: Kill stale processes
  run: lsof -ti:3000 | xargs kill -9 2>/dev/null || true
```

### No PR annotations

**Cause**: `github` reporter not configured.

**Fix**: Add `github` reporter for CI:

```typescript
// playwright.config.ts
import { defineConfig } from '@playwright/test';

export default defineConfig({
  reporter: process.env.CI
    ? [['html', { open: 'never' }], ['github']]
    : [['html', { open: 'on-failure' }]],
});
```

## Related

- [test-tags.md](../core/test-tags.md) — tagging and filtering tests
- [reporting.md](reporting.md) — reporter configuration
- [docker.md](docker.md) — container images
