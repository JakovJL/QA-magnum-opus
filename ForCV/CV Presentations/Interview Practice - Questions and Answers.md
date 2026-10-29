# Interview Practice: Questions and Answers

## 1. Choosing API and UI tests

### Interview question

How do you decide which scenarios to test through the API and which to cover through the UI?

Another way to ask this is: Why do you use API tests for some scenarios instead of UI tests?

### Strong answer

I choose the test level based on what I need to verify. I usually use API tests for business rules, permissions, and integrations because they are faster and more stable, and they do not depend on UI locators. I also use UI tests for a small number of critical user journeys. For example, in our AI assistant, I check that a support agent can ask a question, review and edit the draft, and open its sources. UI locators can change, so UI tests may need more maintenance. When a UI test fails, a screenshot helps the team understand what happened.

### Key idea

API tests and UI tests have different purposes. API tests are useful for business rules, permissions, integrations, and data. UI tests check important user journeys from the operator's point of view. The answer should explain how the layers complement each other, not suggest that one replaces the other.

## 2. Investigating an access-control bug

### Interview question

Tell me about a challenging bug you found. How did you investigate it, and what was the result?

### Strong answer

One challenging bug I found was that a support agent could see restricted rule text in the assistant's answer because the permission check ran after the model generated the draft. I reproduced the issue, compared the retrieved chunks with the model request, and confirmed that the restricted text had entered the model context. I worked with a developer to move the permission check into retrieval, before the model call. After the fix, I added regression tests across different roles and organizations and confirmed that only authorized users could access the protected information.

### Key idea

Authorization must filter restricted content before it reaches the model; hidden source links do not protect content already placed in context. Regression coverage should verify that access works correctly across different roles and organizations.

## 3. Deciding what to test

### Interview question

How did you decide what to test, and why?

### Strong answer

In my last project, I started by reviewing the requirements and the release schedule. Before testing, our team prepared a test plan, agreed what to test manually and what to automate, and divided the work. I focused first on security risks and important workflows. I chose API tests for core business logic and UI tests for key user actions. After we created them, we added the tests to regression testing and included them in the smoke suite when appropriate.

### Key idea

Explain how requirements, release timing, security, and important workflows shaped your priorities. Then connect the test level to the behavior: API tests for core logic and UI tests for important user actions.

## 4. Working with developers on bugs

### Interview question

How did you work with developers when you found a bug?

### Strong answer

When I find a bug, I reproduce it and collect evidence such as logs, screenshots, and, when needed, a database snapshot. I share the steps and evidence with the developer, and we clarify the expected behavior with a business analyst or manager if we disagree about whether it is a bug. After a fix, I retest the issue and return it for further work if it still occurs. For example, on an access-control issue in an AI knowledge system, I compared API requests, retrieval chunks, and model outputs to show the developer that restricted text was reaching the answer. We moved the permission check into retrieval, and I added regression tests across user roles.

### Key idea

Show a clear bug workflow: reproduce and document the issue, agree on expected behavior, verify the fix, and retest if it still fails. A concrete example demonstrates how you communicate evidence and confirm the result.

## 5. Measuring and reporting test results

### Interview question

How did you measure or report the results of your testing?

### Strong answer

I collected test metrics from our CI pipelines and reviewed them in Allure reports. The reports showed pass and fail results and included screenshots and logs to help investigate failures. We also tracked blocked tests, defect counts, fix and reopen rates, and defects reported by clients after release. For coverage and efficiency, we monitored requirements, test-case, risk, and code coverage, along with CI feedback time, execution time, and automation coverage for important tests. These metrics gave the team visibility into test execution, product risks, and automation progress.

### Key idea

Group metrics instead of listing them all: test outcomes and failure evidence, defect trends and escaped defects, then coverage, speed, and automation. Allure can point to failing tests and useful evidence; investigation is still needed to identify the cause.

## 6. Learning from a missed concurrency bug

### Interview question

Tell me about a time you missed something in testing. What did you learn?

### Strong answer

One time, I missed a race condition in the warehouse reservation flow because I tested only one request at a time. After the issue appeared, I added a concurrent-request test that reproduced it, and we fixed the blocking logic. I learned to include concurrency tests for critical workflows and assess concurrency risks early in test planning.

### Key idea

