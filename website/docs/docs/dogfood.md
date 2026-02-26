---
title: Dogfood
---

# Dogfood

The dogfood skill turns your AI coding agent into a mobile QA tester. It systematically explores your app on iOS simulators or Android emulators, finds bugs, UX issues, and accessibility problems, and produces a structured markdown report with screenshot evidence for every finding.

## Prerequisites

- `agent-device` [installed](/docs/installation)
- A running iOS simulator or Android emulator (`agent-device boot --platform ios`)
- Your app installed on the device
- An AI coding agent with the dogfood skill installed (see [Skills](/docs/skills))

## Quick Start

With the dogfood skill installed, tell your AI agent:

```
dogfood my app com.example.myapp
```

The agent will:

1. Boot a session and open your app
2. Navigate through every screen it can find
3. Test interactions, forms, and edge states
4. Screenshot and document every issue
5. Produce a structured report in `./dogfood-output/report.md`

You can scope the session or provide auth credentials:

```
dogfood com.example.myapp --platform ios
Focus on the onboarding and settings screens
Sign in with user@example.com / password123
```

## Workflow

The dogfood skill follows a 6-phase workflow:

### Phase 1: Initialize

Sets up the output directory structure and copies the report template:

```bash
mkdir -p dogfood-output/screenshots dogfood-output/videos
```

### Phase 2: Launch & Authenticate

Opens the target app in a named session:

```bash
agent-device --session my-app open com.example.myapp --platform ios
agent-device --session my-app snapshot -i
```

If login is required, the agent identifies form fields from the snapshot and fills credentials:

```bash
agent-device --session my-app fill @e1 "user@example.com"
agent-device --session my-app fill @e2 "password123"
agent-device --session my-app press @e3
```

For OTP or email verification codes, the agent will ask you to provide the code.

### Phase 3: Orient

Captures an initial screenshot and snapshot to map the app's navigation structure:

```bash
agent-device --session my-app screenshot dogfood-output/screenshots/initial.png
agent-device --session my-app snapshot -i
```

The agent identifies tabs, menus, and key entry points before diving deeper.

### Phase 4: Explore

The agent works through each area of the app systematically:

- Visits every tab and major section
- Tests interactive elements: buttons, forms, modals, dropdowns
- Checks edge states: empty lists, error conditions, boundary inputs
- Runs end-to-end workflows (create, edit, delete flows)
- Uses `diff snapshot` after transitions to detect UI changes

At each screen:

```bash
agent-device --session my-app snapshot -i
agent-device --session my-app screenshot dogfood-output/screenshots/screen-name.png
```

### Phase 5: Document Issues

Issues are documented as they are found — not batched for later. Each issue gets:

**For interactive/behavioral issues** (bugs that require user action to reproduce):

```bash
# Start video recording
agent-device --session my-app record start dogfood-output/videos/issue-001-repro.mp4

# Step-by-step reproduction with screenshots
agent-device --session my-app screenshot dogfood-output/screenshots/issue-001-step-1.png

# ... perform actions ...

agent-device --session my-app screenshot dogfood-output/screenshots/issue-001-result.png

# Stop recording
agent-device --session my-app record stop
```

**For static/on-load issues** (visual problems visible without interaction):

```bash
agent-device --session my-app screenshot dogfood-output/screenshots/issue-002.png
```

### Phase 6: Wrap Up

The agent reconciles summary counts in the report, closes the session, and presents findings:

```bash
agent-device --session my-app close
```

## Issue Taxonomy

Issues are categorized by **severity** and **category**:

### Severity Levels

| Severity | Definition |
|----------|------------|
| **Critical** | Blocks a core workflow, causes data loss, or crashes/freeze loops the app |
| **High** | Major feature broken or unusable, no practical workaround |
| **Medium** | Feature works with notable friction or partial failure; workaround exists |
| **Low** | Minor cosmetic or polish issue |

### Categories

| Category | Examples |
|----------|---------|
| **Visual** | Broken layout, clipped text, missing icons, dark mode issues |
| **Functional** | Buttons do nothing, flows fail, navigation dead-ends |
| **UX** | Confusing labels, missing loading feedback, excessive steps |
| **Content** | Typos, placeholder text, untranslated strings, inconsistent terminology |
| **Performance** | Slow startup, scroll jank, input lag |
| **Accessibility** | Missing labels, focus issues, low contrast, small touch targets |
| **Permissions** | Broken permission flows, denied permissions not handled |
| **Diagnostics** | Crashes, unhandled errors, suspicious log patterns |

## Report Format

The output report is a structured markdown file with:

- **Header** — date, platform, target app, session name, scope
- **Summary table** — severity counts
- **Individual issues** — each with severity, category, screen location, description, repro steps, and screenshot references

Example issue entry:

```markdown
### ISSUE-003: "Terms of use" link opens wrong page

| Field | Value |
|-------|-------|
| **Severity** | high |
| **Category** | functional |
| **Screen** | Login screen |
| **Repro Video** | N/A |

**Description**
Tapping "Terms of use" opens the company's legal notice page instead of the actual terms and conditions.

**Evidence**
![Wrong destination](screenshots/issue-003.png)
```

## Tips for Best Results

- **Scope the session** — "Focus on onboarding and settings" produces better results than "test everything"
- **Provide auth upfront** — include credentials in your initial prompt to avoid interruptions
- **Quality over quantity** — 5 well-evidenced issues beat 20 vague ones
- **Let the agent finish** — the workflow is designed to be thorough; interrupting mid-explore can leave gaps
- **Review the report** — the agent finds real issues, but use your domain knowledge to prioritize
