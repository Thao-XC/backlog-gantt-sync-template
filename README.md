# Backlog Gantt Sync (template)

Engineers work in **GitHub Issues**; every change reaches **Backlog** within about
20 seconds, so engineering work shows on each project's **Backlog Gantt chart** next to
sales items (NDAs, quotes, contracts).

日本語: セットアップ手順は下の「Setup」と同じです。詳しくは社内の「Backlog Gantt Guide」（日本語タブ）を見てください。

**What you get**

- An **Engineering task** issue form: Project, Parent issue, Start date, Due date
- New GitHub issues become Backlog issues with dates, in the right project, as parent or child
- Four statuses from GitHub labels: no label = Open, `in-progress`, `resolved`, closed = Closed
- Commits mentioning `#21` are posted to Backlog; merging a PR with `Fixes #21` closes it
- Optional GitHub Projects board (Todo / In Progress / Resolved / Done + Roadmap)

---

## Setup (about 15 minutes)

1. **Use this template** (green button, top right) → **Create a new repository** → choose
   **Private** → **Create repository**. Keep it private: issues sync to your Backlog.
2. **Backlog API key:** Backlog → your icon → **Personal Settings** → **API** → **Register** → copy.
   The key's owner must be a member of every Backlog project you connect. A shared bot user is best.
3. **Secret:** your new repo → **Settings** → **Secrets and variables** → **Actions** →
   **New repository secret** → name `BACKLOG_API_KEY`, paste the key.
4. **Your Backlog address:** in `.github/workflows/backlog-sync.yml`, change
   `BACKLOG_HOST: your-space.backlog.jp`.
5. **Your projects:** in `.github/backlog-projects.json`, replace the examples:

   ```json
   "projects": {
     "project-a": { "projectKey": "ABC", "parentKey": "ABC-2" },
     "project-b": { "projectKey": "XYZ", "parentKey": "" }
   }
   ```

   - name (`project-a`): what engineers pick in the form; also a GitHub label
   - `projectKey`: the Backlog project key (shown next to the project name in Backlog)
   - `parentKey`: optional Backlog issue that groups engineering work (`""` = none)

   Put the same names in the **Project** list in `.github/ISSUE_TEMPLATE/engineering-task.yml`.
6. **Test:** **Issues** → **New issue** → **Engineering task**. Within ~20 seconds the title
   becomes `[ABC-n] …` and the issue is in Backlog. A red ❌ in the **Actions** tab shows the error.
7. **Optional:** bring existing Backlog engineering issues in with **Actions** → **Sync to Backlog**
   → **Run workflow** → mode `import` (copies children of `parentKey`; projects with no
   `parentKey` are skipped so sales items stay in Backlog).

## Daily use

| On the GitHub issue | Backlog |
| --- | --- |
| Create with the **Engineering task** form | New issue with dates, in the chosen project |
| Label `in-progress` (or assign someone, or push a commit with `#n`) | In Progress |
| Label `resolved` | Resolved (done, waiting for a check) |
| Close the issue, or merge a PR with `Fixes #n` | Closed |
| Remove the label / reopen | Back to Open / In Progress |
| Edit title, dates, **Parent issue** | Updated; the Gantt bar moves |

- **Parent issue** box: empty = the project's `parentKey`; `none` = a new parent;
  `#21` or `ABC-17` = child of that issue. Backlog allows one level, in the same project.
- Never delete the `[ABC-n]` at the start of a title: it links the issue to Backlog.
- Changes flow GitHub → Backlog only. Edit engineering issues in GitHub.

## Projects board (optional)

1. GitHub → **Settings** → **Developer settings** → **Personal access tokens** →
   **Tokens (classic)** → generate with scopes **repo** and **project**.
2. Save it as the repo secret `PROJECT_TOKEN`.
3. **Actions** → **Sync to Backlog** → **Run workflow** → mode `setup-board`.
4. Open the board → **+ New view** → **Board**, and **+ New view** → **Roadmap**.

The board follows the labels. Change status with labels, not by dragging cards.

## Files

| File | Purpose |
| --- | --- |
| `.github/workflows/backlog-sync.yml` | When the sync runs; Backlog address |
| `.github/scripts/backlog-sync.js` | GitHub → Backlog sync |
| `.github/scripts/board.js` | Projects board |
| `.github/backlog-projects.json` | Your projects |
| `.github/ISSUE_TEMPLATE/engineering-task.yml` | The issue form |

## Troubleshooting

| Problem | Fix |
| --- | --- |
| Red ❌ in **Actions** | Check `BACKLOG_API_KEY`, and that its owner is a member of that Backlog project |
| Bot: "no project chosen" | Add the project label (e.g. `project-a`) to the issue |
| Bot: parent problem | Edit the **Parent issue** line (one level, same project) |
| Label has no effect | Put it on the **issue**, not the pull request |
| No bar on the Gantt | Add both dates, or move the Gantt's Start Date earlier |
