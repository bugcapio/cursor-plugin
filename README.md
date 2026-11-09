# Bugcap for Cursor

Read [Bugcap](https://bugcap.io) bug reports in Cursor and fix them without leaving the editor.

A Bugcap report is a browser recording: video, clicks and navigation, console errors, failed network requests, the reporter's narration and an AI summary with steps to reproduce. This plugin connects Cursor to your organization's reports through the Bugcap MCP server, so the agent can read a report, find the code behind it and update the report when it's fixed.

## Included

- `mcp.json`: the `bugcap` MCP server (`https://bugcap.io/api/mcp`)
- `skills/fix-bug-report/`: how the agent goes from a report to a fix
- `commands/bugcap-fix.md`: `/bugcap-fix <link, id or search text>`
- `rules/bugcap-reports.mdc`: handling `bugcap.io/s/<id>` links and report contents

## Install

Install **Bugcap** from the Cursor Marketplace. The first time the agent uses a Bugcap tool, Cursor asks you to sign in to Bugcap and pick an organization. The agent only sees that organization's reports.

Without the plugin, add the server to `.cursor/mcp.json` yourself:

```json
{
  "mcpServers": {
    "bugcap": { "url": "https://bugcap.io/api/mcp" }
  }
}
```

You can also use the Cursor and Claude Code buttons on your bugcap.io dashboard to add the server.

To switch organization or revoke access, disconnect the agent under **Settings > Connected agents** on bugcap.io.

## Use

Paste a report link and ask the agent to fix it, or run the command:

```text
/bugcap-fix https://bugcap.io/s/<id>
/bugcap-fix checkout button
```

## Tools

| Tool                                                            | What it does                                                                                           |
| --------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------ |
| `list_bug_reports`                                              | Newest reports, filtered by title or page URL, or only yours                                           |
| `get_bug_report`                                                | The full report as Markdown (long lists shortened)                                                     |
| `list_console_logs`                                             | Console entries with stack traces, filtered by level or message text                                   |
| `list_network_requests`                                         | Requests with status, timing and sizes; optionally only failed ones, or with masked headers and bodies |
| `list_steps`                                                    | Clicks, typing and navigation, with rage or dead click flags and what each step led to                 |
| `get_performance`                                               | Web Vitals per page with ratings, long tasks and memory use                                            |
| `get_environment`                                               | Browser, OS, viewport, locale, app version, app state and context                                      |
| `update_title`, `update_summary`, `set_priority`, `update_tags` | Edit a report you recorded                                                                             |

## Development

```bash
node scripts/validate-template.mjs
```

The validator checks `.cursor-plugin/plugin.json`, referenced paths, `mcp.json` and the frontmatter of rules, skills and commands.

## License

MIT
