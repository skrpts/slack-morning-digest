---
type: workflow
id: slack-morning-digest
title: Slack Morning Digest
description: "Fetches Slack messages via MCP, categorises and triages, then produces a personalised morning briefing"
tags: [Production, Messaging]
connections:
  - target: message-fetch
    type: uses
  - target: channel-categorisation
    type: uses
  - target: triage-slack-urgency
    type: uses
  - target: digest-synthesis
    type: uses
  - target: language-polish
    type: uses
  - target: slack-mcp
    type: runs_on
  - target: llm-service
    type: runs_on
metadata:
  estimated_duration: "20-60 seconds"
  avg_tokens: 15000
  trigger: manual
output_step: "language-polish"
composite_steps:
  - "message-fetch"
  - "channel-categorisation"
  - "triage-slack-urgency"
  - "digest-synthesis"
  - "language-polish"
execution:
  - skill: "message-fetch"
    step_type: "generation"
    prompt: "fetch-messages"
    output: { name: "messages", type: "text" }
  - parallel:
    - skill: "channel-categorisation"
      step_type: "synthesis"
      prompt: "categorise-channels"
      output: { name: "categorised_channels", type: "text" }
      context:
        voice_profile: "Neutral professional tone"
        channel_grouping: "Automatic"
    - skill: "triage-slack-urgency"
      step_type: "synthesis"
      prompt: "triage-slack-urgency"
      output: { name: "urgency_triage", type: "text" }
      context:
        urgency_sensitivity: "Standard"
  - skill: "digest-synthesis"
    step_type: "synthesis"
    prompt: "synthesise-digest"
    output: { name: "digest", type: "text" }
    context:
      voice_profile: "Neutral professional tone"
      digest_length: "Standard"
  - skill: "language-polish"
    step_type: "content"
    prompt: "polish-digest"
    output: { name: "polished_digest", type: "text" }
    context:
      voice_profile: "Neutral professional tone"
      grammar_strictness: "Professional"
---

## Overview

This workflow produces a personalised morning briefing from your Slack workspace. It fetches recent messages from configured channels, runs two parallel analysis passes (categorisation and urgency triage), synthesises the results into a readable digest, and applies a final language polish.

The digest adapts to your preferences: choose how messages are grouped (by channel, topic, or team), how aggressively items are flagged as urgent, and how detailed the final briefing should be.

## Pipeline Stages

### Stage 1: Fetch Messages

**Input:** Channel list, lookback period

Using the Slack MCP service, fetch recent messages from configured channels. Resolves thread context and maps user IDs to display names.

**Output:** Structured message set grouped by channel.

### Stage 2: Parallel Analysis (Two Agents)

Two analysis agents run concurrently:

#### 2a. Channel Categorisation

Groups messages by channel (default), by topic (cross-channel themes), or by team. Configurable via the `channel_grouping` persona dial.

#### 2b. Triage Urgency

Classifies each message as action-required, FYI, or background. Configurable via the `urgency_sensitivity` persona dial (Relaxed, Standard, Aggressive).

### Stage 3: Digest Synthesis

Combines the categorised message groups and urgency classifications into a single briefing document. Leads with action items, follows with key updates, and optionally includes background context. Length controlled by the `digest_length` persona dial (Brief, Standard, Detailed).

### Stage 4: Language Polish

Final cleanup: spelling, grammar, clarity, and Voice Profile alignment. Uses British English throughout.

**Output:** Publication-ready morning digest.

## Error Handling

- If the Slack MCP server is unreachable, abort and report the connection error
- If a specific channel is not accessible (bot not added), skip it and note the gap
- If the workspace has no activity in the lookback period, produce a brief "all quiet" message
- If the message volume is very high (500+ messages), the fetch step batches requests to stay within rate limits

## Inputs

| Name | Required | Description | Example |
|------|----------|-------------|---------|
| `{{input.channels}}` | No | Channels to monitor (comma-separated). Default: all public channels. | `general, engineering, product` |
| `{{input.lookback_hours}}` | No | Hours of history to fetch. Default: 12. | `24` |

## Outputs

| Name | Description |
|------|-------------|
| Morning digest | Formatted briefing in markdown with action items, updates, and optional background |

## Setup

Before running this workflow:

1. **Slack MCP server** — install and configure the Slack MCP server in your skrptiq settings.
2. **Bot token** — create a Slack app with the required scopes (`channels:history`, `channels:read`, `groups:history`, `groups:read`, `users:read`) and install it to your workspace.
3. **Add bot to channels** — invite the bot to any private channels you want included in the digest.

## Provider Notes

- Categorisation and triage are analytical tasks — most models handle them well.
- Digest synthesis benefits from a model with strong writing capabilities.
- The pipeline is moderate on token usage — no long-context requirements.

## Example Input

To test this workflow immediately after import:

```
Channels: general
Lookback hours: 24
```