Explain the gap in the test approach, how the issue was reproduced and fixed, and what changed in your future testing. Start with the concrete example instead of a general introduction.
	
## 7. Keeping test cases and documentation up to date

### Interview question

How do you keep test cases and documentation up to date?

### Strong answer

Because requirements can change quickly, I review my test cases and documentation whenever requirements or acceptance criteria change. I use triggers that notify my agent about requirement changes and flag related documents and framework tests for review. I also stay in contact with the business analyst and developers so I hear about documentation or functionality changes early. Then I update the affected tests and documentation to match the current acceptance criteria.

### Key idea

Describe both the change-detection process and the communication with people who own requirements or implementation. Update only the affected tests and documents so they stay aligned with the current acceptance criteria.

## 8. Investigating intermittent CI failures

### Interview question

How do you investigate an intermittent test failure in CI?

### Strong answer

When a test fails intermittently in CI, I first review the logs, screenshots, and recent run history to see what failed and whether the failure is repeatable. I check that the CI environment and test data are configured consistently. For a UI test, I verify whether the page or locator changed; if an application change caused the failure, I update the test and run it again. If the cause is still unclear, I compare failing and passing runs and investigate further rather than assuming it is a product defect.

### Key idea

Use evidence to distinguish an environment problem, an application change, and a fragile test. Confirm the cause before labeling the failure as flaky or changing a locator, then rerun the test to verify the fix.

## 9. Handling flaky or unstable tests

### Interview question

How do you handle flaky or unstable tests in automation?

### Strong answer

When a test fails, I check the Allure report in CI to see exactly which step failed and why. I look for common causes, such as timing issues or unstable locators, then fix the root cause and rerun the test to confirm it is stable. This helps the team trust the results.

### Key idea

Choose the fix based on the cause: use an explicit wait for asynchronous UI behavior, update a locator when the UI changes, and rerun the test to confirm stability.

## 10. Finding a duplicate-shipment bug

### Interview question

Tell me about a time you found a tricky bug in a web app. How did you find it, and what did you do next?

### Strong answer

In an e-commerce app, I found that the same payment webhook could create two shipments. I reproduced the issue by sending the signed webhook twice and checked the database to confirm the duplicate shipment records. I shared the steps and logs with the developers. They added an idempotency check, and I wrote a regression test to verify that repeated webhooks create only one shipment. This helped avoid extra shipping costs and customer confusion.

### Key idea

Explain how you reproduced the issue, gave developers clear evidence, and verified the fix. End with the business impact.

## 11. Prioritizing tests when time is limited

### Interview question

How do you decide what to test first when time is very limited?

### Strong answer

When time is limited, I start with critical business flows and the highest-risk areas. For an e-commerce app, I would test checkout first because it affects customers and the business. I would also check payment gateway integrations, then cover the most important edge cases. This helps protect the main purchase flow and reduce the risk of duplicate shipments, extra shipping costs, and customer confusion.

### Key idea

Start with critical flows and high-risk integrations, then cover important edge cases. Give a concrete example and explain how this focus helps protect customers and the business.

## 12. Clarifying unclear requirements

### Interview question

How do you handle unclear or incomplete requirements before you start testing?

### Strong answer

When requirements are unclear, I ask the product owner or analyst specific questions and turn the answers into clear acceptance criteria. In my AI knowledge assistant project, I worked with a support expert to create a Golden Set of 60 questions, with the expected facts and allowed sources for each answer. This helped me check that the AI answers matched business expectations, not just that the code worked.

### Key idea

Clarify expected behavior with the product owner, analyst, or domain expert. Use clear acceptance criteria and examples such as a Golden Set to check that tests match business expectations.

## 13. Discussing bug priority with leadership

### Interview question

How do you handle a bug that leadership wants fixed quickly, but you do not think is critical?

### Strong answer

I would explain why I think the bug is not critical and share evidence about its impact. Then I would discuss it with the product owner, the developers, and other team members. I would also consider the developers’ workload and suggest fixing the bug later if more important work needs attention. If leadership still wants an urgent fix, I would respect the decision and agree on the next steps with the team.

For example, I found a minor UI issue that did not affect the core functionality. I shared the evidence, and we agreed to fix it in the next sprint.

### Key idea

Use evidence to explain the risk, suggest a plan, and keep stakeholders informed.

## 14. Improving a testing process

