# BunkerWeb Taskboard — tasks.json Reference

The taskboard at `taskboard.html` is **read-only**. Tasks are managed exclusively by editing `tasks.json` in this folder and pushing to the repository. The board auto-syncs from the raw GitHub URL every 30 seconds.

---

## File structure

```json
{
  "version": 1,
  "project": "BunkerWeb Config Generator",
  "tasks": [ ...task objects... ]
}
```

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| `version` | number | yes | Always `1` |
| `project` | string | yes | Displayed in the board header |
| `tasks` | array | yes | List of task objects (see below) |

---

## Task object

```json
{
  "id":          "bwcg-001",
  "title":       "Short task title",
  "description": "Longer explanation of what needs to be done and why.",
  "priority":    "high",
  "column":      "planned",
  "tags":        ["template"],
  "created":     "2026-03-13"
}
```

| Field | Type | Required | Notes |
|-------|------|----------|-------|
| `id` | string | yes | **Must be unique** across all tasks. Duplicate IDs cause the board to silently drop all but the first. Convention: `bwcg-001`, `bwcg-002`, … |
| `title` | string | yes | Short, action-oriented title shown on the card |
| `description` | string | yes | Full description (can be empty string `""` but a sentence is better) |
| `priority` | string | yes | See priority values below |
| `column` | string | yes | See column/status values below |
| `tags` | array | yes | Array of strings. First element is shown as the tag badge on the card. Use `[]` for no tag. |
| `created` | string | yes | ISO date `YYYY-MM-DD` — shown on the card footer |

---

## Column / Status values

These are the five columns of the board. Set `"column"` to exactly one of the values below.

| `column` value | Board column | Colour | Meaning |
|----------------|-------------|--------|---------|
| `"planned"` | **Planned** | Grey | Idea accepted, not yet started. Default for new tasks. |
| `"next"` | **Next** | Blue | Queued up — will be started in the near future |
| `"inprogress"` | **In Progress** | Yellow | Actively being worked on right now |
| `"blocked"` | **Blocked** | Red | Work has started but is stuck waiting on something external |
| `"done"` | **Done** | Green | Completed |

> **Tip:** Move a task between columns by changing its `"column"` value in `tasks.json`, committing, and pushing. The board will pick it up within 30 seconds.

---

## Priority values

| `priority` value | Badge colour | Meaning |
|-----------------|-------------|---------|
| `"high"` | Red | Must be done soon; blocks other work or affects users |
| `"medium"` | Orange | Important but not blocking anything |
| `"low"` | Green | Nice to have; do when time allows |

---

## Tags

Tags are free-form strings. Only the **first element** of the `tags` array is shown on the card badge.

Common conventions used in this project:

| Tag | Meaning |
|-----|---------|
| `"template"` | Adding or updating a service template |
| `"feature"` | New functionality in the generator itself |
| `"bug"` | Something broken that needs fixing |
| `"ui"` | Visual / UX improvement |
| `"ssl"` | SSL/TLS related work |
| `"docs"` | Documentation |

---

## Inline status markers

Any task description can include an **inline status note** by wrapping text between `###` markers. The board strips the markers and renders the enclosed text as an animated amber badge directly on the card — useful for surfacing blockers or current progress without changing the main description.

**Syntax:**

```
"description": "Main description text. - ### Status note shown as animated badge. ###"
```

**Example:**

```json
{
  "id":          "bwcg-001",
  "title":       "Add Synapse template",
  "description": "Create a BunkerWeb template for Synapse (Matrix homeserver). Needs federation endpoint proxying, WebSocket support for the client-server API, and correct headers for the .well-known discovery routes. - ### Currently stuck to get livekit reliably running for video and audio calls. ###",
  "priority":    "high",
  "column":      "blocked",
  "tags":        ["template"],
  "created":     "2026-03-13"
}
```

The card will display:
- The main description text (without the `###` section)
- An animated pulsing amber badge reading: *Currently stuck to get livekit reliably running for video and audio calls.*

**Notes:**
- The `###` markers and everything between them are never shown as raw text.
- Only one `### ... ###` block per description is supported; the first match is used.
- Tasks without any `### ... ###` in their description are completely unaffected.
- The status badge is intentionally eye-catching — best used for active blockers or important progress notes, not as a permanent part of the description.

---

## Workflow example

### Starting work on a task

```json
"column": "inprogress"
```

### Task is blocked

```json
"column": "blocked",
"description": "Waiting for upstream PR #42 to be merged before this can proceed."
```

### Task is blocked with a status note

```json
"column": "blocked",
"description": "Waiting for upstream PR #42 to be merged before this can proceed. - ### PR is open, awaiting maintainer review since 2026-03-20. ###"
```

### Task is done

```json
"column": "done"
```

### Adding a new task

1. Pick the next available ID (e.g. `"bwcg-009"`)
2. Add the object to the `"tasks"` array
3. Commit and push — the board syncs automatically

```json
{
  "id":          "bwcg-009",
  "title":       "Add Immich template",
  "description": "Create a BunkerWeb template for Immich photo management. Needs WebSocket support and large upload size for photo/video files.",
  "priority":    "medium",
  "column":      "planned",
  "tags":        ["template"],
  "created":     "2026-03-14"
}
```

---

## Common mistakes

| Problem | Cause | Fix |
|---------|-------|-----|
| Task missing from board | Duplicate `id` | Every task must have a unique ID |
| Board shows no tasks | JSON syntax error | Validate with [jsonlint.com](https://jsonlint.com) |
| Task stuck in wrong column | Wrong `column` value | Check spelling — valid values: `planned`, `next`, `inprogress`, `blocked`, `done` |
| Tag badge missing | `tags` is `[]` or first element is empty | Add at least one non-empty string to `tags` |
| Status markers showing as raw text | Mismatched `###` | Ensure both the opening and closing `###` are present |

---

## Source URL

The board fetches tasks from:

```
https://raw.githubusercontent.com/palmcoasty/bunkerweb-confs-taskboard/refs/heads/main/tasks.json
```

To change it temporarily (local testing or a fork), click **Source URL** in the board toolbar and enter an alternative raw JSON URL. The setting is saved in `localStorage` and survives page reloads. Click **Reset to Default** to restore the original URL.
