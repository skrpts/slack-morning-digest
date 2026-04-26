---
type: skill
id: message-fetch
title: Message Fetch
description: "Retrieves recent messages from configured Slack channels via MCP"
tags: [Production, Messaging]
connections:
  - target: slack-mcp
    type: runs_on
  - target: llm-service
    type: runs_on
---

## Capability

Fetches recent messages from Slack channels using the Slack MCP server. Collects message text, author, timestamp, thread context, and channel metadata for downstream analysis.

## When to Use

- As the first step in a Slack digest or monitoring pipeline
- When you need a snapshot of recent workspace activity

## What It Does

1. **List channels** — calls `slack_list_channels` to get available channels, then filters to the configured set (or all channels if none specified)
2. **Fetch history** — calls `slack_get_channel_history` for each channel to retrieve recent messages (configurable lookback period)
3. **Resolve threads** — for messages with replies, calls `slack_get_thread_replies` to capture the full conversation context
4. **Resolve users** — calls `slack_get_users` and `slack_get_user_profile` to replace user IDs with display names

## Inputs

| Name | Required | Description | Example |
|------|----------|-------------|---------|
| `{{input.channels}}` | No | Comma-separated channel names to monitor. Default: all public channels. | `general, engineering, product` |
| `{{input.lookback_hours}}` | No | Hours of history to fetch. Default: 12. | `24` |

## Outputs

Structured message set: list of messages grouped by channel, each with author name, timestamp, text, thread replies (if any), and channel metadata.