### Interview question

Tell me about a time you improved a testing process or workflow?

### Strong answer

I noticed our API tests only checked that the app was running, not whether the AI answers were correct. I worked with a support expert to define expected answers and sources for a Golden Set, and logged model, prompt, and document versions. This helped us quickly see whether retrieval or generation caused issues, so tests covered both functionality and content quality.

### Key idea

Explain what the old checks missed, what you added, and how it improved feedback or coverage.

## 15. Testing an unreliable third-party service

### Interview question

How do you test a feature that depends on an unreliable third-party service?

### Strong answer

I use WireMock to test normal responses and failures. I also test the real service in a separate environment for timeouts and retries. In an e-commerce project, I sent the same payment webhook twice and found two delivery jobs for one order. The developers added a check to prevent duplicates, and I added tests for normal responses and failures to CI.

### Key idea

Use WireMock for controlled responses, check timeouts and retries separately, and cover normal and failure cases in CI.

---

## Basic Interview Questions

1. Have you built a test framework from scratch? How would you build one? How was the framework built on your project?

   **Answer**

   Yes, I started building an automation framework from scratch on a warehouse project. I was the first tester on the project, and after a few releases, my manager asked me to start test automation.

   I began with the basic configuration and used TestNG to run the tests. For UI tests, I created Page Objects, so the page elements and actions were separate from the test logic. I also prepared the API part for future tests and added a reporting tool. I started with individual tests, and a colleague reviewed my code.

   If I built a framework again, I would start with one or two critical flows and a small core: configuration, test execution, and the components needed for those flows. Then I would add test data, reporting, and a CI pipeline to run the tests automatically. I would expand the framework gradually as the project needed it.

2. What framework architecture would you choose, and why? What design patterns would you use?

   **Answer**

   I would choose a modular, layered architecture. I think it makes the framework easier to maintain, extend, and understand. Each layer has a clear responsibility, so a change in one part should affect as little of the framework as possible.

   For the main tools, I would use Java, JUnit 5 to run the tests, Selenium for UI testing, and REST Assured for API testing. I would choose them based on the project’s needs and the team’s experience.

   I would keep the test scenarios separate from the code that interacts with the application. The tests would describe the steps and check the results. For the UI, I would use the **Page Object pattern**: each page object would contain the locators and actions for a page. If the UI changes, I can update the page object instead of changing the same locator in many tests.

   For the API, I would create separate client classes that use REST Assured to send requests. The tests would call these clients and check the responses. This makes common requests reusable and keeps the tests easier to read.

   I would use a **Factory** to create WebDriver instances for different browsers. Each test would get its own driver, and the driver would be closed after the test. This helps keep tests independent and supports parallel execution.

   For test data, I would use the **Builder pattern** when I need to create complex objects, such as users or request payloads. This makes the test data easier to create and change. I would also keep environment settings in configuration files, so I can run the same tests against different environments.

   Finally, I would add Allure reporting to help the team investigate failures, and I would integrate the tests into CI. I would start with the most important scenarios and expand the framework gradually, based on the project’s needs.

3. How many tests did your project have?

   **Answer**

   In my current project, we have around 240 automated tests. Most of them are API tests: almost 200 API tests and around 40 UI tests.

   The API tests cover access permissions, document versions, AI-generated answers, and integration failures. We group tests by area (access control, AI answer and source evaluation, and integration tests). We also organize them into smoke and regression suites.

   We run the tests in CI. The smoke pipeline takes about 15 minutes, and the full regression pipeline takes about an hour and a half. These are the total pipeline times, including preparation and other steps.

4. How long did the tests take to run in CI? How did you run them?

   **Answer**

   In my current role, we run automated tests in GitLab CI. The pipeline starts when someone creates a merge request or merges changes into the main branch. We also run checks when we deploy to staging or production. We run a smoke suite after changes for quick feedback. We run the full regression every night. The smoke pipeline takes about 15 minutes, and the full regression pipeline takes about an hour and a half. These are the total pipeline times, including preparation and other steps.

5. Where were test cases stored? Who wrote them: manual testers or your team?

   **Answer**

   We stored requirements and test cases in Confluence, and we tracked tasks in Jira. We divided testing work between manual and automation testers. Sometimes we automated manual regression tests when it made sense, to free manual testers to focus on new features.

   Writing test cases was mainly the manual testers’ job. Sometimes we reviewed and updated them if we found mistakes or needed to clarify how to automate them.

