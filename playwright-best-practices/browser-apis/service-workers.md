# Service Worker Testing

## Table of Contents

1. [Service Worker Basics](#service-worker-basics)
2. [Registration & Lifecycle](#registration--lifecycle)
3. [Cache Testing](#cache-testing)
4. [Offline Testing](#offline-testing)
5. [Background Sync](#background-sync)

## Service Worker Basics

### Waiting for Service Worker Registration

```typescript
test("service worker registers", async ({ page }) => {
  await page.goto("/pwa-app");

  // Wait for SW to register
  const swRegistered = await page.evaluate(async () => {
    if (!("serviceWorker" in navigator)) return false;

    const registration = await navigator.serviceWorker.ready;
    return !!registration.active;
  });

  expect(swRegistered).toBe(true);
});
```

### Getting Service Worker State

```typescript
test("check SW state", async ({ page }) => {
  await page.goto("/");

  const swState = await page.evaluate(async () => {
    const registration = await navigator.serviceWorker.getRegistration();
    if (!registration) return null;

    return {
      installing: !!registration.installing,
      waiting: !!registration.waiting,
      active: !!registration.active,
      scope: registration.scope,
    };
  });

  expect(swState?.active).toBe(true);
  expect(swState?.scope).toContain(page.url());
});
```

### Service Worker Context

```typescript
test("access service worker", async ({ context, page }) => {
  await page.goto("/pwa-app");

  // Get all service workers in context
  const workers = context.serviceWorkers();

  // Wait for service worker if not yet available
  if (workers.length === 0) {
    await context.waitForEvent("serviceworker");
  }

  const sw = context.serviceWorkers()[0];
  expect(sw.url()).toContain("sw.js");
});
```

## Registration & Lifecycle

### Testing SW Installation

```typescript
test("verify SW install event", async ({ context, page }) => {
  // Listen for service worker before navigating
  const swPromise = context.waitForEvent("serviceworker");

  await page.goto("/pwa-app");

  const sw = await swPromise;

  // Evaluate in SW context
  const swVersion = await sw.evaluate(() => {
    // Access SW globals
    return (self as any).SW_VERSION || "unknown";
  });

  expect(swVersion).toBe("1.0.0");
});
```

### Unregistering Service Workers

```typescript
test.beforeEach(async ({ page }) => {
  await page.goto("/");

  // Unregister all service workers for clean state
  await page.evaluate(async () => {
    const registrations = await navigator.serviceWorker.getRegistrations();
    await Promise.all(registrations.map((r) => r.unregister()));
  });

  // Clear caches
  await page.evaluate(async () => {
    const cacheNames = await caches.keys();
    await Promise.all(cacheNames.map((name) => caches.delete(name)));
  });
});
```

## Cache Testing

### Verifying Cached Resources

```typescript
test("assets are cached", async ({ page }) => {
  await page.goto("/pwa-app");

  // Wait for SW to cache assets
  await page.evaluate(async () => {
    await navigator.serviceWorker.ready;
  });

  // Check cache contents
  const cachedUrls = await page.evaluate(async () => {
    const cache = await caches.open("app-cache-v1");
    const requests = await cache.keys();
    return requests.map((r) => r.url);
  });

  expect(cachedUrls).toContain(expect.stringContaining("/styles.css"));
  expect(cachedUrls).toContain(expect.stringContaining("/app.js"));
});
```

### Testing Cache Strategies

```typescript
test("cache-first strategy", async ({ page }) => {
  await page.goto("/pwa-app");

  // Wait for initial cache
  await page.waitForFunction(async () => {
    const cache = await caches.open("app-cache-v1");
    const keys = await cache.keys();
    return keys.length > 0;
  });

  // Block network for cached resources
  await page.route("**/styles.css", (route) => route.abort());

  const cssResponsePromise = page.waitForResponse((response) => {
    const url = new URL(response.url());
    return (
      url.origin === new URL(page.url()).origin &&
      url.pathname === "/styles.css" &&
      response.request().method() === "GET"
    );
  });

  // Reload - should work from cache
  await page.reload();

  const cssResponse = await cssResponsePromise;
  expect(cssResponse.fromServiceWorker()).toBe(true);
});
```

### Testing Cache Updates

```typescript
test("cache updates on new version", async ({ page }) => {
  await page.goto("/pwa-app");

  // Get initial cache
  const initialCacheKeys = await page.evaluate(async () => {
    const cache = await caches.open("app-cache-v1");
    const keys = await cache.keys();
    return keys.map((r) => r.url);
  });

  // Before this step, deploy the next build so /sw.js returns the v2 worker.
  // page.route() and context.route() cannot intercept the browser's update
  // request for the service worker script.

  // Trigger update
  await page.evaluate(async () => {
    const reg = await navigator.serviceWorker.ready;
    await reg.update();
  });

  // Verify new cache exists
  await page.waitForFunction(async () => {
    return await caches.has("app-cache-v2");
  });
});
```

## Offline Testing

This section covers **offline-first apps (PWAs)** that are designed to work offline using service workers, caching, and background sync. For testing **unexpected network failures** (error recovery, graceful degradation), see [error-testing.md](../debugging/error-testing.md#offline-testing).

### Simulating Offline Mode

```typescript
test("app works offline", async ({ page, context }) => {
  await page.goto("/pwa-app");

  // Ensure SW is active and content cached
  await page.evaluate(async () => {
    await navigator.serviceWorker.ready;
  });

  // Verify that the current page is actually available in Cache Storage
  await expect
    .poll(
      () =>
        page.evaluate(async (pageUrl) => {
          const cacheNames = await caches.keys();
          for (const name of cacheNames) {
            const cache = await caches.open(name);
            if (await cache.match(pageUrl)) return true;
          }
          return false;
        }, page.url()),
      {
        message: "wait for the PWA page to be cached",
        timeout: 10_000,
      },
    )
    .toBe(true);

  // Go offline
  await context.setOffline(true);

  // Navigate to cached page
  await page.reload();

  // Verify content loads
  await expect(page.getByRole("heading", { name: "Dashboard" })).toBeVisible();

  // Verify offline indicator
  await expect(page.getByText("Offline")).toBeVisible();

  // Go back online
  await context.setOffline(false);
  await expect(page.getByText("Offline")).not.toBeVisible();
});
```

### Testing Offline Fallback

```typescript
test("shows offline page for uncached routes", async ({ page, context }) => {
  await page.goto("/pwa-app");
  await page.evaluate(() => navigator.serviceWorker.ready);

  // Go offline
  await context.setOffline(true);

  // Navigate to uncached page
  await page.goto("/uncached-page");

  // Should show offline fallback
  await expect(page.getByText("You are offline")).toBeVisible();
  await expect(page.getByRole("button", { name: "Retry" })).toBeVisible();
});
```

### Testing Offline Form Submission

```typescript
test("queues form submission offline", async ({ page, context }) => {
  await page.goto("/pwa-app/form");

  // Go offline
  await context.setOffline(true);

  // Submit form
  await page.getByLabel("Message").fill("Offline message");
  await page.getByRole("button", { name: "Send" }).click();

  // Should show queued status
  await expect(page.getByText("Queued for sync")).toBeVisible();

  // Go online
  await context.setOffline(false);

  // Trigger sync (or wait for automatic)
  await page.evaluate(async () => {
    const reg = await navigator.serviceWorker.ready;
    // Manually trigger sync for testing
    await (reg as any).sync?.register("form-sync");
  });

  // Verify submission completed
  await expect(page.getByText("Message sent")).toBeVisible({ timeout: 10000 });
});
```

## Background Sync

### Testing Background Sync Registration

```typescript
test("registers background sync", async ({ page }) => {
  await page.goto("/pwa-app");

  // Register sync
  const syncRegistered = await page.evaluate(async () => {
    const reg = await navigator.serviceWorker.ready;
    if (!("sync" in reg)) return false;

    await (reg as any).sync.register("my-sync");
    return true;
  });

  expect(syncRegistered).toBe(true);
});
```

## Anti-Patterns to Avoid

| Anti-Pattern                   | Problem                 | Solution                                     |
| ------------------------------ | ----------------------- | -------------------------------------------- |
| Not clearing SW between tests  | Tests affect each other | Unregister SW in beforeEach                  |
| Not waiting for SW ready       | Race conditions         | Always await `navigator.serviceWorker.ready` |
| Testing in isolation only      | Misses real SW behavior | Test with actual caching                     |
| Hardcoded timeouts for caching | Flaky tests             | Wait for cache to populate                   |
| Ignoring SW update cycle       | Missing update bugs     | Test install, activate, update flows         |

## Related References

- **Network Failures**: See [error-testing.md](../debugging/error-testing.md#offline-testing) for unexpected network failure patterns
