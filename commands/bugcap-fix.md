---
name: bugcap-fix
description: Fix a Bugcap bug report. Pass a bugcap.io/s/<id> link, a report id, or words from its title.
---

# Fix a Bugcap bug report

Follow the `fix-bug-report` skill for the report given after the command.

- If the argument is a `bugcap.io/s/<id>` link or an id, read it with `get_bug_report`.
- If it is free text, search with `list_bug_reports` (`query`) and confirm the match with the user when there is more than one.
- With no argument, list the user's latest reports with `list_bug_reports` (`mine: true`, `limit: 10`) and ask which one to fix.

When the Markdown isn't enough, dig in with `list_console_logs`, `list_network_requests` and `list_steps` (and `get_performance` or `get_environment` when relevant).

Then find the root cause in this workspace, fix it, verify the fix, and summarize what changed so the user can share it with the reporter.