6. Which levels of the test pyramid did you write tests for?

   **Answer**

   In my current project, developers write the unit tests. My main focus is API testing: I check contracts, access permissions, document versions, and business results. I also test integrations and use UI tests for the most important user journeys.

   The main automated suite has around 200 API tests and 40 UI tests. We focus more on API tests because they are usually faster and more stable, while UI tests check that key flows work from the user’s point of view.

   For the AI assistant, we also separate functional checks from answer-quality evaluation. Together with a support expert, we define questions, expected facts, and allowed sources for a Golden Set. We use it to assess the answers and their sources.

7. How would you explain the need for automation? What value does QA bring to a project?

   **Answer**

   I use automation to get faster and more repeatable feedback about the product. Once an automated test is in place, I can run it regularly without repeating the same steps manually. This is especially useful for regression testing, because I can check important functionality after every change.

   I start with critical business flows and scenarios that we need to check often. For example, I can automate API checks for business rules and permissions, as well as UI tests for the most important user journeys. I run these checks in CI, so the team can find possible regressions before a change reaches users.

   Automation also reduces repetitive work for manual testers. This gives them more time to explore new features and investigate complex cases. I don’t try to automate everything: I consider the value of a test, how often we will run it, and the effort needed to create and maintain it.

   QA brings value beyond finding bugs. I review requirements, ask questions, and discuss unclear cases with the team before development starts. This helps us prevent some defects early. I also share test results and remaining risks, so the team can make an informed release decision and reduce the chance that users will find serious problems after release.

8. How would you choose tools for a test framework?

   **Answer**

   I would start with the project’s needs. I would look at what we need to test, the technology stack, the team’s experience, and how the tests will run in CI. I would also consider how easy the tools are to maintain and whether they have good documentation and support.

   In my current project, the tools were already chosen when I joined, so I didn’t select them myself. But I work with them and can explain why they fit our needs.

   We use **Java** because it is the language of our automation framework. We use **JUnit 5** to organize and run the tests. It supports setup and cleanup, parameterized tests, and tags for selecting test groups. TestNG can solve similar problems, and I used it on the warehouse project. I wouldn’t say JUnit is always better; it was a suitable choice for our current Java stack.

   For API testing, we use **REST Assured** to send requests and check responses. For UI testing, we use **Selenium** to check important user journeys in a real browser. Selenium supports different browsers and fits our Java framework.

   Playwright is another option for UI testing. For example, it can automatically wait for elements to become ready before actions. If I considered switching from Selenium, I would compare that benefit with the effort of moving our existing tests and helping the team maintain the new tool.

   We also use **WireMock** to simulate responses and failures from external services, and **Allure** to review test results and investigate failures.

   Finally, I would discuss the choice with the team. A tool needs to solve the project’s problems, but the team also needs to understand and maintain it.

9. What was the overall team and QA team composition? How did you communicate?
10. How would you onboard onto a project?
11. Did you work alone or in a team on previous projects? Was the team international?
12. Why did you choose QA as a career?
13. How many tests did you write?
14. How did you integrate testing into CI/CD? What was the test execution flow?
15. What types of tests did you write? What was their percentage breakdown?
16. How would you test a system that performs complex financial calculations?
17. How do you know when a system has been tested enough?
18. If time is limited, what would you test first?
19. What tests do you consider useless?
20. How do you distinguish a good automated test from a bad one?
21. What is the AAA pattern in automated testing?
22. How do you know when a task is ready for production?
23. What does product quality mean to you?
24. What would you do if an analyst and a developer told you opposite things?
25. How do you decide which checks to automate and which to perform manually?
26. How would you choose smoke tests for a project?
27. What would you do if the requirements were contradictory? How would you test a feature without documentation? How would you know that the requirements were insufficient?
28. How would you respond if a critical bug was found after release?
29. How would you organize the review of tests?
30. Which quality metrics are useful, and which are useless?
31. Do you use AI for personal purposes? Have you introduced it into your work process?
32. Tell me about a time you had to work with someone difficult. Were there any conflicts, and how did you handle them? How would you resolve disagreements or technical disputes within the team?
33. How actively did you participate in the release process?
