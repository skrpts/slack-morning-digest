---
type: skill
id: digest-synthesis
title: Digest Synthesis
description: "Combines categorised and triaged messages into a readable morning briefing"
tags: [Production, Messaging]
connections:
  - target: llm-service
    type: runs_on
context_params:
  voice_profile:
    label: "Voice Profile"
    description: "Your writing style for the digest — tone, vocabulary, and sentence patterns"
    required: false
  digest_length:
    label: "Digest Length"
    description: "How detailed the digest should be — Brief, Standard, or Detailed"
    default: "Standard"
    required: false
---

## Capability

Merges the categorised message groups and urgency triage results into a single, readable morning briefing document. Prioritises action items, highlights key updates, and optionally includes background context.

## When to Use

- After the parallel categorisation and triage steps have completed
- As the main synthesis step in a digest pipeline

## What It Does

1. **Action items first** — leads with messages classified as action-required, grouped by urgency
2. **Key updates** — follows with FYI items, summarised by category
3. **Background** (Detailed only) — includes a brief summary of background activity for full context
4. **Cross-references** — notes related threads across channels (e.g. the same topic discussed in #engineering and #product)

## Digest Length Levels

- **Brief** — action items only, one sentence per item. 200–400 words.
- **Standard** — action items + key updates with short summaries. 400–800 words.
- **Detailed** — everything including background context and cross-references. 800–1,500 words.

## Outputs

Formatted morning briefing in markdown. Starts with an action items section, followed by updates, and ends with optional background context.
