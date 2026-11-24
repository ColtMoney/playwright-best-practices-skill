---
name: playwright-best-practices
description: Use when writing Playwright tests, fixing flaky tests, debugging failures, implementing Page Object Model, configuring CI/CD, optimizing performance, mocking APIs, handling authentication or OAuth, file uploads/downloads, date/time mocking, WebSockets, multi-tab/popup flows, mobile/responsive layouts, touch gestures, error handling, offline mode, multi-user collaboration, console error monitoring, global setup/teardown, test annotations (skip, fixme, slow), test tags (@smoke, @fast, @critical, filtering with --grep), project dependencies, security testing (XSS, CSRF, auth), and service workers/PWA. Covers E2E, visual, and security testing.
license: MIT
metadata:
  author: currents.dev
  version: "1.2"
---

# Playwright Best Practices

This skill provides guidance for Playwright test development, from writing new tests to debugging and maintaining existing test suites.

## Activity-Based Reference Guide

Consult these references based on what you're doing:

### Writing New Tests

**When to use**: Creating new test files, writing test cases, implementing test scenarios

| Activity                            | Reference Files                                                                                                                               |
| ----------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------- |
| **Writing E2E tests**               | [test-suite-structure.md](core/test-suite-structure.md), [locators.md](core/locators.md), [assertions-waiting.md](core/assertions-waiting.md) |
| **Structuring test code with POM**  | [page-object-model.md](core/page-object-model.md), [test-suite-structure.md](core/test-suite-structure.md)                                    |
| **Setting up test data/fixtures**   | [fixtures-hooks.md](core/fixtures-hooks.md), [test-data.md](core/test-data.md)                                                                |
| **Handling authentication**         | [authentication.md](advanced/authentication.md), [authentication-flows.md](advanced/authentication-flows.md)                                  |
| **Testing date/time features**      | [clock-mocking.md](advanced/clock-mocking.md)                                                                                                 |
| **Testing file upload/download**    | [file-operations.md](testing-patterns/file-operations.md), [file-upload-download.md](testing-patterns/file-upload-download.md)                |
| **Testing forms/validation**        | [forms-validation.md](testing-patterns/forms-validation.md)                                                                                   |
| **Testing file drop zones**         | [drag-drop.md](testing-patterns/drag-drop.md)                                                                                                 |
| **Testing security (XSS, CSRF)**    | [security-testing.md](testing-patterns/security-testing.md)                                                                                   |
| **Using test annotations**          | [annotations.md](core/annotations.md)                                                                                                         |
| **Using test tags**                 | [test-tags.md](core/test-tags.md)                                                                                                             |

### Mobile & Responsive Testing

**When to use**: Testing mobile devices, touch interactions, responsive layouts

| Activity                        | Reference Files                                                                  |
| ------------------------------- | -------------------------------------------------------------------------------- |
| **Device emulation**            | [mobile-testing.md](advanced/mobile-testing.md)                                  |
| **Touch gestures (swipe, tap)** | [mobile-testing.md](advanced/mobile-testing.md)                                  |
| **Viewport/breakpoint testing** | [mobile-testing.md](advanced/mobile-testing.md)                                  |
| **Mobile-specific UI**          | [mobile-testing.md](advanced/mobile-testing.md), [locators.md](core/locators.md) |

### Real-Time & Browser APIs

**When to use**: Testing WebSockets and multi-tab flows

| Activity                        | Reference Files                                                                          |
| ------------------------------- | ---------------------------------------------------------------------------------------- |
| **WebSocket/real-time testing** | [websockets.md](browser-apis/websockets.md)                                              |
| **Multi-tab/popup flows**       | [multi-context.md](advanced/multi-context.md)                                            |
| **OAuth popup handling**        | [multi-context.md](advanced/multi-context.md)                                            |

### Debugging & Troubleshooting

**When to use**: Test failures, element not found, timeouts, unexpected behavior

