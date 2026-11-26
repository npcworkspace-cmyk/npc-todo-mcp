# Todo By NPC MCP

A local stdio MCP bridge for [Todo By NPC](https://npctodo.com/), a free team task workspace. An MCP client can read tasks and update explicitly selected statuses or schedules through a scoped **`NPC_TODO_TOKEN`**. This repository contains the bridge and its installation files.

[中文接入指南](https://npctodo.com/agent-guide) · [申请开通工作空间](https://npctodo.com/contact/) · [NPC 官网](https://www.npctech.site/)

## Access

1. [Request a workspace](https://npctodo.com/contact/). The Todo By NPC team reviews workspace requests.
2. In the workbench, open **Account settings → API & Agent access** and create a key with the scopes you need. Choose read-only access for queries.
3. Supply the key as `NPC_TODO_TOKEN` in your MCP client's secret or environment settings. Keep the real value out of shared configuration and source control.

The bridge sends Bearer-authenticated requests to `https://npctodo.com/api/v1/`. Existing workspace, team and token permissions determine which data and actions the key can access. MCP initialization and tool discovery work before a key is configured. Every tool execution requires the key.

## Install

**Desktop extension:** Open the `.mcpb` asset from the [v1.0.0 GitHub release](https://github.com/npcworkspace-cmyk/npc-todo-mcp/releases/tag/v1.0.0) in a compatible desktop client and enter your token when prompted.

**From source:** Use Node.js 22 or newer.

```sh
git clone https://github.com/npcworkspace-cmyk/npc-todo-mcp.git
cd npc-todo-mcp
npm ci --ignore-scripts
```

For an MCP client that uses an `mcpServers` JSON configuration, set the following. Replace the script path with the absolute path on your machine and set the token via your client's secure settings when supported.

```json
{
  "mcpServers": {
    "npc-todo": {
      "command": "node",
      "args": ["/absolute/path/to/npc-todo-mcp/npc-todo-mcp.mjs"],
      "env": {
        "NPC_TODO_TOKEN": "your-workspace-api-key"
      }
    }
  }
}
```

On Windows, write the absolute script path with forward slashes, for example `C:/tools/npc-todo-mcp/npc-todo-mcp.mjs`. For client-specific setup, see the [Agent guide](https://npctodo.com/agent-guide).

## Tools

| Tool | Action |
| --- | --- |
| `npc_todo_identity` | Read the token identity and scopes. |
| `npc_todo_list_workspaces` | List accessible workspaces. |
| `npc_todo_list_teams` | List accessible teams, optionally by workspace. |
| `npc_todo_list_projects` | List accessible projects, optionally by workspace or team. |
| `npc_todo_list_tasks` | Search or filter accessible tasks, with pagination. |
| `npc_todo_get_task` | Read a task and its current version. |
| `npc_todo_update_task` | Update one task's status or schedule. |
| `npc_todo_batch_update_tasks` | Atomically update up to 100 explicitly identified tasks. |

Every update requires the task's current `expected_version`. On a version conflict, read the task again and review the intended change. Reuse a `request_id` only for an exact retry of the original payload. Schedule values use RFC3339 with an explicit offset and zero seconds.

The MIT license covers this MCP bridge. Account access and data are managed through the [Todo By NPC service](https://npctodo.com/).
