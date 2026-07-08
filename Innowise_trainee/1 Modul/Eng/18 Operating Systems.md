# 18 Operating Systems

## Table of Contents

- [[#What Is an Operating System]]
- [[#Operating System Architecture]]
	- [[#Kernel]]
	- [[#User Space]]
	- [[#File System]]
- [[#Windows vs Linux vs macOS]]
- [[#Windows Basics]]
	- [[#Windows Registry]]
	- [[#Installing and Uninstalling Programs in Windows]]
- [[#macOS Basics]]
	- [[#macOS Interface vs Windows]]
	- [[#macOS Terminal]]
- [[#Operating System Security Measures]]
- [[#Interview Questions]]
	- [[#Beginner Questions]]
	- [[#Intermediate Questions]]
	- [[#Advanced Questions]]
	- [[#Code Questions]]

**Related notes:** [[QA manual eng]]

---

## What Is an Operating System

An **operating system (OS)** is the main software layer between hardware and applications. It manages the computer's resources and gives programs a way to work with files, memory, devices, and the user interface.

**Goal:** let applications run safely and predictably on the machine.

**Main functions of an OS:**
- process management
- memory management
- file management
- device management
- user accounts and permissions
- networking

**Examples:** Windows, Linux, macOS, Android, iOS.

> [!danger] Key idea
> Applications do not work directly with hardware. They usually ask the operating system to do it.

---

## Operating System Architecture

### Kernel

The **kernel** is the core part of the OS. It works closely with hardware and controls low-level operations.

**Goal:** manage CPU, memory, devices, and system calls.

**Example:** when an app wants to read a file, it asks the kernel through the OS API.

### User Space

**User space** is where normal applications run. Browsers, editors, test tools, and terminals usually work here.

**Goal:** isolate applications from critical system parts so one broken app does not directly break the whole OS.

**Example:** Chrome crashes in user space, but the whole OS should usually stay alive.

### File System

A **file system** is the OS structure for storing and organizing files and folders.

**Goal:** keep data in a predictable hierarchy and control access to it.

**Common examples:**
- **Windows:** NTFS
- **Linux:** ext4
- **macOS:** APFS

**QA relevance:**
- file paths differ by OS
- file permissions can block tests
- path case sensitivity may change application behavior

> [!tip] Path difference
> Windows usually uses backslashes like `C:\Users\John\file.txt`. Linux and macOS use forward slashes like `/Users/john/file.txt`.

---

## Windows vs Linux vs macOS

These systems do the same basic job, but differ in defaults, tools, and environment behavior.

| Area | Windows | Linux | macOS |
|---|---|---|---|
| Main audience | General users, enterprise | Servers, developers, power users | Apple ecosystem users |
| File path separator | `\` | `/` | `/` |
| Case sensitivity | Usually not sensitive | Sensitive | Usually not sensitive by default |
| Package management | MSI, EXE, Store, winget | apt, yum, dnf, pacman | App Store, DMG, Homebrew |
| GUI style | Start menu, Taskbar | Depends on distro | Dock, menu bar |
| Terminal | Command Prompt, PowerShell | Shells like Bash | Terminal with zsh/bash |

**QA relevance:**
- the same app may behave differently by OS
- installer flow is OS-specific
- file upload, paths, and permissions may break only on one platform
- hotkeys and UI conventions differ

---

## Windows Basics

Windows is the most common desktop OS in many QA projects, especially in enterprise environments.

**Useful built-in tools:**
- **File Explorer** — browse files and folders
- **Control Panel / Settings** — manage system configuration
- **Task Manager** — inspect running processes, CPU, memory
- **Event Viewer** — inspect system and application logs

### Windows Registry

The **Windows Registry** is a central database of system and application settings.

**Goal:** store configuration used by Windows and many installed programs.

**How it works:** settings are organized into keys and values, similar to folders and items.

**Why QA should care:**
- some apps store configuration in the registry
- broken installers may leave bad keys behind
- settings may remain after uninstall and affect retesting

**Example:** an app remembers install path or startup behavior in registry keys.

### Installing and Uninstalling Programs in Windows

Windows programs are often installed through **MSI**, **EXE**, or the **Microsoft Store**.

**What QA checks:**
- installer starts correctly
- install path can be selected if required
- desktop/start menu shortcuts are created if expected
- uninstall removes the app correctly
- old config or data does not unexpectedly remain

**Common uninstall paths:**
- **Settings -> Apps**
- **Control Panel -> Programs and Features**

---

## macOS Basics

macOS is Apple's desktop operating system. QA engineers may meet it when testing browsers, desktop apps, or mobile-related tooling.

**Useful built-in tools:**
- **Finder** — browse files and folders
- **System Settings** — manage OS configuration
- **Activity Monitor** — inspect CPU, memory, disk, and processes

### macOS Interface vs Windows

The macOS interface differs from Windows in several common ways.

| Feature | Windows | macOS |
|---|---|---|
| Main launcher | Start menu | Launchpad / Spotlight |
| Main app bar | Taskbar | Dock |
| App menu | Inside each window | Global menu bar at top |
| Window buttons | Usually top-right | Top-left |

**Why this matters for QA:**
- user steps differ between platforms
- screenshots and instructions must match the OS
- hotkeys and UI expectations are different

### macOS Terminal

The **Terminal** is the command-line interface in macOS.

**Goal:** run commands, inspect files, and automate work faster than through the GUI.

**Basic commands:**
- `pwd` — show current folder
- `ls` — list files
- `cd` — change folder
- `cat` — print file contents
- `grep` — search text

**QA relevance:**
- useful for reading logs
- useful for checking file paths and permissions
- often used with developer tools and automation

---

## Operating System Security Measures

Operating systems include built-in security controls that protect data and reduce the risk of malware or unauthorized access.

**Windows examples:**
- **UAC (User Account Control)** — asks for confirmation before privileged actions
- **Windows Defender** — built-in antivirus and protection
- **Firewall** — controls network access
- **BitLocker** — disk encryption

**macOS examples:**
- **Gatekeeper** — blocks untrusted apps
- **XProtect** — built-in malware protection
- **FileVault** — disk encryption
- **SIP (System Integrity Protection)** — protects important system files

**Linux examples:**
- file permissions (`rwx`)
- `sudo` for privileged actions
- signed packages from repositories

**QA relevance:**
- security prompts can affect install and launch flows
- firewall rules can block app connections
- permissions can break file access or test setup

> [!tip] Security vs bug
> Sometimes the application is not broken. The OS security layer is intentionally blocking the action.

---

## Interview Questions

### Beginner Questions

**1. What is an operating system?**
An operating system is the main software layer between hardware and applications. It manages resources such as CPU, memory, files, and devices, and lets programs work with them safely.

**2. What are the main functions of an operating system?**
Process management, memory management, file management, device management, networking, and user permissions.

**3. What is the difference between the kernel and user space?**
The kernel is the core part of the OS and works closely with hardware. User space is where normal applications run. This separation improves stability and security.

**4. What is a file system?**
A file system is the OS structure for storing and organizing files and folders, for example NTFS, ext4, or APFS.

### Intermediate Questions

**1. What are the main differences between Windows, Linux, and macOS for QA?**
They differ in paths, case sensitivity, installation methods, terminal tools, permissions, and UI behavior. These differences can cause OS-specific bugs.

**2. What is the Windows Registry?**
The Windows Registry is a central database of system and application settings. QA may inspect it when testing installers, configuration, or cleanup after uninstall.

**3. Why does case sensitivity matter?**
On Linux, `Report.txt` and `report.txt` are different files. On Windows they usually are treated as the same. This can cause bugs that appear only on one OS.

**4. Why should a QA know basic terminal commands?**
They help inspect logs, paths, files, permissions, and environment state faster than using only the GUI.

### Advanced Questions

**1. An application works on Windows but fails on Linux when opening a config file. What might be the cause?**
Possible causes include path separator differences, file permission problems, or case sensitivity in the filename.

**2. Why can uninstall testing be more than just "the app disappeared"?**
Because registry keys, config files, services, or cached data may remain and affect the next install or future testing.

### Code Questions

**1. What does this path tell you?**

```bash
/Users/john/Documents/report.txt
```

It is a Unix-style path, so it matches Linux or macOS rather than the usual Windows path format.
