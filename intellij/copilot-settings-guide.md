# GitHub Copilot in IntelliJ IDEA — Setup Guide

This guide explains how to configure GitHub Copilot in IntelliJ IDEA to use the context files from this seed repository.

---

## What Works Automatically

GitHub Copilot in IntelliJ reads these files without any manual configuration:

| File | Behaviour |
|------|-----------|
| `.github/copilot-instructions.md` | Automatically loaded for all Copilot interactions |
| `.github/instructions/*.instructions.md` | Available via `#file:` reference in Copilot Chat |
| `.github/prompts/**/*.prompt.md` | Available via `#file:` reference in Copilot Chat |

---

## What Requires Manual Configuration

Unlike VS Code, IntelliJ IDEA does not use `.vscode/settings.json`. Configure Copilot in IntelliJ via:

**Settings → Tools → GitHub Copilot**

### Enable Copilot Chat

1. `Settings` → `Tools` → `GitHub Copilot`
2. Ensure **Enable GitHub Copilot** is checked
3. Sign in with your GitHub account if not already done

### Configure the Editor

IntelliJ manages formatting via its own Code Style settings, not `.vscode/settings.json`. To match the project's Google Java Style:

1. `Settings` → `Editor` → `Code Style` → `Java`
2. Click the gear icon → **Import Scheme** → **IntelliJ IDEA code style XML**
3. Import the `intellij/google-java-style.xml` file from this directory (if provided)
4. Alternatively, install the **google-java-format** plugin (see Recommended Plugins below)

### `.editorconfig` Support

IntelliJ respects `.editorconfig` natively — no configuration needed. The file at the root of this project will be picked up automatically and applied to all files in the project.

---

## Using Agent Prompts in IntelliJ Copilot Chat

To invoke an agent persona:

1. Open the **Copilot Chat** panel (View → Tool Windows → GitHub Copilot Chat)
2. Type `#file:` and browse to the prompt file:
   ```
   #file:.github/prompts/agents/reviewer.prompt.md
   
   Review this class for correctness and security issues.
   ```
3. Or drag the prompt file from the Project panel into the Copilot Chat input

### Quick Agent Reference (Copy-Paste)

```
#file:.github/prompts/agents/architect.prompt.md
#file:.github/prompts/agents/developer.prompt.md
#file:.github/prompts/agents/reviewer.prompt.md
#file:.github/prompts/agents/tester.prompt.md
#file:.github/prompts/agents/analyst.prompt.md
#file:.github/prompts/agents/coverage-enforcer.prompt.md
#file:.github/prompts/agents/test-quality-enforcer.prompt.md
#file:.github/prompts/agents/modernization-expert.prompt.md
#file:.github/prompts/agents/security-auditor.prompt.md
#file:.github/prompts/agents/performance-reviewer.prompt.md
```

### Quick Task Reference (Copy-Paste)

```
#file:.github/prompts/tasks/generate-unit-tests.prompt.md
#file:.github/prompts/tasks/generate-rest-api.prompt.md
#file:.github/prompts/tasks/code-review.prompt.md
#file:.github/prompts/tasks/explain-code.prompt.md
#file:.github/prompts/tasks/refactor-to-clean-code.prompt.md
#file:.github/prompts/tasks/modernize-cobol-to-java.prompt.md
```

---

## Scoped Instructions in IntelliJ

The `applyTo` glob patterns in `.github/instructions/*.instructions.md` are a VS Code Copilot feature. In IntelliJ, these files are not automatically applied based on the active file type. To use them:

1. Manually reference the relevant instruction file in Copilot Chat when working on a specific file type:
   ```
   #file:.github/instructions/spring-boot.instructions.md
   
   Generate a new REST endpoint for customer search.
   ```
2. Or reference multiple files for full context:
   ```
   #file:.github/copilot-instructions.md
   #file:.github/instructions/spring-boot.instructions.md
   #file:.github/instructions/test.instructions.md
   
   Generate a service class with tests for the Order domain.
   ```

---

## SonarLint in IntelliJ

1. Install `SonarLint` plugin: `Settings` → `Plugins` → search "SonarLint"
2. After installation, SonarLint runs automatically on files you edit
3. To connect to SonarQube/SonarCloud for shared team rules:
   - `Settings` → `Tools` → `SonarLint` → `Connected Mode`
   - Add your SonarQube server URL and token

---

## Recommended JDK Configuration

In IntelliJ, configure project SDK via `File` → `Project Structure` → `SDKs`:
- Add JDK 21 (for Spring Boot modules): point to your JDK 21 installation
- Add JDK 17 (for Spring Boot legacy): point to your JDK 17 installation
- Add JDK 11 or 8 (for legacy Spring MVC modules): point to the appropriate JDK

Set the project SDK per module: `File` → `Project Structure` → `Modules` → select module → SDK
