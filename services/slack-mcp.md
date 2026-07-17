---
type: service
id: slack-mcp
title: Slack MCP
description: "Slack MCP server for reading channel messages, user profiles, and thread history"
tags: [Production, Messaging]
connections: []
metadata:
  provider: slack
  protocol: mcp
  auth_type: bot_token
  env_var: SLACK_BOT_TOKEN
  required_scopes: [channels:history, channels:read, groups:history, groups:read, users:read]
---

## Service Description

Provides access to Slack workspace messages and user data via the Model Context Protocol (MCP). This service is used to fetch channel history for the morning digest — reading messages, resolving user names, and following thread context.

## Configuration

### Authentication

Requires a Slack Bot User OAuth Token (`xoxb-...`) set as the `SLACK_BOT_TOKEN` environment variable, plus your Slack Team ID set as `SLACK_TEAM_ID`.

**Required OAuth scopes:**
- **channels:history** — read message history from public channels
- **channels:read** — list public channels and their metadata
- **groups:history** — read message history from private channels the bot is in
- **groups:read** — list private channels the bot is a member of
- **users:read** — resolve user IDs to display names

To create a bot token, go to [api.slack.com/apps](https://api.slack.com/apps), create an app, add the scopes above under OAuth & Permissions, and install to your workspace.

### MCP Server Setup

The Slack MCP server must be configured in your MCP settings:

```json
{
  "mcpServers": {
    "slack": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-slack"],
      "env": {
        "SLACK_BOT_TOKEN": "{SLACK_BOT_TOKEN}",
        "SLACK_TEAM_ID": "{SLACK_TEAM_ID}"
      }
    }
  }
}
```

## Capabilities Used

### Reading

- `slack_list_channels` — list public channels in the workspace (supports pagination via `limit` and `cursor`)
- `slack_get_channel_history` — retrieve recent messages from a channel (configurable `limit`, default 10)
- `slack_get_thread_replies` — fetch all replies within a message thread (requires `channel_id` and `thread_ts`)
- `slack_get_users` — list workspace users with basic profile information (supports pagination)
- `slack_get_user_profile` — retrieve detailed profile for a specific user by `user_id`

### Not Used by This Skrpt

- `slack_post_message` — this is a read-only digest; no messages are posted
- `slack_reply_to_thread` — not used
- `slack_add_reaction` — not used

## Rate Limiting

Slack API rate limits vary by method (typically 1–50 requests per minute per method). The digest workflow fetches history from multiple channels sequentially with reasonable limits to stay within bounds. For workspaces with many channels, configure the channel list in your input to limit scope.

## Privacy Considerations

All Slack messages accessed through this service are sent to your configured LLM provider for categorization and summarization. Messages may contain PII (names, email addresses, personal information). The `data_handling: pii` declaration in the manifest makes this explicit during import. Ensure your organization's policies permit sending Slack content to third-party AI services.
