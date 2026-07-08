# 19 Charles Proxy and Network Sniffing

## Table of Contents

- [[#What Is a Network Sniffer and Proxy]]
- [[#Why QA Engineers Use Proxies]]
- [[#Charles Proxy Overview]]
- [[#Setting Up a Proxy]]
	- [[#Desktop Proxy Setup]]
	- [[#Mobile Proxy Setup]]
- [[#SSL Traffic Inspection]]
- [[#Key Charles Features for QA]]
	- [[#Breakpoints]]
	- [[#Map Local and Map Remote]]
	- [[#Throttling]]
	- [[#Repeat and Compose]]
- [[#Charles for Mobile Testing]]
- [[#Interview Questions]]
	- [[#Beginner Questions]]
	- [[#Intermediate Questions]]
	- [[#Advanced Questions]]
	- [[#Code Questions]]

**Related notes:** [[QA manual eng]]

---

## What Is a Network Sniffer and Proxy

A **network sniffer** is a tool that captures and inspects network traffic. A **proxy** is an intermediate server that traffic passes through before reaching the real destination.

**Goal:** let QA see what requests and responses the application really sends and receives.

**Why this matters:** the UI may show only part of the story, but the real bug may be in headers, payload, status code, SSL, or request timing.

---

## Why QA Engineers Use Proxies

QA engineers use proxies to:
- inspect API requests and responses
- debug mobile applications
- verify headers, cookies, and tokens
- reproduce slow network conditions
- modify requests and responses during testing

**Example:** the UI says "unknown error", but Charles shows the backend returned `403 Forbidden` because the token expired.

---

## Charles Proxy Overview

**Charles Proxy** is a desktop application that acts as a local proxy between the device and the internet.

**How it works:** the app or browser sends traffic to Charles, Charles captures it, and then forwards it to the target server.

**Why it is useful for QA:**
- readable request and response view
- SSL traffic inspection
- request replay and editing
- mobile app traffic debugging

> [!info] Man-in-the-middle idea
> Charles works as a controlled man-in-the-middle proxy. That is why SSL certificate setup is needed for HTTPS inspection.

---

## Setting Up a Proxy

### Desktop Proxy Setup

To inspect traffic from a browser or desktop app, you usually:
1. Start Charles
2. Enable system proxy settings or manually point the app to Charles
3. Confirm the app sends traffic through Charles
4. Inspect the session list, request, and response

**Common detail:** Charles often listens on port `8888`.

### Mobile Proxy Setup

To inspect traffic from a phone:
1. Connect the phone and computer to the same Wi-Fi network
2. Find the computer IP address
3. Open Wi-Fi proxy settings on the phone
4. Enter the computer IP and Charles port
5. Trust the certificate if HTTPS inspection is needed

**QA relevance:** this is common for testing native mobile apps that call backend APIs.

---

## SSL Traffic Inspection

HTTPS traffic is encrypted, so Charles cannot read it unless the device trusts the Charles root certificate.

**Goal:** inspect request and response bodies for HTTPS endpoints.

**Typical steps:**
1. Install the Charles root certificate
2. Trust the certificate in the OS or device settings
3. Enable SSL Proxying for the needed domains

**Why this is needed:** without certificate trust, Charles can see the connection but cannot decrypt the payload.

**QA checks:**
- is the correct host added for SSL proxying?
- does the app reject untrusted certificates?
- is the body visible after trust is configured?

> [!caution] Certificate pinning
> Some mobile apps use certificate pinning. In that case the app may reject Charles even if the root certificate is installed.

---

## Key Charles Features for QA

Charles has several features that are especially useful in testing.

### Breakpoints

**Breakpoints** pause a request or response before it is forwarded.

**Goal:** inspect or change the traffic in the middle of the flow.

**Example:** pause a request, change a parameter, then forward it.

### Map Local and Map Remote

**Map Local** replaces a server response with a local file.

**Map Remote** redirects traffic from one remote endpoint to another.

**Goal:** simulate special backend behavior without changing the real server.

**Example:** return a local JSON file with an error response to test the UI.

### Throttling

**Throttling** slows down the network.

**Goal:** simulate poor internet conditions such as 3G or unstable mobile networks.

**Example:** check whether the app shows a loader and handles timeouts correctly.

### Repeat and Compose

**Repeat** replays an existing request again.

**Compose** lets you build and send a new request manually.

**Goal:** quickly rerun or craft traffic without repeating UI steps.

---

## Charles for Mobile Testing

Charles is very useful in mobile QA because mobile apps often communicate with APIs that are hard to observe in the UI.

**Common uses:**
- inspect login and token refresh requests
- verify headers and payloads
- test app behavior on slow or broken networks
- check whether the app correctly handles `401`, `403`, or `500`

**Important limitation:** some apps block proxy inspection because of certificate pinning.

---

## Interview Questions

### Beginner Questions

**1. What is Charles Proxy?**
Charles Proxy is a local proxy tool that captures and shows network traffic between an application and a server.

**2. Why do QA engineers use Charles?**
To inspect requests and responses, debug mobile apps, view HTTPS traffic, simulate slow networks, and replay requests.

**3. What is the difference between a sniffer and a proxy?**
A sniffer captures traffic. A proxy stands in the middle and traffic passes through it. Charles mainly works as a proxy.

### Intermediate Questions

**1. How do you connect a mobile device to Charles?**
Put the phone and computer on the same Wi-Fi, set the phone's proxy to the computer IP and Charles port, then install and trust the Charles certificate if HTTPS inspection is needed.

**2. Why do you need a certificate for HTTPS inspection?**
Because HTTPS traffic is encrypted. The device must trust the Charles root certificate so Charles can decrypt and display the traffic.

**3. What are breakpoints used for in Charles?**
They pause requests or responses so you can inspect or modify them before forwarding.

**4. What is the purpose of throttling?**
To simulate slow or unstable network conditions and check how the app behaves.

### Advanced Questions

**1. Charles is configured, but the mobile app still shows SSL errors. Why?**
Possible reasons are missing certificate trust, wrong SSL proxying host setup, or certificate pinning in the app.

**2. Why can replaying a request be useful in QA?**
It lets you retest backend behavior quickly without repeating the full UI path each time.

### Code Questions

**1. What does this usually mean in Charles setup?**

```text
Proxy: 192.168.0.15:8888
```

It means the device should send its traffic to the computer at IP `192.168.0.15` on port `8888`, where Charles is listening.
