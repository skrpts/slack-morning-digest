---
type: skill
id: channel-categorisation
title: Channel Categorisation
description: "Groups messages by channel, topic, or team — producing a structured category map"
tags: [Production, Messaging]
connections:
  - target: llm-service
    type: runs_on
context_params:
  voice_profile:
    label: "Voice Profile"
    description: "Your writing style for category labels and summaries"
    required: false
  channel_grouping:
    label: "Channel Grouping"
    description: "How to group messages — By Channel, By Topic, or By Team"
    default: "By Channel"
    required: false
---

## Capability

Takes the raw message set and groups messages into logical categories. The grouping mode determines the axis: by source channel, by detected topic (across channels), or by team/department.

## When to Use

- As a parallel analysis step after message fetching
- When you want a structured overview of what was discussed

## What It Does

1. **By Channel** (default) — groups messages under their source channel name with a one-line summary per channel
2. **By Topic** — clusters messages across channels by detected theme (e.g. "deployment issues", "hiring", "product feedback") using semantic similarity
3. **By Team** — groups messages by the author's team or department, inferred from channel membership and user profiles

## Outputs

Structured category map: list of categories, each with a label, summary, and the messages assigned to it.
