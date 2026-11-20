# MCP tool and REST endpoint map

Every MCP tool runs exactly one REST endpoint as the connection's key, so the two columns are the same action. Paths are relative to `https://api.uptizm.com/v1`. The ability is what the key or connection needs; a call without it answers 403 with `required_ability`. Each endpoint's fields are on its page, linked from https://uptizm.com/llms.txt (the page name is the method and path in lowercase, such as `post-status-pages-statuspage-monitors.md`). MCP tools name a path id with an `_id` suffix (`{monitor}` is `monitor_id`).

## Contents

Account, Dashboard, Monitors, Checks and history, Custom metrics, Incidents, Board, Status pages, Maintenance, On-call, Escalation, Notification channels.

## Account

| Tool | Endpoint | Ability |
|---|---|---|
| `AccountGet` | `GET /me` | `self` |
| `MemberList` | `GET /team/members` | `team:read` |

## Dashboard

| Tool | Endpoint | Ability |
|---|---|---|
| `DashboardStats` | `GET /dashboard/stats` | `stats:read` |
| `DashboardIncidents` | `GET /dashboard/active-incidents` | `stats:read` |
| `DashboardMonitors` | `GET /dashboard/monitors-snapshot` | `stats:read` |
| `SuggestionList` | `GET /dashboard/ai-inbox` | `stats:read` |
| `SuggestionAccept` | `POST /ai-suggestions/{suggestion}/accept` | `incidents:write` |
| `SuggestionDismiss` | `POST /ai-suggestions/{suggestion}/dismiss` | `incidents:write` |

## Monitors

| Tool | Endpoint | Ability |
|---|---|---|
| `MonitorList` | `GET /monitors` | `monitors:read` |
| `MonitorGet` | `GET /monitors/{monitor}` | `monitors:read` |
| `MonitorCreate` | `POST /monitors` | `monitors:write` |
| `MonitorUpdate` | `PUT /monitors/{monitor}` | `monitors:write` |
| `MonitorDelete` | `DELETE /monitors/{monitor}` | `monitors:write` |
| `MonitorPause` | `POST /monitors/{monitor}/pause` | `monitors:write` |
| `MonitorResume` | `POST /monitors/{monitor}/resume` | `monitors:write` |
| `MonitorCheckNow` | `POST /monitors/{monitor}/test` | `monitors:write` |
| `MonitorAnalyze` (AI) | `POST /monitors/analyze` | `monitors:write` |
| `MonitorAnalyzeResult` | `GET /monitors/analyze/{run}` | `monitors:read` |

## Checks and history

| Tool | Endpoint | Ability |
|---|---|---|
| `CheckList` | `GET /monitors/{monitor}/checks` | `monitors:read` |
| `CheckGet` | `GET /monitors/{monitor}/checks/{check}` | `monitors:read` |
| `CheckEpisodeList` | `GET /monitors/{monitor}/check-episodes` | `monitors:read` |
| `MonitorUptime` | `GET /monitors/{monitor}/uptime` | `monitors:read` |
| `MonitorResponseTimes` | `GET /monitors/{monitor}/response-times` | `monitors:read` |
| `MonitorAnomalies` | `GET /monitors/{monitor}/anomalies` | `monitors:read` |
| `MonitorStatusHistory` | `GET /monitors/{monitor}/status-history` | `monitors:read` |
| `MonitorMaintenanceSpans` | `GET /monitors/{monitor}/maintenance-spans` | `monitors:read` |
| `ContentVersionList` | `GET /monitors/{monitor}/content` | `monitors:read` |
| `MetricCandidates` | `GET /monitors/{monitor}/content/candidates` | `monitors:read` |

## Custom metrics

| Tool | Endpoint | Ability |
|---|---|---|
| `MetricList` | `GET /monitors/{monitor}/metrics` | `metrics:read` |
| `MetricCreate` | `POST /monitors/{monitor}/metrics` | `metrics:write` |
| `MetricUpdate` | `PUT /monitors/{monitor}/metrics/{metric}` | `metrics:write` |
| `MetricDelete` | `DELETE /monitors/{monitor}/metrics/{metric}` | `metrics:write` |
| `MetricReorder` | `PATCH /monitors/{monitor}/metrics/reorder` | `metrics:write` |
| `MetricPreview` | `POST /monitors/{monitor}/metrics/preview` | `metrics:write` |
| `MetricDiscover` (AI) | `POST /monitors/{monitor}/metrics/discover` | `metrics:write` |
| `MetricSeries` | `GET /monitors/{monitor}/metrics/series` | `metrics:read` |
| `MetricHistory` | `GET /monitors/{monitor}/metrics/{metric}/series` | `metrics:read` |
| `MetricReadings` | `GET /monitors/{monitor}/metrics/{metric}/readings` | `metrics:read` |

## Incidents

