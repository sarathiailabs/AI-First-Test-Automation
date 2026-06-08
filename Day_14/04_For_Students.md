# Day 14: Fixtures + Hooks + Test Data – Student Revision Notes

This revision sheet provides a quick-revision summary of fixtures, test lifecycles, global credentials authentication sharing, and environment setups for all topics covered in Day 14.

---

## Topic Revision

### 1. Fixtures
* **Definition:** Prepared objects passed into test arguments that manage setup and teardown tasks cleanly. *(Fixtures ready-to-use helpers hain jo setup aur teardown automate karte hain).*
* **Example Syntax:**
  ```typescript
  export const test = base.extend({
    myFixture: async ({ page }, use) => {
      await use(new MyClass(page)); // Yield object
    }
  });
  ```
* **Key Points:**
  * Runs on-demand for specific tests.
  * Encapsulates teardown using code blocks that execute after the `use` keyword.

---

### 2. Hooks
* **Definition:** Checkpoint methods (`beforeAll`, `beforeEach`, `afterEach`, `afterAll`) that run automatically during test lifecycles. *(Test runner checkpoints jo cleanup aur common setups automate karte hain).*
* **Key Points:**
  * `beforeAll` and `afterAll` run once per file.
  * `beforeEach` and `afterEach` run before/after every single test.

---

### 3. Shared Authentication
* **Definition:** Saving login session storage details (cookies/tokens) to a JSON file to skip UI login steps in subsequent tests. *(Login tokens ko JSON file me save karke login screen ko skip karna).*
* **Example Syntax:**
  ```typescript
  // Save State
  await page.context().storageState({ path: 'auth.json' });
  ```
* **Key Points:**
  * Dramatically reduces test execution times in CI/CD pipelines.
  * Auth path configurations are loaded globally in configs.

---

### 4. Test Data Management
* **Definition:** Storing test input values (usernames, passwords, amounts) in external JSON files and importing them dynamically. *(Input data details ko code se alag JSON me save karna).*
* **Example Loop:**
  ```typescript
  import users from './users.json';
  for (const user of users) {
    test(`Verify ${user.name}`, async ({ page }) => { ... });
  }
  ```
* **Key Points:**
  * Enables data-driven test loops (parameterization).
  * Always treat imported JSON arrays as read-only.

---

### 5. Environment Management
* **Definition:** Dynamically switching test hosts (Staging vs Prod) at runtime using environment variables. *(Test targets ko Staging aur Production me swap karna).*
* **Example Variable:**
  `const url = process.env.TEST_ENV === 'staging' ? 'stage-url' : 'prod-url';`

---

### 6. Configuration Files
* **Definition:** Global configuration file (`playwright.config.ts`) that manages browser choices, timeouts, retries, and setup dependencies. *(Global settings control center).*
* **Key Points:**
  * `timeout` configures max test execution length (default 30s).
  * `expect.timeout` configures assert wait threshold (default 5s).

---

## Assignment Summary

During this session, we practice:
1. **Custom LoginPage Fixture:** Create a custom fixture to auto-instantiate page objects and navigate URLs.
2. **Data-Driven Login Verification:** Loop login test templates over a `student-credentials.json` input file.
3. **Environment Config Setup:** Configure `playwright.config.ts` base URLs dynamically using `TEST_ENV` variables.

---

## Quick Revision Sheet

| Concept | Code Pattern | Trigger Scope | Main Purpose |
| --- | --- | --- | --- |
| **Fixture** | `test.extend({ name: ... })` | Parameter demand | Encapsulate setup/teardown POM |
| **Hook** | `beforeEach(async () => {})` | Runs every test | Share standard page setups |
| **Shared Auth** | `storageState: 'path.json'` | Config load | Skip UI logins to save execution time |
| **Test Data** | `import from './data.json'` | Loop execution | Loop tests across data inputs |
| **Env variables**| `process.env.TEST_ENV` | Runtime select | Switch test targets dynamically |
| **Config file** | `playwright.config.ts` | Global suite | Configure parallel runs and timeouts |

---

## Important Takeaways

1. **Fixtures over Hooks:** Prefer fixtures for POM setup as they are only run when requested.
2. **Protect credentials:** Save authorization storage files in project ignores; never commit cookies to repositories.
3. **Read-Only Data:** Treat JSON imports as immutable constants to prevent Test 1 from corrupting inputs for Test 2.
