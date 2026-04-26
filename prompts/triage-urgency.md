---
type: prompt
id: triage-urgency
title: Triage Urgency
description: "Classifies messages by urgency level — action required, FYI, or background"
tags: [Production, Messaging]
connections:
  - target: urgency-triage
    type: derived_from
metadata:
  output_format: structured
  prompt_type: task
---

## Purpose

Scans the message set and classifies each message or thread by urgency level, separating what needs attention from what can wait.

## Configuration

- **Urgency sensitivity:** {{step.context.urgency_sensitivity}}

## Prompt

You are a triage agent. Classify each message or thread in the set below by urgency level.

### Urgency Levels

**Action required** — you need to respond, decide, or do something:
- Direct mentions of you or your role
- Questions explicitly awaiting your answer
- Blockers that affect your work or team
- Deadline references within the next 48 hours
- Escalations or incidents

**FYI** — useful to know, no action needed:
- Status updates from colleagues
- Announcements and decisions made by others
- Progress reports on projects you follow
- Meeting notes and summaries

**Background** — safe to skip:
- Social chat and emoji reactions
- Bot notifications and automated alerts
- Routine messages (daily standups, recurring reminders)
- Messages in channels you monitor but don't actively participate in

### Sensitivity Adjustment

- **Relaxed:** only flag explicit requests and direct mentions as action-required
- **Standard:** also flag questions in your domain and time-sensitive items
- **Aggressive:** flag anything that could potentially need your input

### Input

- **Messages:** {{steps.previous.output}}

### Output Format

```
triage:
  action_required:
    - message_ref: (channel + timestamp)
      reason: "Why this needs action"
  fyi:
    - message_ref: (channel + timestamp)
      reason: "Why this is worth knowing"
  background:
    - count: N
      summary: "Brief note on what was filtered out"
```
