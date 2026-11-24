# E2E Test Architecture

## Table of Contents

1. [E2E Tests](#e2e-tests)
2. [Common Mistakes](#common-mistakes)
3. [Related](#related)

> **When to use**: Structuring browser-based E2E tests for complete user flows.

## E2E Tests

**Ideal for**:

- Critical user flows that generate revenue (checkout, signup)
- Authentication flows (login, SSO, password reset)
- Multi-page workflows where state carries across navigation
- Smoke tests validating the entire stack
- Real-time collaboration requiring multiple browser contexts

```typescript
import { test, expect } from "@playwright/test";

test.describe("subscription flow", () => {
  test.beforeEach(async ({ page }) => {
    await page.goto("/account/upgrade");
  });

  test("upgrades to premium plan", async ({ page }) => {
    await test.step("select plan", async () => {
      await expect(
        page.getByRole("heading", { name: "Choose Your Plan" })
      ).toBeVisible();
      await page.getByRole("button", { name: "Select Premium" }).click();
    });

    await test.step("enter billing details", async () => {
      await page.getByLabel("Cardholder name").fill("Sam Johnson");
      await page.getByLabel("Billing address").fill("456 Oak Ave");
      await page.getByLabel("City").fill("Seattle");
      await page.getByRole("combobox", { name: "State" }).selectOption("WA");
      await page.getByLabel("Postal code").fill("98101");
      await page.getByRole("button", { name: "Continue" }).click();
    });

    await test.step("complete payment", async () => {
      await page.getByLabel("Card number").fill("5555555555554444");
      await page.getByLabel("Expiry").fill("09/29");
      await page.getByLabel("CVV").fill("456");
      await page.getByRole("button", { name: "Subscribe now" }).click();
    });

    await test.step("verify success", async () => {
      await page.waitForURL("**/account/subscription/success**");
      await expect(
        page.getByRole("heading", { name: "Welcome to Premium" })
      ).toBeVisible();
      await expect(page.getByText(/Subscription #\d+/)).toBeVisible();
    });
  });
});
```

## Common Mistakes

| Anti-Pattern                     | Problem                                             | Better Approach                                  |
| -------------------------------- | --------------------------------------------------- | ------------------------------------------------ |
| One giant E2E for entire feature | Long test failing somewhere with no clear cause     | Focused E2E per user flow; use `test.step()`     |

## Related

- [test-suite-structure.md](../core/test-suite-structure.md) — file structure and naming
- [authentication.md](../advanced/authentication.md) — auth flow patterns with `storageState`
- [pom-vs-fixtures.md](pom-vs-fixtures.md) — organizing shared test logic
