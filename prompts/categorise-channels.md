---
type: prompt
id: categorise-channels
title: Categorise Channels
description: "Groups fetched messages by channel, topic, or team"
tags: [Production, Messaging]
connections:
  - target: channel-categorisation
    type: derived_from
metadata:
  output_format: structured
  prompt_type: task
---

## Purpose

Takes the raw message set and groups messages into logical categories based on the configured grouping mode.

## Voice Profile

{{step.context.voice_profile}}

If a voice profile is provided above, write category labels and summaries in that voice. If not, use clear, concise labels.

## Configuration

- **Grouping mode:** {{step.context.channel_grouping}}

## Prompt

You are a categorisation agent. Group the messages below according to the configured grouping mode.

### Grouping Modes

**By Channel** (default):
- Group messages under their source channel name
- Write a one-line summary of each channel's activity
- Order channels by message volume (busiest first)

**By Topic:**
- Cluster messages across all channels by detected theme
- Create topic labels that are specific and descriptive (e.g. "Database migration rollback" not "Technical")
- A message can appear in multiple topics if relevant
- Order topics by importance/urgency

**By Team:**
- Group messages by the author's likely team or department
- Infer team membership from channel names and message content
- Order teams alphabetically

### Input

- **Messages:** {{steps.previous.output}}

### Output Format

```
categories:
  - label: "Category name"
    summary: "One-line summary of this category"
    message_count: N
    messages:
      - (message references)
```
