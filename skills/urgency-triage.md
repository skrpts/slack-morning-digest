---
type: skill
id: urgency-triage
title: Urgency Triage
description: "Triages the fetched messages by urgency, surfacing what needs attention first"
tags: [Production, Messaging]
connections:
  - target: llm-service
    type: runs_on
context_params:
  voice_profile:
    label: "Voice Profile"
    description: "Your writing style for the triage summary"
    required: false
---

## Capability

Sorts the morning's fetched messages by urgency and importance, surfacing what needs a response first so the digest can lead with what matters. Reads the raw message set and produces a triage view: which messages are time-sensitive, which are FYI, and which can wait.

## When to Use

- As a parallel analysis step after message fetching, alongside channel categorisation
- When the digest should highlight what needs attention before the full summary

## What It Does

1. Scans each message for urgency signals — direct questions, deadlines, mentions, blockers, escalations
2. Ranks messages into urgency bands (needs response now / today / FYI)
3. Notes who is waiting on whom, so the digest can call out unanswered asks

## Outputs

An urgency-ordered triage of the messages — the ranked list plus a short "what needs attention first" note, consumed by the digest synthesis step.
