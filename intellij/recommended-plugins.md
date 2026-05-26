# Recommended IntelliJ IDEA Plugins

Equivalent to `.vscode/extensions.json` — install these plugins for the best development experience on this project.

---

## Core Plugins

| Plugin | Purpose | Install |
|--------|---------|---------|
| **GitHub Copilot** | AI pair programmer + Chat | Settings → Plugins → search "GitHub Copilot" |
| **SonarLint** | Real-time local code quality analysis | Settings → Plugins → search "SonarLint" |
| **google-java-format** | Google Java Style Guide auto-formatter | Settings → Plugins → search "google-java-format" |
| **CheckStyle-IDEA** | Checkstyle integration in the IDE | Settings → Plugins → search "CheckStyle-IDEA" |
| **SpotBugs** | SpotBugs static analysis integration | Settings → Plugins → search "SpotBugs" |

---

## Java / Spring Plugins

| Plugin | Purpose |
|--------|---------|
| **Spring Boot** | Spring Boot support, `application.yml` completion (bundled in IntelliJ Ultimate) |
| **Lombok** | Lombok annotation processing support (if project uses Lombok) |
| **MapStruct Support** | MapStruct code navigation and generation hints |
| **JPA Buddy** | JPA entity and repository generation, DB schema management |
| **Database Tools** (bundled) | Database browser and SQL execution (IntelliJ Ultimate) |

---

## Mainframe / COBOL Plugins

| Plugin | Purpose |
|--------|---------|
| **IBM Z Open Editor** | COBOL, JCL, PL/I, HLASM syntax highlighting and validation |
| **Broadcom COBOL Language Support** | COBOL IntelliSense, copybook navigation |
| **Zowe Explorer** | Browse and edit z/OS datasets and USS files |

> **Note:** IBM Z Open Editor and Broadcom COBOL Language Support are primarily VS Code extensions. For IntelliJ, use IBM Z Open Editor if available via the JetBrains plugin marketplace, or use VS Code for mainframe file editing alongside IntelliJ for Java development.

---

## Angular / TypeScript Plugins

| Plugin | Purpose |
|--------|---------|
| **Angular and AngularJS** | Angular template support (bundled in IntelliJ Ultimate / WebStorm) |
| **ESLint** | TypeScript/JavaScript linting (bundled) |
| **Prettier** | Code formatter for TypeScript, HTML, SCSS (bundled) |

---

## Git / Collaboration Plugins

| Plugin | Purpose |
|--------|---------|
| **GitToolBox** | Enhanced git information in the IDE (status bar, branch info) |
| **Git Graph** | Visual git log and branch graph |

---

## Productivity Plugins

| Plugin | Purpose |
|--------|---------|
| **Key Promoter X** | Suggests keyboard shortcuts as you click UI elements |
| **Rainbow Brackets** | Colour-codes matching brackets for readability |
| **String Manipulation** | Utilities for string case conversion, sorting, etc. |
| **Save Actions** | Auto-format and optimize imports on save |

---

## Configuring google-java-format

After installing the `google-java-format` plugin:

1. `Settings` → `google-java-format Settings`
2. Check **Enable google-java-format**
3. Select **Google Style (2-space indent)**
4. To auto-format on save, install `Save Actions` plugin and configure it to run the formatter

---

## Configuring CheckStyle-IDEA

After installing `CheckStyle-IDEA`:

1. `Settings` → `Tools` → `Checkstyle`
2. Click `+` to add a configuration file
3. Select **Use a Checkstyle file** and point to the project's `google_checks.xml`
4. Set the Checkstyle version to match the project's `maven-checkstyle-plugin` version
5. Right-click any Java file → `Check Current File` to run locally
