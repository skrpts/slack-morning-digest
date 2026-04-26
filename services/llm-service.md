---
type: service
id: llm-service
title: LLM Service
description: "Language model service for message categorisation, triage, and digest synthesis"
tags: [Production, Tested]
connections: []
metadata:
  serviceType: llm
  auth_type: api_key
---

## LLM Service

This skrpt uses a language model for analytical and generative tasks. The LLM handles message categorisation, urgency triage, and digest synthesis across each stage of the workflow.

### Usage Pattern

The LLM is invoked at each stage of the pipeline. The parallel agents (categorisation, urgency triage) each run independent analysis passes. The synthesis step combines their outputs into a coherent briefing. The final polish step applies your Voice Profile.

### Configuration

- **Temperature:** 0.3 for categorisation and triage, 0.5 for digest synthesis
- **Max tokens:** 4,000 per analysis agent, 6,000 for synthesis
- **Context window:** Each parallel agent receives the full message set. The synthesis step receives both agent outputs.

### Requirements

- A configured LLM provider in skrptiq settings
- Sufficient token quota for the full pipeline
- No external network access required beyond your AI provider's endpoint
