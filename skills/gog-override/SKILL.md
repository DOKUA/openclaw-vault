---
name: gog
description: Google Workspace CLI for Gmail, Calendar, Drive, Contacts, Sheets, Docs, and Tasks.
homepage: https://gogcli.sh
metadata:
  {
    "openclaw":
      {
        "emoji": "🎮",
        "requires": { "bins": ["gog"] },
        "install":
          [
            {
              "id": "brew",
              "kind": "brew",
              "formula": "steipete/tap/gogcli",
              "bins": ["gog"],
              "label": "Install gog (brew)",
            },
          ],
      },
  }
---

# gog

Use `gog` for Gmail/Calendar/Drive/Contacts/Sheets/Docs/Tasks. Requires OAuth setup.

Setup (once)

- `gog auth credentials /path/to/client_secret.json`
- `gog auth add you@gmail.com --services gmail,calendar,drive,contacts,docs,sheets,tasks`
- `gog auth list`

Default account: `GOG_ACCOUNT=andrusevych.ops@gmail.com`
Default task list ID: `MTI1NjQxNTYxMjI5MTMzODU3NDc6MDow` (title: "Мої завдання")

## Google Tasks

- List task lists: `gog tasks lists list`
- List tasks: `gog tasks list <tasklistId>`
- Add task: `gog tasks add <tasklistId> --title "Task title"`
- Add task with due date: `gog tasks add <tasklistId> --title "Task" --due 2026-03-10`
- Add task with notes: `gog tasks add <tasklistId> --title "Task" --notes "Description here"`
- Add subtask: `gog tasks add <tasklistId> --title "Subtask" --parent <parentTaskId>`
- Add repeating task: `gog tasks add <tasklistId> --title "Daily standup" --repeat daily`
- Complete task: `gog tasks done <tasklistId> <taskId>`
- Uncomplete task: `gog tasks undo <tasklistId> <taskId>`
- Update task: `gog tasks update <tasklistId> <taskId> --title "New title"`
- Delete task: `gog tasks delete <tasklistId> <taskId>`
- Get task info: `gog tasks get <tasklistId> <taskId>`

When user asks to create a task/завдання, ALWAYS use `gog tasks add` (not calendar events).
When user asks to create a meeting/зустріч/подію, use `gog calendar create`.

## Sheets — Extended operations (via helper script)

The helper `/data/gog-sheets-helper.sh` provides operations not available in gog CLI:

- Duplicate/copy a sheet tab: `/data/gog-sheets-helper.sh duplicate-sheet <spreadsheetId> "Source Tab Name" "New Tab Name"`
- Add empty sheet tab: `/data/gog-sheets-helper.sh add-sheet <spreadsheetId> "New Tab Name"`

Example workflow — duplicate "Лютий" tab as "Березень 2026":
1. Find the spreadsheet: `gog drive search "Звіт по витратам" --max 5 --json`
2. Get metadata to see tabs: `gog sheets metadata <spreadsheetId> --json`
3. Duplicate tab: `/data/gog-sheets-helper.sh duplicate-sheet <spreadsheetId> "Лютий" "Березень 2026"`
4. Clear data in new tab (keep headers): `gog sheets clear <spreadsheetId> "Березень 2026!A2:Z"`

IMPORTANT: When user asks to add/create a new sheet/tab/page/сторінку/вкладку in a spreadsheet, ALWAYS use `/data/gog-sheets-helper.sh duplicate-sheet` or `/data/gog-sheets-helper.sh add-sheet`. Never say you cannot do this.

## Common commands

- Gmail search: `gog gmail search 'newer_than:7d' --max 10`
- Gmail messages search (per email, ignores threading): `gog gmail messages search "in:inbox from:ryanair.com" --max 20 --account you@example.com`
- Gmail send (plain): `gog gmail send --to a@b.com --subject "Hi" --body "Hello"`
- Gmail send (multi-line): `gog gmail send --to a@b.com --subject "Hi" --body-file ./message.txt`
- Gmail send (stdin): `gog gmail send --to a@b.com --subject "Hi" --body-file -`
- Gmail send (HTML): `gog gmail send --to a@b.com --subject "Hi" --body-html "<p>Hello</p>"`
- Gmail draft: `gog gmail drafts create --to a@b.com --subject "Hi" --body-file ./message.txt`
- Gmail send draft: `gog gmail drafts send <draftId>`
- Gmail reply: `gog gmail send --to a@b.com --subject "Re: Hi" --body "Reply" --reply-to-message-id <msgId>`
- Calendar list events: `gog calendar events <calendarId> --from <iso> --to <iso>`
- Calendar create event: `gog calendar create <calendarId> --summary "Title" --from <iso> --to <iso>`
- Calendar create with color: `gog calendar create <calendarId> --summary "Title" --from <iso> --to <iso> --event-color 7`
- Calendar update event: `gog calendar update <calendarId> <eventId> --summary "New Title" --event-color 4`
- Calendar show colors: `gog calendar colors`
- Drive search: `gog drive search "query" --max 10`
- Contacts: `gog contacts list --max 20`
- Sheets get: `gog sheets get <sheetId> "Tab!A1:D10" --json`
- Sheets update: `gog sheets update <sheetId> "Tab!A1:B2" --values-json '[["A","B"],["1","2"]]' --input USER_ENTERED`
- Sheets append: `gog sheets append <sheetId> "Tab!A:C" --values-json '[["x","y","z"]]' --insert INSERT_ROWS`
- Sheets clear: `gog sheets clear <sheetId> "Tab!A2:Z"`
- Sheets metadata: `gog sheets metadata <sheetId> --json`
- Docs export: `gog docs export <docId> --format txt --out /tmp/doc.txt`
- Docs cat: `gog docs cat <docId>`

Calendar Colors

- Use `gog calendar colors` to see all available event colors (IDs 1-11)
- Add colors to events with `--event-color <id>` flag
- Event color IDs (from `gog calendar colors` output):
  - 1: #a4bdfc
  - 2: #7ae7bf
  - 3: #dbadff
  - 4: #ff887c
  - 5: #fbd75b
  - 6: #ffb878
  - 7: #46d6db
  - 8: #e1e1e1
  - 9: #5484ed
  - 10: #51b749
  - 11: #dc2127

Email Formatting

- Prefer plain text. Use `--body-file` for multi-paragraph messages (or `--body-file -` for stdin).
- Same `--body-file` pattern works for drafts and replies.
- `--body` does not unescape `\n`. If you need inline newlines, use a heredoc or `$'Line 1\n\nLine 2'`.
- Use `--body-html` only when you need rich formatting.
- HTML tags: `<p>` for paragraphs, `<br>` for line breaks, `<strong>` for bold, `<em>` for italic, `<a href="url">` for links, `<ul>`/`<li>` for lists.

Notes

- Set `GOG_ACCOUNT=andrusevych.ops@gmail.com` to avoid repeating `--account`.
- For scripting, prefer `--json` plus `--no-input`.
- Sheets values can be passed via `--values-json` (recommended) or as inline rows.
- Docs supports export/cat/copy. In-place edits require a Docs API client (not in gog).
- Confirm before sending mail or creating events.
- `gog gmail search` returns one row per thread; use `gog gmail messages search` when you need every individual email returned separately.
