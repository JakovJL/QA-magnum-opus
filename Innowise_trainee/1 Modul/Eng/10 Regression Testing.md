# 10 Regression Testing

## Table of Contents

- [[#What Is Regression Testing]]
- [[#Types of Regression Testing]]
	- [[#Full Regression]]
	- [[#Partial Regression]]
	- [[#Progressive Regression]]
	- [[#Corrective Regression]]
- [[#How to Do Regression Testing]]
- [[#How to Select a Regression Test Suite]]
	- [[#Risk-Based Selection]]
	- [[#Priority-Based Selection]]
	- [[#Coverage-Based Selection]]
- [[#Regression vs Retesting]]
- [[#Best Practices]]
- [[#Interview Questions]]
	- [[#Beginner Questions]]
	- [[#Intermediate Questions]]
	- [[#Advanced Questions]]
	- [[#Code Questions]]

**Related notes:** [[QA manual eng]]

---

## What Is Regression Testing

Regression testing verifies that a code change has not broken functionality that worked before. Every time the system is modified — a new feature, a bug fix, or a refactor — regression testing confirms that existing features still behave correctly.

**Goal:** Detect unintended side effects of changes before they reach production.

**Why regressions happen:** Software modules are interconnected. A fix in one area can silently break another without any obvious link.

**When to run:**
- After every code change (bug fix, new feature, refactor)
- Before every release
- After environment changes (OS update, database migration)

---

## Types of Regression Testing

### Full Regression

Re-execute the entire test suite against the modified build.

**Goal:** Maximum coverage — used before major releases or when core functionality changes.

**Advantages:**
- No areas are skipped
- Catches unexpected interactions anywhere in the system

**Disadvantages:**
- Time-consuming and expensive
- Impractical after every small change

---

### Partial Regression

Test only the areas affected by the change and their direct dependencies.

**Goal:** Fast, focused verification of the highest-risk areas.

**Advantages:**
- Much faster than full regression
- Concentrated on likely failure points

**Disadvantages:**
- Requires careful impact analysis — a missed dependency can miss a bug

---

### Progressive Regression

Test only the new functionality plus tests covering areas that interact with it.

**Goal:** Verify a new feature without re-running the full suite.

**When to use:** When a new feature is added and existing tests are assumed stable.

---

### Corrective Regression

Re-run the existing tests without modification.

**Goal:** Quick confirmation that a small isolated bug fix did not break adjacent code.

**When to use:** Minor fixes where requirements have not changed.

---

> [!tip] Which type to choose
> | Scenario | Type |
> |---|---|
> | Major release / core change | Full |
> | Regular sprint or bug fix | Partial |
> | New feature added | Progressive |
> | Minor isolated fix | Corrective |

---

## How to Do Regression Testing

1. **Identify the change.** Understand what was modified and which modules it could affect.
2. **Select the test suite.** Choose tests based on the scope of the change (see [[#How to Select a Regression Test Suite]]).
3. **Prioritize.** Run the most critical tests first — if time runs out, the highest-risk areas are covered.
4. **Execute.** Run tests in a stable environment. Automate where possible.
5. **Analyze results.** Distinguish real failures from environment noise or flaky tests.
6. **Report defects.** Log regressions as bugs and link them to the change that caused them.
7. **Retest after fixes.** Once a regression is fixed, re-run the same tests to confirm resolution.

---

## How to Select a Regression Test Suite

### Risk-Based Selection

Prioritize tests for features that are most critical to the business and most likely to be affected by the change.

**Criteria:**
- Business impact if this feature fails
- Technical proximity to the changed code
- Historical defect density in this area

### Priority-Based Selection

Assign each test case a priority (high / medium / low) and include all high-priority tests in every regression cycle.

**Advantage:** Simple to manage — no per-change analysis needed.
**Disadvantage:** Priorities become stale if not maintained regularly.

### Coverage-Based Selection

Select tests that cover the code paths touched by the change. Requires code coverage data, typically from an automated test suite.

---

## Regression vs Retesting

> [!tip] Regression vs Retesting
> | | Regression Testing | Retesting |
> |---|---|---|
> | **Goal** | Verify unchanged features still work | Verify a specific fixed bug is resolved |
> | **Scope** | Broad — full system or affected areas | Narrow — exact failing scenario |
> | **Triggered by** | Any code change | A bug fix |
> | **Test cases used** | Existing test suite | The original failing test case |
> | **Finds** | Unintended side effects | Confirmed resolution of a known defect |

Both are run together after a bug fix: retest the fixed bug first, then run regression to confirm nothing else broke.

---

## Best Practices

- **Automate the core regression suite.** Manual regression is slow and error-prone. Prioritize automation for stable, high-priority test cases.
- **Keep the suite maintainable.** Remove obsolete tests and update them when requirements change. A stale suite produces false negatives.
- **Run in a stable environment.** Flaky environments create unreliable results and erode team trust in the suite.
- **Link test cases to modules.** Makes impact analysis faster and suite selection more accurate.
- **Track regression bugs separately.** A regression (functionality broken by a change) is higher severity than a newly found bug — it represents lost functionality.
- **Set time budgets per cycle.** Know how much time you have before a release and select the test scope accordingly.

---

## Interview Questions

### Beginner Questions

**1. What is regression testing?**
Regression testing verifies that a recent code change has not broken functionality that worked before. It is run after any modification — a bug fix, a new feature, or a refactor — to catch unintended side effects.

**2. Why do regressions happen?**
Software modules are interconnected. A change in one area can break another that has no obvious link to it, because they share data, state, or dependencies. Regression testing exists to catch these hidden breakages.

**3. When should regression testing be performed?**
After every code change (bug fix, new feature, refactor), before every release, and after environment changes such as an OS update or database migration.

**4. What are the main types of regression testing?**
Full (re-run the whole suite), partial (only affected areas and their dependencies), progressive (new functionality plus interacting areas), and corrective (re-run existing tests without changes for a small fix).

### Intermediate Questions

**1. What is the difference between regression testing and retesting?**
Retesting verifies that one specific fixed bug is now resolved — it runs the exact failing scenario. Regression testing is broader: it checks that the fix did not break anything else. Retesting is narrow and targeted; regression is wide.

**2. How do you select which tests to include in a regression suite?**
By risk (business-critical and frequently-changed areas first), by priority (always run high-priority cases), or by coverage (tests that touch the changed code paths, using coverage data).

**3. Should regression testing be automated? Why?**
Yes, the core suite should be automated. Regression runs often and repeats the same checks, so automation saves time and removes human error. Stable, high-priority cases are the best automation candidates.

**4. What happens if you skip regression testing?**
A change may silently break existing features, and the defect reaches production. Because the broken feature used to work, users and stakeholders see it as a loss of functionality — usually treated as higher severity.

**5. How does a regression suite stay effective over time?**
By maintenance: remove obsolete tests, update tests when requirements change, link tests to modules for faster impact analysis, and keep the environment stable so results are reliable.

### Advanced Questions

**1. Is it always necessary to run the full regression suite?**
No. Full regression is expensive and is reserved for major releases or core changes. For a regular sprint or small fix, a partial regression focused on the affected areas is more practical.

**2. A bug fix passed retesting. Do you still need regression testing?**
Yes. Retesting only proves the specific bug is fixed. The fix itself could have broken other features, so regression is still required to confirm nothing else changed.

**3. A regression test fails, but the feature works fine when you check manually. What is happening?**
Likely a flaky test — caused by an unstable environment, timing/async issues, or stale test data, not a real defect. Flaky tests must be fixed or quarantined, because they erode trust in the suite.

**4. Why is a regression bug often considered higher severity than a newly found bug?**
Because it represents lost functionality — something that previously worked now fails. It is a step backward for users and signals that a change had an unexpected impact.

**5. Can automated regression fully replace manual regression?**
No. Automation handles stable, repetitive checks well, but new, frequently-changing, or visual/usability aspects still need human judgment. A healthy regression strategy combines both.

**6. What is the difference between progressive and corrective regression testing?**
Progressive regression is used when new functionality is added, so both the new feature and connected old areas must be checked. Corrective regression is used when the product has not changed much and existing tests can be re-run without major updates.

**7. How do you choose regression tests after an environment change?**
Focus on areas affected by the environment: integrations, database connections, file paths, browsers, operating systems, configuration, and deployment flow. Even without code changes, environment changes can break existing behavior.

**8. Why should obsolete regression tests be removed or updated?**
Old tests that no longer match requirements create noise and waste time. They can fail for reasons that are no longer relevant, hide real risks, and make the regression suite slower and harder to trust.

### Code Questions

**1.** Your full regression suite takes 8 hours, but the release must go out in 2 hours.

```text
- Full regression runtime: 8 hours
- Available time:           2 hours
- Change made:              bug fix in the checkout module
```

**Question:** What type of regression do you run, how do you select the tests, and what must you do about the parts you cannot cover?

**Answer:** Run a **partial, risk-based regression** — the business-critical flows plus the areas closest to the changed module (checkout and its dependencies). Select tests by risk and priority. Document explicitly what was *not* covered so the residual risk is visible and accepted consciously, rather than silently skipped.

---

**2.** A developer fixed bug #123 and you confirmed the fix works (retesting passed).

```text
- Bug #123:   fixed and retested → PASS
- Same area:  shopping cart and payment flow
```

**Question:** Is retesting enough to close the task? What else must you run, and why?

**Answer:** No. Retesting only proves the specific bug is fixed. You must also run **regression tests** on the cart and payment flow, because the fix itself could have broken adjacent functionality (shared data, state, dependencies). Only regression confirms nothing else changed.

---

**3.** A regression test fails in CI, but when you test the feature manually it works perfectly.

```text
- Automated regression test: FAIL (intermittently)
- Manual check of the same feature: PASS
- Environment: shared CI server, test data reset nightly
```

**Question:** What is the most likely cause, and what should you do about the test?

**Answer:** This is a **flaky test** — likely caused by an unstable environment, timing/async issues, or stale test data rather than a real defect. Do not ignore it: investigate the root cause and fix it, or quarantine the test so it stops eroding trust in the suite. Deleting it silently would hide a real reliability problem.