| Activity                                          | Reference Files                                                                                                                                |
| ------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------- |
| **Debugging test failures**                       | [debugging.md](debugging/debugging.md), [assertions-waiting.md](core/assertions-waiting.md)                                                    |
| **Fixing flaky tests**                            | [flaky-tests.md](debugging/flaky-tests.md), [debugging.md](debugging/debugging.md), [assertions-waiting.md](core/assertions-waiting.md)        |
| **Debugging flaky parallel runs**                 | [flaky-tests.md](debugging/flaky-tests.md), [performance.md](infrastructure-ci-cd/performance.md), [fixtures-hooks.md](core/fixtures-hooks.md) |
| **Ensuring test isolation / avoiding state leak** | [flaky-tests.md](debugging/flaky-tests.md), [fixtures-hooks.md](core/fixtures-hooks.md), [performance.md](infrastructure-ci-cd/performance.md) |
| **Fixing selector issues**                        | [locators.md](core/locators.md), [debugging.md](debugging/debugging.md)                                                                        |
| **Investigating timeout issues**                  | [assertions-waiting.md](core/assertions-waiting.md), [debugging.md](debugging/debugging.md)                                                    |
| **Using trace viewer**                            | [debugging.md](debugging/debugging.md)                                                                                                         |
| **Debugging race conditions**                     | [flaky-tests.md](debugging/flaky-tests.md), [debugging.md](debugging/debugging.md), [assertions-waiting.md](core/assertions-waiting.md)        |
| **Debugging console/JS errors**                   | [console-errors.md](debugging/console-errors.md), [debugging.md](debugging/debugging.md)                                                       |

### Error & Edge Case Testing

**When to use**: Testing error states, offline mode, network failures, validation

| Activity                       | Reference Files                                                                                       |
| ------------------------------ | ----------------------------------------------------------------------------------------------------- |
| **Error boundary testing**     | [error-testing.md](debugging/error-testing.md)                                                        |
| **Network failure simulation** | [error-testing.md](debugging/error-testing.md)                                                    |
| **Offline mode testing**       | [error-testing.md](debugging/error-testing.md), [service-workers.md](browser-apis/service-workers.md) |
| **Service worker testing**     | [service-workers.md](browser-apis/service-workers.md)                                                 |
| **Loading state testing**      | [error-testing.md](debugging/error-testing.md)                                                        |
| **Form validation testing**    | [error-testing.md](debugging/error-testing.md)                                                        |

### Multi-User & Collaboration Testing

**When to use**: Testing features involving multiple users, roles, or real-time collaboration

| Activity                       | Reference Files                                                                      |
| ------------------------------ | ------------------------------------------------------------------------------------ |
| **Multiple users in one test** | [multi-user.md](advanced/multi-user.md)                                              |
| **Real-time collaboration**    | [multi-user.md](advanced/multi-user.md), [websockets.md](browser-apis/websockets.md) |
| **Role-based access testing**  | [multi-user.md](advanced/multi-user.md)                                              |
| **Concurrent action testing**  | [multi-user.md](advanced/multi-user.md)                                              |

### Architecture Decisions

**When to use**: Choosing test patterns, deciding between approaches, planning test architecture

| Activity                     | Reference Files                                           |
| ---------------------------- | --------------------------------------------------------- |
| **POM vs fixtures decision** | [pom-vs-fixtures.md](architecture/pom-vs-fixtures.md)     |
| **E2E scenario structure**   | [test-architecture.md](architecture/test-architecture.md) |
| **Test suite structure**     | [test-suite-structure.md](core/test-suite-structure.md)   |

### Framework-Specific Testing

**When to use**: Testing Vue applications

| Activity                  | Reference Files             |
| ------------------------- | --------------------------- |
| **Testing Vue apps** | [vue.md](frameworks/vue.md) |

### Refactoring & Maintenance

**When to use**: Improving existing tests, code review, reducing duplication

