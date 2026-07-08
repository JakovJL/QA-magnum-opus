# 20 Testing Metrics

## Table of Contents

- [[#What Testing Metrics Are]]
- [[#Why Metrics Matter]]
- [[#Test Progress Metrics]]
- [[#Defect Metrics]]
- [[#Coverage Metrics]]
- [[#Automation Metrics]]
- [[#Quality And Release Metrics]]
- [[#How To Use Metrics Correctly]]
- [[#Common Mistakes]]
- [[#Interview Questions]]
	- [[#Beginner Questions]]
	- [[#Intermediate Questions]]
	- [[#Advanced Questions]]
	- [[#Code Questions]]

**Related notes:** [[QA manual eng]]

---

## What Testing Metrics Are

**Testing metrics** are numbers that help a team understand testing progress, product quality, test coverage, and release risk.

**Goal:** make testing visible and help the team make decisions based on facts, not only opinions.

**Example:** "80% of planned test cases are executed" is a testing metric. It shows progress, but it does not prove that the product is ready.

> [!danger] Key Rule
> A metric is a signal, not a final answer. Always read metrics together with context, risks, and product knowledge.

---

## Why Metrics Matter

Metrics help answer important questions:

- Are we on track with testing?
- Which areas have many defects?
- Which requirements are covered by tests?
- Is automation giving fast and stable feedback?
- Is the product safe enough to release?

**Advantages:**
- Make progress visible to QA, developers, managers, and product owners
- Help find risky areas early
- Support release decisions
- Help improve the testing process over time

**Disadvantages:**
- Can give false confidence if used alone
- Can be manipulated if used to blame people
- Can hide real quality problems if the wrong metric is chosen

> [!tip] Good Metrics
> A good metric supports a decision. Before collecting a metric, ask: "What will we do differently if this number changes?"

---

## Test Progress Metrics

Test progress metrics show how much planned testing is done.

| Metric | Formula / Meaning | What It Shows |
|---|---|---|
| **Test execution progress** | executed test cases / planned test cases x 100% | How much of the planned suite was run |
| **Pass rate** | passed test cases / executed test cases x 100% | How many executed tests passed |
| **Fail rate** | failed test cases / executed test cases x 100% | How many executed tests failed |
| **Blocked rate** | blocked test cases / planned test cases x 100% | How much testing cannot continue because of blockers |
| **Not run count** | test cases not executed yet | Remaining testing scope |

**Example:** if 80 out of 100 planned tests are executed, test execution progress is 80%.

**When to use:** daily test status, sprint testing, release testing, and test progress reports.

> [!caution] Common Mistake
> A high pass rate does not always mean high quality. The tests may cover only easy scenarios or miss risky areas.

---

## Defect Metrics

Defect metrics show where defects are found, how serious they are, and how quickly they are fixed.

| Metric | Formula / Meaning | What It Shows |
|---|---|---|
| **Defect count** | number of defects found | General defect volume |
| **Defect density** | defects per module, feature, story, or code size | Areas with many defects |
| **Defect severity distribution** | defects grouped by severity | Technical impact of defects |
| **Defect priority distribution** | defects grouped by priority | Business urgency of defects |
| **Defect fix rate** | fixed defects / reported defects x 100% | How quickly the team fixes defects |
| **Defect reopen rate** | reopened defects / closed defects x 100% | Quality of fixes and retesting |
| **Defect leakage** | defects found after release | Bugs that escaped testing |
| **Defect rejection rate** | rejected defects / reported defects x 100% | Quality of defect reports or requirement clarity |

**Example:** if the payment module has 15 defects and the profile module has 2 defects, the payment module is probably a higher-risk area.

**When to use:** risk analysis, defect triage, release readiness, and process improvement.

→ see [[07 Bug Report and Defect Life Cycle]]

---

## Coverage Metrics

Coverage metrics show what part of the product, requirements, or code is covered by tests.

| Metric | Formula / Meaning | What It Shows |
|---|---|---|
| **Requirements coverage** | requirements covered by at least one test / total requirements x 100% | Whether requirements are checked |
| **Test case coverage** | test cases created / required test cases | How complete the designed test suite is |
| **Risk coverage** | high-risk areas covered by tests | Whether the most dangerous areas are tested |
| **Code coverage** | code executed by automated tests | Which code was touched by tests |
| **Regression coverage** | important regression areas covered by tests | How strong the regression suite is |

**Example:** 100% requirements coverage means every requirement has at least one linked test case. It does not mean every possible scenario is tested.

**When to use:** test planning, RTM, regression selection, automation planning, and release decisions.

→ see [[03 Test Artifacts]], [[06 Test Design Techniques]], [[10 Regression Testing]]

> [!warning] Coverage Limit
> 100% coverage does not guarantee that there are no bugs. It only means that the selected coverage target was reached.

---

## Automation Metrics

Automation metrics show whether automated tests are useful, stable, and worth maintaining.

| Metric | Formula / Meaning | What It Shows |
|---|---|---|
| **Automation coverage** | automated test cases / total important test cases x 100% | How much important testing is automated |
| **Automated regression coverage** | automated regression checks / important regression checks x 100% | How much regression can run automatically |
| **Flaky test count** | tests that pass and fail without product changes | Test suite stability |
| **False positive rate** | failures not caused by product defects / all failures x 100% | Noise in the test suite |
| **Execution time** | time needed to run the suite | Speed of feedback |
| **Feedback time in CI** | time from commit to visible result | How quickly the team learns about problems |
| **Maintenance effort** | time spent fixing tests and test data | Cost of keeping automation healthy |

**Example:** if automation has 95% pass rate but 30 flaky tests, the suite is not reliable. The team may start ignoring failures.

**When to use:** AQA process improvement, CI health checks, regression automation planning, and test framework maintenance.

→ see [[16 Test Automation Fundamentals]] and [[20 Automation Framework Architecture]]

---

## Quality And Release Metrics

Quality and release metrics help decide whether the product is ready to release.

| Metric | Formula / Meaning | What It Shows |
|---|---|---|
| **Open critical defects** | unresolved defects with high severity or priority | Release blocking risk |
| **Escaped defects** | defects found by users after release | Quality of pre-release testing |
| **Customer-reported defects** | defects reported by users or support | Real user impact |
| **Known issues count** | accepted unresolved defects | Remaining release risk |
| **Performance metrics** | response time, throughput, error rate, resource usage | Non-functional readiness |
| **Security findings** | open security issues by severity | Security release risk |

**Example:** a release can have 98% pass rate but still be blocked by one critical payment defect.

**When to use:** release readiness meetings, go/no-go decisions, and post-release analysis.

→ see [[17 Performance Testing]] and [[15 Security Testing Basics]]

---

## How To Use Metrics Correctly

Use metrics as a decision tool, not as a scoreboard.

Good practice:

- Define the goal before choosing a metric.
- Use several metrics together.
- Combine numbers with risk analysis and tester judgement.
- Track trends over time, not only one snapshot.
- Explain what the metric means and what it does not mean.
- Use metrics to improve the process, not to blame people.

**Example:** `pass rate`, `requirements coverage`, `open critical defects`, and `defect leakage` together give a better release picture than `pass rate` alone.

> [!tip] Metrics In Context
> If the pass rate goes up while escaped defects also go up, the tests may be weak or focused on the wrong areas.

---

## Common Mistakes

- **Using one metric alone.** Example: judging quality only by pass rate.
- **Confusing progress with quality.** Executing many tests does not mean the product is good.
- **Measuring what is easy, not what matters.** Example: counting test cases without checking risk coverage.
- **Using metrics to blame people.** This makes people hide problems instead of solving them.
- **Ignoring trends.** One bad day may be noise; a repeated pattern is a real signal.
- **Treating 100% coverage as "no bugs".** Coverage shows what was touched, not what was proven correct.

---

## Interview Questions

### Beginner Questions

**1. What are testing metrics?**
Testing metrics are numbers that help a team understand testing progress, product quality, test coverage, and release risk.

**2. Why do we need testing metrics?**
We need them to make testing visible, track progress, find risky areas, support release decisions, and improve the testing process.

**3. What test progress metrics do you know?**
Test execution progress, pass rate, fail rate, blocked rate, and not run count.

**4. What is pass rate?**
Pass rate is passed test cases divided by executed test cases, multiplied by 100%. It shows how many executed tests passed.

**5. What is defect density?**
Defect density shows how many defects are found in a module, feature, story, or code area. It helps find risky parts of the product.

---

### Intermediate Questions

**1. Why is pass rate not enough to judge product quality?**
Pass rate only shows how many executed tests passed. It does not show whether the tests cover risky areas, whether important requirements are tested, or whether critical defects are still open.

**2. What is the difference between requirements coverage and code coverage?**
Requirements coverage shows whether requirements are covered by test cases. Code coverage shows which code was executed by automated tests. Both are useful, but neither guarantees that there are no bugs.

**3. What automation metrics are useful?**
Useful automation metrics include automation coverage, automated regression coverage, flaky test count, false positive rate, execution time, feedback time in CI, and maintenance effort.

**4. How can defect metrics help with risk analysis?**
Defect metrics show where many defects appear, which defects are critical, how often fixes are reopened, and which defects escape to production. This helps the team focus testing on high-risk areas.

**5. How should a team use metrics correctly?**
The team should define the goal first, use several metrics together, read them with context, track trends, and use them to improve the process rather than blame people.

---

### Advanced Questions

**1. What is defect leakage and why is it important?**
Defect leakage is the number of defects found after release. It is important because it shows which bugs escaped pre-release testing and helps improve future test strategy.

**2. Can 100% requirements coverage or 100% code coverage guarantee quality?**
No. 100% requirements coverage means every requirement has at least one test. 100% code coverage means the code was executed by tests. Neither proves that all scenarios, edge cases, and risks were tested correctly.

**3. What can a high defect rejection rate mean?**
It can mean that defect reports are unclear, duplicates are reported, requirements are ambiguous, or the team does not share the same understanding of expected behavior.

**4. Which metrics would you use for a release readiness decision?**
I would use test execution progress, pass/fail rate, requirements coverage, open critical defects, known issues, defect leakage trends, and important non-functional metrics such as performance and security findings.

**5. What does it mean if pass rate is high but escaped defects are also high?**
It may mean that the tests are weak, focused on low-risk areas, missing real user scenarios, or not checking important assertions. The team should review coverage, risks, and test design.

---

### Code Questions

**1. Given the data below, calculate test execution progress and pass rate.**

```text
Planned test cases: 120
Executed test cases: 90
Passed test cases: 72
Failed test cases: 18
```

**Answer:**
- Test execution progress = 90 / 120 x 100% = 75%
- Pass rate = 72 / 90 x 100% = 80%

**2. A release has these metrics. Is it ready?**

```text
Pass rate: 98%
Requirements coverage: 95%
Open critical defects: 1
Defect: payment fails for Visa cards
```

**Answer:** Probably not ready. The pass rate is high, but one open critical payment defect can block the release. Metrics must be read with business risk.

**3. An automation suite has these metrics. What is the main problem?**

```text
Pass rate: 96%
Flaky test count: 25
Feedback time in CI: 55 minutes
False positive rate: high
```

**Answer:** The main problem is low trust in automation. Many flaky tests and false positives make failures noisy, and slow CI feedback delays investigation. The team should fix or quarantine flaky tests and reduce feedback time.
