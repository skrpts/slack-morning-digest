---
type: skill
id: urgency-triage
title: Urgency Triage
description: "Classifies messages by urgency — action required, FYI, or background noise"
tags: [Production, Messaging]
connections:
  - target: llm-service
    type: runs_on
context_params:
  urgency_sensitivity:
    label: "Urgency Sensitivity"
    description: "How aggressively to flag items as urgent — Relaxed, Standard, or Aggressive"
    default: "Standard"
    required: false
---

## Capability

Scans the message set and classifies each message (or thread) by urgency level. Separates actionable items from informational updates and background chatter.

## When to Use

- As a parallel analysis step after message fetching
- When you want to know what needs attention versus what can wait

## What It Does

1. **Action required** — messages that need a response, decision, or task completion from you. Includes direct mentions, questions awaiting answers, blockers, and deadline references.
2. **FYI** — useful updates that don't require action but you should be aware of. Includes status updates, announcements, and decisions made by others.
3. **Background** — routine messages, social chat, bot notifications, and automated alerts that don't need your attention.

## Sensitivity Levels

- **Relaxed** — only flags explicit requests and direct mentions as action-required. Good for busy workspaces where most messages are informational.
- **Standard** — flags requests, mentions, questions, and time-sensitive items. The default.
- **Aggressive** — flags anything that could potentially need your input, including discussions in your domain. Good for managers who want full visibility.

## Outputs

Structured triage map: list of messages with their urgency classification (action_required, fyi, background) and a brief reason for each classification.
