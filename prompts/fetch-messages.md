---
type: prompt
id: fetch-messages
title: Fetch Messages
description: "Retrieves and structures Slack messages from configured channels via MCP"
tags: [Production, Messaging]
connections:
  - target: message-fetch
    type: derived_from
metadata:
  output_format: structured
  prompt_type: task
---

## Purpose

Fetches recent Slack messages from configured channels and structures them for downstream analysis.

## Prompt

You are a data retrieval agent. Using the Slack MCP server, fetch recent messages from the specified channels.

### Steps

1. Call `slack_list_channels` to get available channels. Filter to the channels specified in the input (or use all public channels if none specified).
2. For each channel, call `slack_get_channel_history` with the configured lookback period to retrieve recent messages.
3. For messages that have thread replies (indicated by a `reply_count` or `thread_ts`), call `slack_get_thread_replies` to capture the full conversation.
4. Call `slack_get_users` to build a user ID → display name mapping, so messages show real names instead of IDs.

### Input

- **Channels:** {{input.channels}} (comma-separated channel names, or empty for all)
- **Lookback hours:** {{input.lookback_hours}} (default: 12)

### Output Format

Return a structured message set:

```
channels:
  - name: "#general"
    id: C01234567
    messages:
      - author: "Jane Smith"
        timestamp: "2025-03-15T08:30:00Z"
        text: "Message content here"
        thread_replies:
          - author: "Bob Jones"
            text: "Reply content"
        reactions: ["thumbsup", "eyes"]
```

### Error Handling

- If a channel is not accessible (bot not added), skip it and note the error
- If the workspace has no activity in the lookback period, return an empty set with a note
