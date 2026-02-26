---
title: Real-World Example
---

# Real-World Example

This walkthrough shows the dogfood skill testing a fictional React Native (Expo) operations app on an iOS simulator. The app includes authentication, localization (English/German), multiple sections, and server-rendered data.

> Privacy note: this example is intentionally anonymized and does not describe a personal or proprietary app.

## Setup

Boot an iOS simulator and open the app:

```bash
agent-device boot --platform ios
agent-device --session example-app open com.example.operations --platform ios
```

Create the output directory and start the report:

```bash
mkdir -p dogfood-output/screenshots dogfood-output/videos
```

## The Agent's Journey

### Login Screen Exploration

The agent starts at the login screen and explores pre-authentication elements:

```bash
agent-device --session example-app snapshot -i
agent-device --session example-app screenshot dogfood-output/screenshots/initial.png
```

It finds a language selector and legal links inside an in-app browser.

**Issues found at this stage:**

- Placeholder text in language selector
- Legal page opens in German while app language is English
- "Terms of use" link points to the wrong destination

### Authentication Flow

The agent tests invalid credentials first, then logs in:

```bash
agent-device --session example-app fill @e1 "qa-user@example.com"
agent-device --session example-app fill @e2 "wrongpassword"
agent-device --session example-app press @e3
agent-device --session example-app snapshot -i
```

**Issue found:** Auth error message is returned in German while the app is configured to English.

After successful login:

```bash
agent-device --session example-app snapshot -i
agent-device --session example-app screenshot dogfood-output/screenshots/dashboard.png
```

### Dashboard Deep Dive

The agent reads the accessibility tree and identifies labeling problems:

```bash
agent-device --session example-app snapshot -i

# Example output:
# @e5 [text] "nav-notifications"
# @e7 [text] "nav-settings"
# @e17 [text] "dashboard-card-main"
```

**Issue found:** Raw i18n keys are exposed as accessibility labels instead of user-friendly text, which harms screen-reader usability.

It also flags repeated subtitles that add no information.

### Navigating App Sections

The agent systematically explores each area:

```bash
# Section A
agent-device --session example-app click @e12
agent-device --session example-app snapshot -i
agent-device --session example-app screenshot dogfood-output/screenshots/section-a.png

# Notifications
agent-device --session example-app click @e5
agent-device --session example-app snapshot -i

# Settings/Profile
agent-device --session example-app click @e7
agent-device --session example-app snapshot -i
```

**Issues found across sections:**

- Mixed-language labels in English mode
- Untranslated status strings in detail views
- Inconsistent capitalization across cards and section titles
- Ambiguous button text in confirmation modals

### Wrapping Up

After exploring major paths:

```bash
agent-device --session example-app close
```

## Results Summary

This sample dogfood session found **12 issues** across multiple categories:

| Severity | Count |
|----------|-------|
| Critical | 0 |
| High | 2 |
| Medium | 6 |
| Low | 4 |
| **Total** | **12** |

### Key Patterns

The biggest pattern was a localization gap: multiple server-side or fallback strings ignored the selected app language.

High-severity examples:

1. Accessibility labels exposing raw translation keys
2. Legal link navigation mismatch

### Issue Breakdown by Category

| Category | Count | Examples |
|----------|-------|---------|
| Content | 6 | Untranslated strings, placeholder text, capitalization drift |
| Accessibility | 1 | Raw i18n keys exposed to screen readers |
| Functional | 1 | Wrong link destination |
| UX | 2 | Redundant subtitles, unclear action labels |
| Low-priority polish | 2 | Minor copy and consistency issues |

## What This Demonstrates

This example highlights strengths of AI-driven dogfood testing:

- **Accessibility detection** via direct snapshot analysis
- **Systematic coverage** across tabs and key workflows
- **Pattern recognition** of root causes instead of isolated bug notes
- **Structured reporting** ready for issue trackers

## Acting on Findings

After reviewing the report:

1. Prioritize High/Critical issues
2. Group fixes by root cause (e.g., localization pipeline)
3. Convert report entries directly into tracked issues
4. Re-run dogfood after fixes to validate and catch regressions
