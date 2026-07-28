# Appium

## Table of Contents

- [[#What is Appium]]
- [[#How a Test Launch Works]]
- [[#Capabilities]]
- [[#Locators]]
- [[#Gestures and Waits]]
- [[#Common Mistakes]]
- [[#Interview Questions]]
	- [[#Beginner Questions]]
	- [[#Intermediate Questions]]
	- [[#Advanced Questions]]
	- [[#Code Questions]]

**Related notes:** [[AQA Java eng]]

---

## What is Appium

**Appium** is a mobile automation tool for Android and iOS. Its API is similar to Selenium WebDriver, so approaches to waits, Page Object, and assertions are largely the same.

**Goal:** control a real device or emulator and verify user scenarios of a mobile application.

Appium supports native, hybrid, and mobile web applications.

---

## How a Test Launch Works

```text
Java Test → Appium Server → platform driver → device or emulator → application
```

Android typically uses the UiAutomator2 driver, iOS uses XCUITest. Before launching, an installed Appium Server, platform tools, and an available device or emulator are required.

---

## Capabilities

Capabilities are session settings: which platform, device, and application should be used.

```java
UiAutomator2Options options = new UiAutomator2Options()
    .setDeviceName("Android Emulator")
    .setPlatformName("Android")
    .setApp("C:/apps/demo.apk");

AndroidDriver driver = new AndroidDriver(appiumUrl, options);
```

In a real project, the app path, server address, and device name should be read from configuration rather than hardcoded.

---

## Locators

Preferred locator order:

1. `accessibility id` — stable and available for users with assistive technologies;
2. `id` — if stable;
3. Android UiSelector or iOS predicate — when standard locators are not available;
4. XPath — only as a last resort.

```java
driver.findElement(AppiumBy.accessibilityId("login_button")).click();
```

> [!caution] Do Not Rely on Text and Coordinates
> Text changes with localization, and coordinates vary across screen sizes. Such tests often fail without a real defect.

---

## Gestures and Waits

Mobile interfaces often load slower than web interfaces. Use explicit waits for the expected element state.

For scrolling and swipes, use W3C Actions or special Appium commands. After a gesture, verify the visible result: element appeared, screen changed, or value updated.

Do not use `Thread.sleep()`: it makes tests slow and does not solve unreliable synchronization.

---

## Common Mistakes

| Mistake | Better Approach |
|---|---|
| Testing only on an emulator | Regularly run important scenarios on real devices |
| Using XPath for all elements | Request stable `accessibility id` attributes first |
| Not resetting app data | Prepare state before each test |
| Ignoring permissions and system dialogs | Handle them explicitly in the scenario |

---

## Interview Questions

### Beginner Questions

**1. What is Appium?**

Appium is a mobile automation tool for Android and iOS. Its API is similar to Selenium WebDriver.

**2. What types of applications does Appium support?**

Native, hybrid, and mobile web applications.

**3. What are capabilities in Appium?**

Session settings: platform, device, app path, and other launch parameters.

**4. Which locator is preferred in Appium?**

accessibility id — it is stable and available for assistive technologies.

### Intermediate Questions

**1. Why should you avoid using XPath for all elements?**

XPath is slow and fragile. It is better to ask developers to add stable accessibility id attributes.

**2. Why is Thread.sleep() dangerous in mobile tests?**

It makes tests slow and does not solve unreliable synchronization. Explicit waits should be used instead.

**3. What drivers are used for Android and iOS?**

Android uses UiAutomator2, iOS uses XCUITest.

### Advanced Questions

**1. Why is testing only on an emulator insufficient?**

An emulator does not reflect real device characteristics: different resolutions, performance, sensors, OS versions, and system dialog interactions.

**2. How do you verify gestures in Appium?**

Use W3C Actions or special Appium commands. After a gesture, verify the visible result: element appeared, screen changed, or value updated.

**3. Why should you avoid relying on text and coordinates in locators?**

Text changes with localization, and coordinates vary across screen sizes. Tests often fail without a real defect.

### Code Questions

**1. What does this code do?**

```java
UiAutomator2Options options = new UiAutomator2Options()
    .setDeviceName("Android Emulator")
    .setPlatformName("Android")
    .setApp("C:/apps/demo.apk");

AndroidDriver driver = new AndroidDriver(appiumUrl, options);
```

**Answer:** Creates an AndroidDriver session with an Android emulator and the demo.apk application.

**2. Which locator is used and why is it preferred?**

```java
driver.findElement(AppiumBy.accessibilityId("login_button")).click();
```

**Answer:** accessibility id is used. It is stable, readable, and available for assistive technologies.
