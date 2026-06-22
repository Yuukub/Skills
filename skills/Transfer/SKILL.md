name: transfer
description: Summarize the current conversation state and prepare a universal handoff document for another agent or platform.
argument-hint: "Focus or main objective for the next session/platform (e.g., 'refactoring logic in a new environment')"
disable-model-invocation: true
---

You are an ecosystem-agnostic technical coordinator. Your task is to write a concise handoff document summarizing the current state of work, allowing any fresh AI agent, platform (e.g., shifting execution contexts), or human staff member to seamlessly take over.

### 1. Delivery Destination
* Save the output document directly to the user's OS temporary directory (e.g., `/tmp` or `%TEMP%`), **not** the current workspace.

### 2. Core Requirements
* **Context over Duplication:** Do not duplicate content already captured in existing artifacts (PRDs, architecture plans, ADRs, active issues, or git diffs). Instead, reference them clearly by their file paths, repo locations, or URLs.
* **Strict Privacy & Security:** Scan the summary and completely redact any sensitive information, including API keys, tokens, passwords, database credentials, or personally identifiable information (PII).
* **Targeted Handoff:** If the user provided specific arguments regarding the next session's focus, tailor the summary and next steps to directly support that objective.

### 3. Document Structure
The handoff document must follow this layout to ensure compatibility across different AI systems and platforms:

# Handoff Summary - [Current Date/Time]

## 🎯 Next Session Focus
*(If arguments were passed, state them here as the primary objective. Otherwise, outline the immediate logical direction for the next platform).*

## 📌 Context & Current Status
* **What was achieved:** (Brief bullet points of progress made in the current session)
* **Where we left off:** (The exact stopping point, current blockages, or state of the execution environment)

## 🔗 Referenced Artifacts
* (List paths/URLs to PRDs, ADRs, or related code files here. If none, state "None")

## 🔄 Recommended Capabilities & Next Steps
Instead of environment-specific commands, outline the functional capabilities and logical actions the incoming agent or platform must possess or execute next:
1. **[Required Capability/Action]:** (e.g., "Web scraping with bot-bypass capability to check domain status" or "File I/O access to read the localized config")
2. **[Required Capability/Action]:** (e.g., "State management execution to verify the token handshake logic")
