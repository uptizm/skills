# Uptizm skills

Agent skills for [Uptizm](https://uptizm.com), the uptime, incident and status page service. The `uptizm` skill teaches an AI agent to work with your Uptizm team: list monitors and their checks, set up monitoring for a site, add custom metrics, triage incidents, report uptime, build your board, publish status pages and plan maintenance.

The agent uses the [Uptizm MCP server](https://uptizm.com/docs/mcp.md) when it is connected, and the [REST API](https://uptizm.com/docs/quickstart.md) with a team API key otherwise. Both run the same API with the same permissions.

## Install

### Claude Code plugin (skill and MCP server)

```
/plugin marketplace add uptizm/skills
/plugin install uptizm@uptizm
```

The plugin adds the skill and the MCP server. Run `/mcp`, pick `plugin:uptizm:uptizm` and sign in with your Uptizm account in the browser. The consent screen asks which team the connection acts on and what it may do: read only, read and write, or full.

### Any agent, with the skills CLI

```
npx skills add uptizm/skills
```

This installs only the skill. Connect it to your account in one of two ways:

- **MCP**, for Claude Code: `claude mcp add --transport http uptizm https://mcp.uptizm.com/mcp`, then `/mcp` to sign in.
- **API key**, for any agent: create a key in the app under **Settings, Team, API keys** and export it before you start the agent.

  ```
  export UPTIZM_API_KEY=upz_...
  ```

## Try it

- "Which of my monitors are down right now?"
- "Monitor https://example.com/health every minute from Europe and the US."
- "Add a metric for the queue backlog in our health endpoint and warn above 100."
- "What happened in last night's incident? Draft a postmortem."
- "What was our uptime over the last 30 days?"

## How access works

An MCP connection is a team API key named "Claude Code (MCP)", created when you approve it. You can see and revoke it in **Settings, Team, API keys** like any other key. A key acts on one team only, and each call needs the ability its access level grants; a call it does not cover is refused with the missing ability named. The agent asks you before a call that deletes something or notifies people, such as resolving an incident or announcing maintenance.

## Documentation

- [Connect Claude Code (MCP)](https://uptizm.com/docs/mcp.md)
- [API quickstart](https://uptizm.com/docs/quickstart.md)
- [Every endpoint](https://uptizm.com/llms.txt)
