# Uptizm workflows

Each step names the MCP tool first and the REST endpoint after it. Read the endpoint page (listed in https://uptizm.com/llms.txt) before a write you have not made before.

## Contents

- Monitor a site or API
- Add custom metrics
- Triage an incident
- Report on uptime
- Build a board
- Publish a status page
- Plan maintenance
- Who is on call

## Monitor a site or API

With AI (proposes settings and metrics; spends the team's AI setup allowance):

1. `MonitorAnalyze` (`POST /monitors/analyze`) with `url`. It answers `data.run_id`. A 409 means another analysis is running for the team.
2. Poll `MonitorAnalyzeResult` (`GET /monitors/analyze/{run}`) every 2.5 seconds for up to about 150 seconds, until `data.status` is `completed` or `failed` (`data.reason` says why). A 404 means the run expired: start again.
3. Create from `data.result.data`: `name`, `url` (the analysed address, which may be a redirect target rather than what the user typed), `"type": "http"` and every key of `suggestion`. Show the user the proposal first when they did not ask you to decide.
   - REST: `POST /monitors` with the `suggested_metrics` worth keeping as `metrics`.
   - MCP: `MonitorCreate` takes no `metrics`, so add each kept metric with `MetricCreate` after the monitor exists.

Without AI: `MonitorCreate` with the fields on https://uptizm.com/docs/post-monitors.md. The plan caps the monitor count, the fastest `check_interval_sec` and the regions per monitor; `AccountGet` shows the limits.

Then `MonitorCheckNow` (`POST /monitors/{monitor}/test`) and `CheckList` (`GET /monitors/{monitor}/checks`) to show the first result. Guide: https://uptizm.com/docs/create-monitors.md.

## Add custom metrics

A custom metric turns one value in a monitor's response into a tracked series with warn and critical bounds, and on paid plans anomaly detection.

1. Find values: `MetricCandidates` (`GET /monitors/{monitor}/content/candidates`) lists numbers in the latest archived response. `MetricDiscover` asks the AI instead.
2. Pick the few numbers a person would act on: backlog, error rate, latency, free disk, age of the oldest job. Skip constants, ids, timestamps and counters that only grow.
3. `MetricPreview` (`POST /monitors/{monitor}/metrics/preview`) with the draft returns the extracted value and its band, storing nothing. Send `sample_body` to test against JSON that is not deployed yet.
4. `MetricCreate` (`POST /monitors/{monitor}/metrics`), then `MetricList` shows the latest value and band.
5. Tell the user what each number means and why you set its bounds there. When no hard limit is known, leave the bounds out and enable anomaly detection.

Fields, bound rules and anomaly kinds: https://uptizm.com/docs/custom-metrics.md.

## Triage an incident

1. `DashboardIncidents` (`GET /dashboard/active-incidents`), or `IncidentList` with `open: true` (`GET /incidents?open=1`).
2. `IncidentGet` (`GET /incidents/{incident}`) for the timeline and affected monitors.
3. Evidence: `CheckList` and `CheckGet` for the failing checks, `CheckEpisodeList` for problem episodes, `MetricReadings` when `trigger_metric_key` is set.
4. `IncidentAnalysis` (`GET /incidents/{incident}/analysis`) when the user wants the AI's read; it spends AI allowance.
5. With the user's word: `IncidentAcknowledge` moves it to investigating, `IncidentAssign` sets a responder (`MemberList` gives ids), `IncidentPostUpdate` adds a timeline note, `IncidentResolve` closes it.
6. Afterwards: `IncidentDraftPostmortem` (AI) or a written body to `IncidentPostmortem`; `publish` shows it on the status page.

Resolve by hand only when the user says it is over.

## Report on uptime

- Team overview: `DashboardStats` and `DashboardMonitors`.
- One monitor: `MonitorUptime` with `range` `24h`, `7d`, `30d` or `90d` (counts by status and the ratio), `MonitorResponseTimes` for latency, `MonitorAnomalies` for unusual metric changes.
- Weekly summary: `IncidentDigest` (404 until the first digest exists).

Mind the unit: `uptime_ratio` is a fraction (`0.9921` is 99.21%), while fields named `uptime_*` on monitors and the dashboard are already percentages.

## Build a board

A board is up to 20 tiles, each one signal (`status`, `response_time`, `uptime` or `metric`) of one monitor.

1. `BoardGet` (`GET /board`) reads the current tiles.
2. `BoardReplace` (`PUT /board`) replaces the whole list: send every tile to keep, in display order. An empty list clears it.
3. `BoardData` (`GET /board/data`) reads the live values; polling faster than the monitor's `interval_sec` shows nothing new.

Tile fields and the view each source allows: https://uptizm.com/docs/build-a-board.md.

## Publish a status page

1. `StatusPageCreate` (`POST /status-pages`) with `name` and `slug`; the plan caps the count.
2. `StatusPageMonitorAdd` for each monitor to show, `StatusPageMonitorReorder` for the order.
3. `StatusPageUpdate` with `is_public: true` when the user is ready to publish.

## Plan maintenance

`MaintenanceCreate` (`POST /scheduled-maintenances`) with `status_page_id`, `title`, `starts_at`, `ends_at` (ISO 8601) and `monitor_ids`. It is announced to the page's subscribers. With `suppress_alerts` (the default), incidents on those monitors are held back while the window is open. Confirm the times and the time zone with the user first.

## Who is on call

`OnCallNow` (`GET /on-call/current`) answers who is on call now. Schedules, rotations and overrides: `ScheduleGet`, `RotationAdd`, `OverrideAdd`; escalation: `EscalationPolicyGet`. `MemberList` gives user ids.