| Activity                             | Reference Files                                                                                            |
| ------------------------------------ | ---------------------------------------------------------------------------------------------------------- |
| **Refactoring to Page Object Model** | [page-object-model.md](core/page-object-model.md), [test-suite-structure.md](core/test-suite-structure.md) |
| **Improving test organization**      | [test-suite-structure.md](core/test-suite-structure.md), [page-object-model.md](core/page-object-model.md) |
| **Extracting common setup/teardown** | [fixtures-hooks.md](core/fixtures-hooks.md)                                                                |
| **Replacing brittle selectors**      | [locators.md](core/locators.md)                                                                            |
| **Removing explicit waits**          | [assertions-waiting.md](core/assertions-waiting.md)                                                        |
| **Creating test data factories**     | [test-data.md](core/test-data.md)                                                                          |
| **Configuration setup**              | [configuration.md](core/configuration.md)                                                                  |

### Infrastructure & Configuration

**When to use**: Setting up projects, configuring CI/CD, optimizing performance

| Activity                                | Reference Files                                                                                                          |
| --------------------------------------- | ------------------------------------------------------------------------------------------------------------------------ |
| **Configuring Playwright project**      | [configuration.md](core/configuration.md), [projects-dependencies.md](core/projects-dependencies.md)                     |
| **Setting up CI/CD pipelines**          | [ci-cd.md](infrastructure-ci-cd/ci-cd.md), [github-actions.md](infrastructure-ci-cd/github-actions.md)                   |
| **GitHub Actions setup**                | [github-actions.md](infrastructure-ci-cd/github-actions.md)                                                              |
| **Docker/container setup**              | [docker.md](infrastructure-ci-cd/docker.md)                                                                              |
| **Global setup & teardown**             | [global-setup.md](core/global-setup.md)                                                                                  |
| **Project dependencies**                | [projects-dependencies.md](core/projects-dependencies.md)                                                                |
| **Optimizing test performance**         | [performance.md](infrastructure-ci-cd/performance.md), [test-suite-structure.md](core/test-suite-structure.md)           |
| **Configuring parallel execution**      | [performance.md](infrastructure-ci-cd/performance.md)                                            |
| **Isolating test data between workers** | [fixtures-hooks.md](core/fixtures-hooks.md), [performance.md](infrastructure-ci-cd/performance.md)                       |
| **Test reporting/artifacts**            | [reporting.md](infrastructure-ci-cd/reporting.md)                                                                        |

### Advanced Patterns

**When to use**: Complex scenarios and advanced test configuration

| Activity                             | Reference Files                                                                                              |
| ------------------------------------ | ------------------------------------------------------------------------------------------------------------ |
| **Mocking API responses**            | [test-suite-structure.md](core/test-suite-structure.md)                                         |
| **Custom fixtures**                  | [fixtures-hooks.md](core/fixtures-hooks.md)                                                                  |
| **Advanced waiting strategies**      | [assertions-waiting.md](core/assertions-waiting.md)                                                          |
| **Failing on console errors**        | [console-errors.md](debugging/console-errors.md)                                                             |
| **Security testing (XSS, CSRF)**     | [security-testing.md](testing-patterns/security-testing.md)                                                  |
| **Test annotations (skip, fixme)**   | [annotations.md](core/annotations.md)                                                                        |
| **Test tags (@smoke, @fast)**        | [test-tags.md](core/test-tags.md)                                                                            |
| **Test steps for reporting**         | [annotations.md](core/annotations.md)                                                                        |

## Quick Decision Tree