| Tool | Endpoint | Ability |
|---|---|---|
| `IncidentList` | `GET /incidents` | `incidents:read` |
| `IncidentGet` | `GET /incidents/{incident}` | `incidents:read` |
| `IncidentCreate` | `POST /incidents` | `incidents:write` |
| `IncidentAcknowledge` | `POST /incidents/{incident}/acknowledge` | `incidents:write` |
| `IncidentResolve` | `POST /incidents/{incident}/resolve` | `incidents:write` |
| `IncidentReopen` | `POST /incidents/{incident}/reopen` | `incidents:write` |
| `IncidentAssign` | `POST /incidents/{incident}/assign` | `incidents:write` |
| `IncidentPostUpdate` | `POST /incidents/{incident}/updates` | `incidents:write` |
| `IncidentPostmortem` | `POST /incidents/{incident}/postmortem` | `incidents:write` |
| `IncidentAnalysis` (AI) | `GET /incidents/{incident}/analysis` | `incidents:read` |
| `IncidentAnalysisFeedback` | `POST /incidents/{incident}/analysis/feedback` | `incidents:write` |
| `IncidentDraftUpdate` (AI) | `POST /incidents/{incident}/draft-update` | `incidents:write` |
| `IncidentDraftPostmortem` (AI) | `POST /incidents/{incident}/draft-postmortem` | `incidents:write` |
| `IncidentDigest` | `GET /incidents/digest` | `incidents:read` |

## Board

| Tool | Endpoint | Ability |
|---|---|---|
| `BoardGet` | `GET /board` | `board:read` |
| `BoardReplace` | `PUT /board` | `board:write` |
| `BoardData` | `GET /board/data` | `board:read` |

## Status pages

| Tool | Endpoint | Ability |
|---|---|---|
| `StatusPageList` | `GET /status-pages` | `status_pages:read` |
| `StatusPageGet` | `GET /status-pages/{statusPage}` | `status_pages:read` |
| `StatusPageCreate` | `POST /status-pages` | `status_pages:write` |
| `StatusPageUpdate` | `PUT /status-pages/{statusPage}` | `status_pages:write` |
| `StatusPageDelete` | `DELETE /status-pages/{statusPage}` | `status_pages:write` |
| `StatusPageMonitorAdd` | `POST /status-pages/{statusPage}/monitors` | `status_pages:write` |
| `StatusPageMonitorRemove` | `DELETE /status-pages/{statusPage}/monitors/{monitor}` | `status_pages:write` |
| `StatusPageMonitorReorder` | `PATCH /status-pages/{statusPage}/monitors/reorder` | `status_pages:write` |

## Maintenance

| Tool | Endpoint | Ability |
|---|---|---|
| `MaintenanceList` | `GET /scheduled-maintenances` | `maintenance:read` |
| `MaintenanceGet` | `GET /scheduled-maintenances/{maintenance}` | `maintenance:read` |
| `MaintenanceCreate` | `POST /scheduled-maintenances` | `maintenance:write` |
| `MaintenanceUpdate` | `PUT /scheduled-maintenances/{maintenance}` | `maintenance:write` |
| `MaintenanceDelete` | `DELETE /scheduled-maintenances/{maintenance}` | `maintenance:write` |

## On-call

| Tool | Endpoint | Ability |
|---|---|---|
| `OnCallNow` | `GET /on-call/current` | `oncall:read` |
| `ScheduleList` | `GET /on-call/schedules` | `oncall:read` |
| `ScheduleGet` | `GET /on-call/schedules/{schedule}` | `oncall:read` |
| `ScheduleCreate` | `POST /on-call/schedules` | `oncall:write` |
| `ScheduleUpdate` | `PUT /on-call/schedules/{schedule}` | `oncall:write` |
| `ScheduleDelete` | `DELETE /on-call/schedules/{schedule}` | `oncall:write` |
| `RotationAdd` | `POST /on-call/schedules/{schedule}/rotations` | `oncall:write` |
| `RotationRemove` | `DELETE /on-call/schedules/{schedule}/rotations/{rotation}` | `oncall:write` |
| `RotationReorder` | `PATCH /on-call/schedules/{schedule}/rotations/reorder` | `oncall:write` |
| `OverrideAdd` | `POST /on-call/schedules/{schedule}/overrides` | `oncall:write` |
| `OverrideRemove` | `DELETE /on-call/schedules/{schedule}/overrides/{override}` | `oncall:write` |

## Escalation

| Tool | Endpoint | Ability |
|---|---|---|
| `EscalationPolicyList` | `GET /escalation-policies` | `oncall:read` |
| `EscalationPolicyGet` | `GET /escalation-policies/{policy}` | `oncall:read` |
| `EscalationPolicyCreate` | `POST /escalation-policies` | `oncall:write` |
| `EscalationPolicyUpdate` | `PUT /escalation-policies/{policy}` | `oncall:write` |
| `EscalationPolicyDelete` | `DELETE /escalation-policies/{policy}` | `oncall:write` |
| `EscalationStepAdd` | `POST /escalation-policies/{policy}/steps` | `oncall:write` |
| `EscalationStepRemove` | `DELETE /escalation-policies/{policy}/steps/{step}` | `oncall:write` |
| `EscalationStepReorder` | `PATCH /escalation-policies/{policy}/steps/reorder` | `oncall:write` |

## Notification channels

| Tool | Endpoint | Ability |
|---|---|---|
| `ChannelList` | `GET /notification-channels` | `channels:read` |
| `ChannelGet` | `GET /notification-channels/{channel}` | `channels:read` |
| `ChannelCreate` | `POST /notification-channels` | `channels:write` |
| `ChannelUpdate` | `PUT /notification-channels/{channel}` | `channels:write` |
| `ChannelDelete` | `DELETE /notification-channels/{channel}` | `channels:write` |

(AI) spends the team's AI allowance.
