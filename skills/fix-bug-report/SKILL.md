---
name: fix-bug-report
description: Fix a bug recorded in Bugcap. Use when the user pastes a bugcap.io/s/<id> link, mentions a Bugcap bug report, or asks to work through their team's reported bugs.
---

# Fix a Bugcap bug report

Bugcap reports are browser recordings: video, clicks, navigation, console errors, failed network requests, the reporter's narration and an AI summary. The `bugcap` MCP server exposes them to the agent.

## Tools

Every tool except `list_bug_reports` takes a report id or its `https://bugcap.io/s/<id>` link as `report`.

- `list_bug_reports`: newest reports in the user's organization. `query` matches the title or page URL, `mine` limits to reports the user recorded, `limit` is 1 to 50.
- `get_bug_report`: the whole report as Markdown, the same text as `https://bugcap.io/s/<id>/report.md`. Long lists are shortened: only console errors and warnings, the first 100 failed and 200 other requests, and a trimmed timeline.
- Section tools return one part of the report in full as JSON. Times (`at`, `offsetMs`) are offsets into the video. The list tools take `limit` (default 100, max 500) and return `total` (matching entries) and `returned`, so you can tell when results were cut off.
  - `list_console_logs`: console entries with stack traces and source locations. `level` (array of `error`, `warn`, `info`, `log`, `debug`) and `search` (text in the message).
  - `list_network_requests`: method, URL, status, duration, sizes and errors. `failedOnly`, `type` (`fetch`, `xhr`, `websocket`, `beacon`, `sse`), `search` (text in the URL), and `includeDetails` for request and response headers and bodies (masked when recorded; at most 50 requests).
  - `list_steps`: clicks, typing, navigation and UI changes, with rage or dead click flags and what each step led to. `includeMinor` adds scroll, resize and visibility steps.
  - `get_performance`: Web Vitals per page (LCP, INP, CLS, TTFB, each rated good, needs-improvement or poor), long tasks with total blocking time, and memory use.
  - `get_environment`: page, recording time, browser, OS, viewport, language, timezone, user agent, app version, app state (cookie names and storage keys), app context and tabs.
- `update_title`, `update_summary`, `set_priority` (`critical`, `high`, `medium`, `low`), `update_tags` (`add`/`remove`, or `set` to replace the list): edit a report. Only the person who recorded the report can edit it.

## Workflow

1. Find the report.
   - With a link or id, call `get_bug_report` with it.
   - Otherwise call `list_bug_reports` with a `query` from the user's words (and `mine: true` if they said "my bug"). If several match, show the titles and ask which one.
2. Read the report before touching code: summary, expected vs actual, steps to reproduce, then the timeline. Console errors and failed network requests usually point to the failing code path; the page URL tells you which route or screen. When the Markdown isn't enough, use the section tools:
   - `list_console_logs` with `level: ["error"]` for stack traces.
   - `list_network_requests` with `failedOnly: true` and `includeDetails: true` for status codes and response bodies.
   - `list_steps` for the exact sequence of clicks and input.
   - `get_performance` when the report is about slowness.
   - `get_environment` for bugs that depend on the browser, viewport, locale or app version.
3. Find the code. Search the workspace for the route, component, error message or API endpoint from the report. Explain the root cause to the user in a sentence or two.
4. Fix it with the smallest change that addresses the root cause, and verify it (tests, type check, or reproducing the steps when practical).
5. Report back.
   - Give the user a short summary they can share with the reporter: what changed and where (files, PR link), with a link to the report and no customer data, secrets or internal-only details.
   - Suggest `set_priority` or `update_tags` only when the report's current values look wrong. Edits fail unless the user recorded the report; say so instead of retrying.

## Errors

- Sign-in prompt or 401: the MCP server needs the user to sign in to Bugcap. Ask them to open Cursor's MCP settings and authenticate the `bugcap` server.
- "Bug report not found": the report is in a different organization than the one picked at sign-in, or it isn't ready yet. To switch organization, disconnect the agent under Settings, Connected agents on bugcap.io and sign in again.
- "Only the person who recorded this report can edit it": leave the report as is and tell the user.