```
What are you doing?
│
├─ Writing a new test?
│  ├─ E2E test → core/test-suite-structure.md, core/locators.md, core/assertions-waiting.md
│  ├─ Mobile/responsive test → advanced/mobile-testing.md
│  ├─ Multi-user test → advanced/multi-user.md
│  ├─ Form validation test → testing-patterns/forms-validation.md
│  └─ File drop zone test → testing-patterns/drag-drop.md
│
├─ Testing specific features?
│  ├─ File upload/download → testing-patterns/file-operations.md, testing-patterns/file-upload-download.md
│  ├─ Date/time dependent → advanced/clock-mocking.md
│  ├─ WebSocket/real-time → browser-apis/websockets.md
│  ├─ OAuth popup flow → advanced/multi-context.md
│  ├─ Service workers/PWA → browser-apis/service-workers.md
│  ├─ Security (XSS, CSRF) → testing-patterns/security-testing.md
│
├─ Architecture decisions?
│  ├─ POM vs fixtures → architecture/pom-vs-fixtures.md
│  ├─ E2E scenario structure → architecture/test-architecture.md
│  └─ Test suite structure → core/test-suite-structure.md
│
├─ Framework-specific testing?
│  └─ Vue app → frameworks/vue.md
│
├─ Authentication testing?
│  ├─ Basic auth patterns → advanced/authentication.md
│  └─ Complex flows (MFA, reset) → advanced/authentication-flows.md
│
├─ Test is failing/flaky?
│  ├─ Flaky test investigation → debugging/flaky-tests.md
│  ├─ Element not found → core/locators.md, debugging/debugging.md
│  ├─ Timeout issues → core/assertions-waiting.md, debugging/debugging.md
│  ├─ Race conditions → debugging/flaky-tests.md, debugging/debugging.md
│  ├─ Flaky only with multiple workers → debugging/flaky-tests.md, infrastructure-ci-cd/performance.md
│  ├─ State leak / isolation → debugging/flaky-tests.md, core/fixtures-hooks.md
│  ├─ Console/JS errors → debugging/console-errors.md, debugging/debugging.md
│  └─ General debugging → debugging/debugging.md
│
├─ Testing error scenarios?
│  ├─ Network failures → debugging/error-testing.md
│  ├─ Offline (unexpected) → debugging/error-testing.md
│  ├─ Offline-first/PWA → browser-apis/service-workers.md
│  ├─ Error boundaries → debugging/error-testing.md
│  └─ Form validation → testing-patterns/forms-validation.md, debugging/error-testing.md
│
├─ Refactoring existing code?
│  ├─ Implementing POM → core/page-object-model.md
│  ├─ Improving selectors → core/locators.md
│  ├─ Extracting fixtures → core/fixtures-hooks.md
│  ├─ Creating data factories → core/test-data.md
│  └─ Configuration setup → core/configuration.md
│
├─ Setting up infrastructure?
│  ├─ CI/CD → infrastructure-ci-cd/ci-cd.md
│  ├─ GitHub Actions → infrastructure-ci-cd/github-actions.md
│  ├─ Docker/containers → infrastructure-ci-cd/docker.md
│  ├─ Reporting/artifacts → infrastructure-ci-cd/reporting.md
│  ├─ Global setup/teardown → core/global-setup.md
│  ├─ Project dependencies → core/projects-dependencies.md
│  ├─ Test performance → infrastructure-ci-cd/performance.md
│  └─ Project config → core/configuration.md, core/projects-dependencies.md
│
├─ Organizing tests?
│  ├─ Skip/fixme/slow tests → core/annotations.md
│  ├─ Test tags (@smoke, @fast) → core/test-tags.md
│  ├─ Filtering tests (--grep) → core/test-tags.md
│  ├─ Test steps → core/annotations.md
│  └─ Conditional execution → core/annotations.md
│
└─ Running subset of tests?
   ├─ By tag (@smoke, @critical) → core/test-tags.md
   ├─ Exclude slow/flaky tests → core/test-tags.md
   ├─ PR vs nightly tests → core/test-tags.md, infrastructure-ci-cd/ci-cd.md
   └─ Project-specific filtering → core/test-tags.md, core/configuration.md
```

## Test Validation Loop

After writing or modifying tests:

1. **Run tests**: `npx playwright test --reporter=list`
2. **If tests fail**:
   - Review error output and trace (`npx playwright show-trace`)
   - Fix locators, waits, or assertions
   - Re-run tests
3. **Only proceed when all tests pass**
