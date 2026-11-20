---
name: uptizm
description: "Manage an Uptizm uptime monitoring account: monitors, custom metrics, incidents, status pages, boards, maintenance and on-call, through the Uptizm MCP server or REST API. Use when the user asks about uptime, outages, monitors, custom metrics, incidents, status pages, maintenance or on-call on Uptizm."
---

# Uptizm

Uptizm checks that websites and APIs answer, opens incidents when they do not, notifies the right people and publishes status pages. This skill acts on the user's Uptizm team: it reads monitors, checks, metrics and incidents, and creates or changes monitors, custom metrics, boards, status pages, maintenance windows, on-call schedules and notification channels.

There are two ways in, and they run the same API with the same permissions: the remote MCP server and the REST API. Every MCP tool is one REST endpoint, so `references/tools.md` (next to this file) maps one to the other.

## Choose the way in

Check in this order, use the first that works, and stay on it for the session.

1. **MCP server.** Tools named `AccountGet`, `MonitorList`, `IncidentList` and so on are available. Claude Code shows them as `mcp__uptizm__MonitorList`, or `mcp__plugin_uptizm_uptizm__MonitorList` when they come from the plugin; when tools are deferred, search for `uptizm`. If the server is listed but answers that it needs authentication, ask the user to run `/mcp` and sign in to `uptizm`.
2. **REST API.** `UPTIZM_API_KEY` is set in the environment (`test -n "$UPTIZM_API_KEY"`). Base URL `https://api.uptizm.com/v1`.
3. **Neither.** You cannot sign in for the user, so stop and offer the ways to connect:
   - MCP, for Claude Code: `claude mcp add --transport http uptizm https://mcp.uptizm.com/mcp`, then `/mcp` and sign in to Uptizm in the browser. The consent screen picks the team and the access (read only, read and write, full).
   - API key: the user creates one in the app under **Settings, Team, API keys** and exports it as `UPTIZM_API_KEY` in the shell that starts the agent.
   - No account yet: the human gets a temporary workspace key from the Uptizm website; an agent cannot create one. Its limits are in https://uptizm.com/docs/anonymous-workspaces.md.

Start with `AccountGet` (MCP) or `GET /me` (REST). It returns the team, the plan with its limits, and the abilities this connection holds. Plan from it: the plan caps monitors, check interval, regions and AI use, and a missing ability means a write will be refused.

## Rules

- The team is fixed by the connection or the key; no call takes a team parameter. To work on another team, the user reconnects and picks it on the consent screen (this replaces the earlier connection), or uses that team's key.
- Take ids from list calls (`MonitorList`, `IncidentList`, `StatusPageList`); never guess one. A 404 means the row does not exist or belongs to another team.
- Ask the user before a call that removes something or reaches people, and say who it reaches: every delete; `IncidentCreate` (always alerts the notification channels; `notify: false` only skips the status page subscriber email); `IncidentAcknowledge`, `IncidentResolve` and `IncidentReopen` (their `message` is shown on the status page, and resolve and reopen alert the channels); `SuggestionAccept` (alerts on-call and the channels); `IncidentPostUpdate` (public unless `is_public` is false); `IncidentPostmortem` with `publish`; `MaintenanceCreate` (announced to subscribers); status page, channel, on-call and escalation changes (they decide who gets paged). Reads, and monitor or metric edits the user asked for, need no extra confirmation.
- Never put a guess about the cause into a public text (an incident update, a lifecycle `message`, a postmortem); write what is known and let the user confirm it.
- Spend the team's AI allowance only when the user asks or it plainly helps: `MonitorAnalyze`, `MetricDiscover`, `IncidentAnalysis`, `IncidentDraftUpdate`, `IncidentDraftPostmortem`.
- Readings are history. Uptizm's probes record checks, statuses and incident evidence; nothing edits them and nothing marks a monitor up. To change what happens next, pause or resume the monitor, run a check now, or acknowledge and resolve the incident.
- Monitor reads leave out probe secrets: `request_headers`, `request_body`, credentials in the `url` and the secret of `auth_config`. To update a monitor, send only the fields that change; send `auth_config` only to replace a credential, with the secret in it. `request_headers` and `request_body` replace the whole stored value and you cannot read it, so ask the user for the complete set before you send either.
- Every person has their own board in each team. Board calls act on the board of the person who created the key or approved the connection; to build someone else's board, they connect.
- Managing members, invitations, status page subscribers, logos and API keys happens in the app, not here.

## Errors

MCP tool errors start with the HTTP status (`403: ...`); REST returns the status and a JSON body.

- 401: the key was revoked or expired, or the MCP connection lapsed after a long idle time. Ask the user to sign in again with `/mcp`, or for a new key.
- 403 naming a required ability (`required_ability` in REST, "Required ability: ..." over MCP): the access is too low. Tell the user which ability is missing and how to get it (reconnect with wider access, or a key with that preset). Do not retry.
- 422: fix the fields named in `errors` and send again.
- 429: wait the `Retry-After` seconds. A key gets 120 reads and 30 writes a minute.
- 409: something is already running, such as an AI analysis for the team; wait for it.
- Any other 4xx answers the same way when retried unchanged.

## Calling the REST API

```bash
curl -sS https://api.uptizm.com/v1/monitors \
  -H "Authorization: Bearer $UPTIZM_API_KEY" \
  -H "Accept: application/json"
```

- Pass the key as `$UPTIZM_API_KEY`; never print, log or commit its value.
- Send a body as JSON with `Content-Type: application/json`.
- Send `Idempotency-Key: <new uuid>` on every POST, so a retry after a timeout does not create a second monitor. Reuse it only for a retry of the same request.
- Lists are cursor-paginated: `per_page` up to 200 (incidents 100) and `cursor` from `meta.next_cursor`, which is null on the last page. Notification channels, on-call schedules and archived content follow `links.next` instead.

Before a write, read the endpoint's page for its fields: `https://uptizm.com/llms.txt` lists every endpoint page, and `https://uptizm.com/docs/openapi.json` is the full spec. With MCP, the tool's input schema lists the fields and the same page explains them.

## Workflows

Read `references/workflows.md` when you set up monitoring for a site or API, add custom metrics, triage an incident, report on uptime, build a board, publish a status page or plan maintenance.
