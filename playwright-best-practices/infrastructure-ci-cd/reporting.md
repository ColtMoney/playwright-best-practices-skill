# Test Reports & Artifacts

> **When to use**: Configuring readable test results and collecting debugging artifacts when tests fail.

## Recommended Configuration

Use a readable local reporter, an HTML report, and concise GitHub Actions output. Keep traces, screenshots, and videos only when they help investigate failures.

```typescript
// playwright.config.ts
import { defineConfig } from '@playwright/test';

export default defineConfig({
  retries: process.env.CI ? 2 : 0,

  reporter: process.env.CI
    ? [
        ['dot'],
        ['html', { open: 'never' }],
        ['github'],
      ]
    : [
        ['list'],
        ['html', { open: 'on-failure' }],
      ],

  use: {
    trace: 'on-first-retry',
    screenshot: 'only-on-failure',
    video: 'retain-on-failure',
  },
});
```

This configuration provides:

- readable test output locally;
- annotations in GitHub Actions;
- an HTML report after the run;
- traces for retried tests;
- screenshots and videos for failed tests.

## Viewing Reports and Traces

```bash
# Open the latest HTML report
npx playwright show-report

# Open a trace directly
npx playwright show-trace test-results/path-to-test/trace.zip
```

You can also open traces at [trace.playwright.dev](https://trace.playwright.dev/).

## Optional: Uploading the Report in GitHub Actions

If project policy allows it, upload the HTML report after test failures:

```yaml
- uses: actions/upload-artifact@v4
  if: ${{ failure() }}
  with:
    name: playwright-report
    path: playwright-report/
    retention-days: 14
```

Reports and attachments can contain passwords, tokens, request data, screenshots, and other sensitive information. Do not upload them unless artifact access and retention comply with project policy.

## Practical Defaults

| Setting | Recommended value |
|---|---|
| HTML report | Optional in CI |
| Trace | `on-first-retry` |
| Screenshot | `only-on-failure` |
| Video | `retain-on-failure` |
| Report retention | 7–14 days |

Avoid `trace: 'on'` and `video: 'on'` in CI because they create large artifacts and slow down uploads.

## Troubleshooting

### The HTML report is empty

Make sure the HTML reporter is configured and that the complete `playwright-report/` directory is preserved.

### A failed test has no trace

`trace: 'on-first-retry'` records a trace only when the test is retried. Enable retries in CI:

```typescript
retries: process.env.CI ? 2 : 0,
```

### Reports use too much storage

Keep screenshots and videos only for failed tests, use traces only on retry, and limit artifact retention to 7–14 days.
