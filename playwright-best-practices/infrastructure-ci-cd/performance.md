# Test Execution Performance

> **When to use**: Making a Playwright test suite faster without reducing test isolation or reliability.

## Recommended Configuration

```typescript
// playwright.config.ts
import { defineConfig } from '@playwright/test';

export default defineConfig({
  fullyParallel: true,
  workers: process.env.CI ? '50%' : undefined,
  retries: process.env.CI ? 2 : 0,
});
```

- `fullyParallel` allows independent tests to run concurrently.
- `workers` controls how many test processes run at once.
- `undefined` uses Playwright's local default.
- Start with `50%` in CI and adjust based on available CPU and memory.

More workers are not always faster. Reduce the worker count when the machine runs out of memory, the application becomes overloaded, or tests become unstable.

## Preserve Test Isolation

Playwright creates a new browser context for every test. Keep this default so cookies, storage, and page state cannot leak between tests.

Avoid:

- sharing a `page` or browser context through `beforeAll`;
- depending on test execution order;
- using the same user or database record in parallel tests;
- storing mutable test state in module-level variables.

Use `beforeEach` for per-test navigation:

```typescript
test.describe('products', () => {
  test.beforeEach(async ({ page }) => {
    await page.goto('/products');
  });

  test('shows the heading', async ({ page }) => {
    await expect(page.getByRole('heading', { name: 'Products' })).toBeVisible();
  });
});
```

For shared backend data, create a unique prefix per worker:

```typescript
test('uses unique account data', async ({ request }, testInfo) => {
  const workerPrefix = `worker-${testInfo.workerIndex}`;
  const email = `${workerPrefix}-${crypto.randomUUID()}@example.com`;

  await request.post('/api/users', { data: { email } });
});
```

Use worker-scoped fixtures when setup should run once per worker. See [fixtures-hooks.md](../core/fixtures-hooks.md).

## Reuse Authentication

Do not perform a full UI login in every test. Authenticate once in a setup project, save `storageState`, and reuse it in test projects.

```typescript
export default defineConfig({
  projects: [
    {
      name: 'setup',
      testMatch: /auth\.setup\.ts/,
    },
    {
      name: 'chromium',
      dependencies: ['setup'],
      use: {
        storageState: '.auth/user.json',
      },
    },
  ],
});
```

Each parallel test should still use isolated application data, even when tests share the same authenticated state.

## Remove Unnecessary Delays

Avoid fixed waits:

```typescript
// Avoid
await page.waitForTimeout(2000);

// Prefer
await expect(page.getByText('Saved')).toBeVisible();
```

Also avoid repeating expensive setup that can safely run in a setup project or worker-scoped fixture.

## Diagnose Slow or Flaky Parallel Tests

```bash
# Compare parallel and single-worker execution
npx playwright test
npx playwright test --workers=1

# Reproduce intermittent failures
npx playwright test --repeat-each=10
```

If a failure disappears with `--workers=1`, check for shared users, database records, files, ports, or mutable variables.

Use the HTML report to find slow tests. Optimize the slowest setup and navigation steps before increasing the worker count.

## Checklist

- Enable `fullyParallel` only for independent tests.
- Tune workers to the machine's CPU and memory.
- Reuse authentication with `storageState`.
- Keep a fresh page and context for each test.
- Isolate users and backend data between workers.
- Replace fixed waits with web-first assertions.
