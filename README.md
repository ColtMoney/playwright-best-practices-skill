```

░█▀█░█░░░█▀█░█░█░█░█░█▀▄░▀█▀░█▀▀░█░█░▀█▀░░░█▀▄░█▀▀░█▀▀░▀█▀░░░█▀█░█▀▄░█▀█░█▀▀░▀█▀░▀█▀░█▀▀░█▀▀░█▀▀░
░█▀▀░█░░░█▀█░░█░░█▄█░█▀▄░░█░░█░█░█▀█░░█░░░░█▀▄░█▀▀░▀▀█░░█░░░░█▀▀░█▀▄░█▀█░█░░░░█░░░█░░█░░░█▀▀░▀▀█░
░▀░░░▀▀▀░▀░▀░░▀░░▀░▀░▀░▀░▀▀▀░▀▀▀░▀░▀░░▀░░░░▀▀░░▀▀▀░▀▀▀░░▀░░░░▀░░░▀░▀░▀░▀░▀▀▀░░▀░░▀▀▀░▀▀▀░▀▀▀░▀▀▀░
```
<img src="assets/currents-logo.png" width="24" height="24" align="left" />by [currents.dev](https://currents.dev?utm_source=ai-skill) - The all-in-one Dashboard for Playwright Testing.

# Playwright Best Practices Skill

A skill that gives the AI specialized guidance for writing, debugging, and maintaining **Playwright** tests in **TypeScript**. Use it in any repo where you work with Playwright so the assistant follows best practices for E2E, component, API, visual regression, accessibility, security, i18n, Electron, and browser extension testing.

## Installation

```bash
npx skills add https://github.com/currents-dev/playwright-best-practices-skill
```

The skill is activity-based: the AI is directed to the right reference depending on what you're doing, so you get focused advice without loading everything at once.

## When the Skill Is Used

The skill triggers when the AI infers you need help with things like:

- Writing new E2E, component, API, visual regression, or accessibility tests
- Testing mobile/responsive layouts, touch gestures, or device emulation
- Implementing file uploads/downloads, date/time mocking, or WebSocket testing
- Handling OAuth popups or multi-tab flows
- Testing service workers or PWA features
- Testing Electron desktop apps or browser extensions
- Internationalization (i18n), locales, RTL layouts, or date/number formats
- Testing error states, offline mode, or network failure scenarios
- Security testing (XSS, CSRF, authentication, authorization)
- Performance testing with Web Vitals or Lighthouse
- Reviewing or refactoring Playwright test code
- Fixing flaky tests or debugging failures
- Setting up CI/CD, test coverage, or global setup/teardown
- Configuring projects, dependencies, or parallel runs

You don't have to mention "skill" or "Playwright best practices"; describe your task (e.g. "fix this flaky login test" or "add accessibility tests") and the AI will use the skill when it's relevant.

## What's Inside

**57 reference documents** organized into 8 categories:

### Core (`core/`)

| Topic                | Reference                   | Use for                                          |
| -------------------- | --------------------------- | ------------------------------------------------ |
| Test structure       | `test-suite-structure.md`   | Structure, config, E2E/component/API/visual tests |
| Locators             | `locators.md`               | Selectors, robustness, avoiding brittle locators |
| Assertions & waiting | `assertions-waiting.md`     | Expect APIs, auto-waiting, polling               |
| Page Object Model    | `page-object-model.md`      | POM structure and patterns                       |
| Fixtures & hooks     | `fixtures-hooks.md`         | Setup, teardown, auth, custom fixtures           |
| Test data            | `test-data.md`              | Factories, Faker, data-driven testing            |
| Annotations          | `annotations.md`            | skip, fixme, slow, test steps                    |
| Configuration        | `configuration.md`          | playwright.config.ts options                     |
| Global setup         | `global-setup.md`           | globalSetup/Teardown, DB migrations              |
| Projects             | `projects-dependencies.md`  | Project config, dependencies, filtering          |

### Debugging (`debugging/`)

| Topic          | Reference            | Use for                                    |
| -------------- | -------------------- | ------------------------------------------ |
| Debugging      | `debugging.md`       | Trace viewer, inspector, common issues     |
| Flaky tests    | `flaky-tests.md`     | Detection, diagnosis, fixing, quarantine   |
| Error testing  | `error-testing.md`   | Error boundaries, offline, network failures |
| Console errors | `console-errors.md`  | Capturing and failing on JS errors         |

### Testing Patterns (`testing-patterns/`)

| Topic               | Reference                 | Use for                                        |
| ------------------- | ------------------------- | ---------------------------------------------- |
| API testing         | `api-testing.md`          | REST API testing, request context              |
| File operations     | `file-operations.md`      | Upload, download basics                        |
| File upload/download| `file-upload-download.md` | Progress, cancellation, retry patterns         |
| Forms validation    | `forms-validation.md`     | Form testing, validation states                |
| File drop zone      | `drag-drop.md`            | Drag-and-drop file uploads                     |
| Security testing    | `security-testing.md`     | XSS, CSRF, auth security, authorization        |
| Performance testing | `performance-testing.md`  | Web Vitals, budgets, Lighthouse                |

### Advanced (`advanced/`)

| Topic            | Reference               | Use for                                     |
| ---------------- | ----------------------- | ------------------------------------------- |
| Authentication   | `authentication.md`     | Login flows, session storage, cookies       |
| Auth flows       | `authentication-flows.md` | MFA, password reset, complex auth         |
| Mobile testing   | `mobile-testing.md`     | Device emulation, touch gestures, viewports |
| Clock mocking    | `clock-mocking.md`      | Date/time mocking, timezones, timers        |
| Multi-context    | `multi-context.md`      | Popups, new tabs, OAuth flows               |
| Multi-user       | `multi-user.md`         | Collaboration, RBAC, concurrent actions     |

### Browser APIs (`browser-apis/`)

| Topic           | Reference            | Use for                                     |
| --------------- | -------------------- | ------------------------------------------- |
| WebSockets      | `websockets.md`      | Real-time testing, SSE, reconnection        |
| Service workers | `service-workers.md` | PWA, caching, offline                       |

### Architecture (`architecture/`)

| Topic             | Reference              | Use for                              |
| ----------------- | ---------------------- | ------------------------------------ |
| POM vs fixtures   | `pom-vs-fixtures.md`   | Choosing between patterns            |
| Test architecture | `test-architecture.md` | E2E scenario structure               |

### Frameworks (`frameworks/`)

| Topic | Reference | Use for                   |
| ----- | --------- | ------------------------- |
| Vue   | `vue.md`  | Vue testing patterns |

### Infrastructure & CI/CD (`infrastructure-ci-cd/`)

| Topic            | Reference              | Use for                              |
| ---------------- | ---------------------- | ------------------------------------ |
| CI/CD            | `ci-cd.md`             | Pipelines, general CI setup          |
| GitHub Actions   | `github-actions.md`    | GitHub-specific workflows            |
| Docker           | `docker.md`            | Container setup, Playwright images   |
| Performance      | `performance.md`       | Parallel runs, optimization          |
| Reporting        | `reporting.md`         | Test reporters, artifacts            |
| Test coverage    | `test-coverage.md`     | V8 coverage, reports, thresholds, CI |

The skill's `SKILL.md` maps your current activity to these references so the right content is used in context.

## License

MIT
