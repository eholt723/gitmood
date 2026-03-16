# gitmood

Rotates your GitHub profile status on a schedule using GitHub Actions and the GraphQL API.

## How it works

A workflow runs every 4 hours, picks the next status from `status-list.json`, updates your GitHub profile via the GraphQL API, and commits the updated `state.json` back to the repo to track position.

Supports two modes:
- **ordered** — cycles through the list in sequence (default)
- **random** — picks randomly, never repeating the same status back-to-back

---

## Setup

### 1. Fork or clone this repo

Push it to your own GitHub account. The workflow needs to live in a repo you own.

### 2. Create a Personal Access Token

1. Go to **GitHub → Settings → Developer settings → Personal access tokens → Tokens (classic)**
2. Click **Generate new token (classic)**
3. Give it a name like `gitmood-status`
4. Select the **`user`** scope (specifically `user` → gives access to update profile status)
5. Generate and copy the token

### 3. Add the token as a repository secret

1. In your repo, go to **Settings → Secrets and variables → Actions**
2. Click **New repository secret**
3. Name: `STATUS_PAT`
4. Value: paste your token
5. Save

> Note: The default `GITHUB_TOKEN` provided by Actions does not have permission to update your profile status — that's why a PAT is required.

### 4. Allow the workflow to push commits

The workflow commits `state.json` back to the repo after each run. To allow this:

1. Go to **Settings → Actions → General**
2. Under **Workflow permissions**, select **Read and write permissions**
3. Save

### 5. Enable the workflow

Push the code. The workflow will trigger automatically on schedule. To test it immediately:

1. Go to **Actions → Rotate GitHub Status**
2. Click **Run workflow**
3. Choose `ordered` or `random` mode
4. Click **Run workflow**

---

## Customizing

### Change the schedule

Edit the `cron` line in [.github/workflows/rotate-status.yml](.github/workflows/rotate-status.yml):

```yaml
- cron: '0 */4 * * *'   # every 4 hours
- cron: '0 9 * * *'     # once daily at 9am UTC
```

Use [crontab.guru](https://crontab.guru) to build expressions.

### Change the default rotation mode

In the workflow file, change this line:

```yaml
MODE: ${{ github.event.inputs.mode || 'ordered' }}
```

Replace `'ordered'` with `'random'` to default to random rotation.

### Add or remove statuses

Edit [status-list.json](status-list.json). The workflow reads from it at runtime — no other changes needed.

---

## Files

| File | Purpose |
|------|---------|
| `.github/workflows/rotate-status.yml` | The Actions workflow |
| `status-list.json` | List of status messages |
| `state.json` | Tracks last-used index (auto-updated by workflow) |
