# Personal DevOps Project

This repository contains the [Docker node-bulletin-board sample](https://github.com/dockersamples/node-bulletin-board) and a practical Git guide for the personal repository assignment. The application source and original licenses are preserved. The original upstream README is in [README.upstream.md](README.upstream.md).

## Sample application

The application files and Dockerfile are in `bulletin-board-app/`. With Docker installed and running, execute these commands from the repository root:

```bash
docker build -t bulletin-board ./bulletin-board-app
docker run --rm --name bulletin-board -p 8080:8080 bulletin-board
```

Open <http://localhost:8080>. Press `Ctrl+C` to stop this foreground container. To run directly with Node.js instead:

```bash
cd bulletin-board-app
npm install
npm start
```

Return to the repository root with `cd ..` before following the Git examples below.

## Git guide

Git records a project's history locally. GitHub hosts a remote copy so people can share changes and review pull requests.

- **Working tree:** the files you are editing.
- **Staging area (index):** the changes selected for the next commit.
- **Commit:** a saved snapshot with a message and an identifier.
- **Branch:** a named line of development. `HEAD` usually points to the current branch's latest commit; `HEAD~1` means its first parent.
- **Remote:** a saved repository address. `origin` is the conventional name created by cloning.

Run these examples from the repository folder. Replace `COMMIT_ID` with an identifier from `git log`. Commands in separate examples are alternatives, not a script to run from top to bottom.

### A normal change from edit to pull request

After the repository has a `main` branch, start new work from its current state:

```bash
git switch main
git pull --ff-only origin main
git switch -c improve-readme
# Edit README.md, then review and save the change.
git status --short
git diff
git add README.md
git diff --staged
git commit -m "Improve the Git usage guide"
git push -u origin improve-readme
```

Open a pull request on GitHub from `improve-readme` into `main`. With the assignment's protection rule, an eligible reviewer must approve it before merging. Future pushes to the same branch update that pull request; when they change its diff, stale-approval dismissal requires a fresh approval. See [GitHub's protected branch documentation](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-protected-branches/about-protected-branches).

For the assignment's first push from the `init` branch, use `git push -u origin init`. The `-u` option records which remote branch this local branch tracks. A cloned project already has commits, so running `git add .` without editing any files does not produce a new commit. Make the intended README change before creating the assignment's `My first push` commit.

### 1. Stage changes with `git add`

Staging selects the file contents that the next commit will save. Edits made after staging need to be staged again to enter that commit.

| Command | Use |
| --- | --- |
| `git add README.md` | Stage one file. |
| `git add .` | Stage additions, edits, and deletions in the current directory and its children. |
| `git add -A` | Stage additions, edits, and deletions across the repository. |
| `git add -u` | Stage edits and deletions to tracked files, without adding new files. |
| `git add -p` | Choose individual portions of changes interactively. |
| `git add --dry-run .` | Preview affected paths without changing the staging area. |

Check `git status` and `git diff --staged` before committing. Avoid staging credentials, SSH private keys, or generated dependencies.

Reference: [Git add documentation](https://git-scm.com/docs/git-add).

### 2. Upload commits with `git push`

Push sends committed history to a remote. It does not send uncommitted edits.

```bash
git push -u origin init
git push
git push --dry-run origin init
git push --verbose origin init
```

- `-u` / `--set-upstream` links the local branch to its remote branch for later pushes and pulls.
- `--dry-run` previews the update without sending it.
- `--verbose` gives more information about the operation.

If Git rejects a push because the remote has new commits, fetch and inspect those commits before deciding how to integrate them. Do not use `--force` as a routine fix: it can replace remote history. Push work branches and use pull requests for protected `main`.

Reference: [Git push documentation](https://git-scm.com/docs/git-push).

### 3. Download and integrate commits with `git pull`

Pull fetches remote commits and integrates the selected remote branch into your **current** local branch. Check `git status` first and commit or stash unfinished changes.

```bash
git switch main
git pull --ff-only origin main
```

| Flag | Use |
| --- | --- |
| `--ff-only` | Update only when no local history has diverged; otherwise stop for review. This is a useful explicit default. |
| `--rebase` | Replay local commits on top of the fetched history. Prefer this only for unpublished local commits. |
| `--no-rebase` | Integrate by merging, potentially creating a merge commit. |
| `--prune` | Remove obsolete remote-tracking references during the fetch step. |

If `--ff-only` stops, inspect `git log --oneline --graph --all` before choosing merge or rebase. Use `git merge --abort` or `git rebase --abort` to cancel the corresponding conflicted operation.

Reference: [Git pull documentation](https://git-scm.com/docs/git-pull).

### 4. Change the origin URL

Changing the remote address directs later fetches and pushes to the intended repository. It does not upload files or change existing commits.

```bash
git remote -v
git remote set-url origin git@github.com:ethandalebout-beep/ethan-dalebout-personal.git
git remote -v
git remote get-url --push origin
```

- `git remote -v` displays remote names and their fetch/push addresses.
- `git remote get-url --all origin` displays every configured fetch URL.
- `git remote get-url --push --all origin` checks every push URL.
- `git remote set-url --push origin NEW_URL` changes a separately configured push URL. Use this only when fetch and push need different addresses for the same repository.

In a typical clone there is one URL and the ordinary `set-url` command is enough. If a separate push URL exists, verify and update it too. Keep the owner in the URL consistent with the repository actually created on GitHub.

Reference: [Git remote documentation](https://git-scm.com/docs/git-remote).

### 5. Temporarily set work aside with `git stash`

Stash saves unfinished local changes so you can work with a clean tree.

```bash
git stash push -u -m "README draft"
git stash list
git stash show -p 'stash@{0}'
git stash apply 'stash@{0}'
```

- `push -u` / `--include-untracked` includes new files as well as tracked changes; ignored files are excluded.
- `push -m` gives the stash a useful description.
- `push -p` selects portions of changes interactively.
- `show -p` displays the saved patch.
- `apply --index` also attempts to restore which changes were staged.

`apply` keeps the stash entry. After verifying the restored work, remove it with `git stash drop 'stash@{0}'`. Alternatively, `git stash pop` applies the latest stash and removes it when application succeeds; conflicts leave the entry available. Ordinary pushes do not upload your stash.

Reference: [Git stash documentation](https://git-scm.com/docs/git-stash).

### 6. Undo a committed change with `git revert`

Revert creates a new commit that reverses an earlier commit's changes. It preserves the existing history, making it appropriate for commits already shared with others. Start from a clean working tree.

```bash
git revert COMMIT_ID
git revert --no-edit COMMIT_ID
```

- `--no-edit` uses the generated commit message without opening an editor.
- `-n` / `--no-commit` applies the reversal without committing yet, allowing review before `git commit`.
- `--continue` resumes after you resolve conflicts and stage the resolutions.
- `--abort` cancels an in-progress revert and returns to its starting state.

For a protected branch, create a work branch, make the revert there, and submit it through a pull request.

Reference: [Git revert documentation](https://git-scm.com/docs/git-revert).

### 7. Unstage or move local history with `git reset`

Reset has different effects depending on its form. Use the file form to unstage without discarding the edited file:

```bash
git reset -- README.md
git reset -p
```

`-p` selects portions to unstage. When a commit is supplied instead, reset moves the current branch to that commit:

| Command | Result |
| --- | --- |
| `git reset --soft HEAD~1` | Undo the last local commit and keep its changes staged. |
| `git reset --mixed HEAD~1` | Undo the last local commit and keep its changes in files, unstaged. `--mixed` is the default mode. |
| `git reset --hard HEAD~1` | Move back one commit and replace the index and working tree with that version. Uncommitted tracked changes are discarded; obstructing untracked files may also be overwritten or removed. |

Use history-moving reset for unpublished local commits. Prefer revert for shared commits. Before a hard reset, preserve any work you need with a commit, stash, or separate backup; do not treat it as an ordinary cleanup command.

Reference: [Git reset documentation](https://git-scm.com/docs/git-reset).

### 8. Browse history with `git log`

Log shows commits. Use it to find the identifier needed by commands such as `show` and `revert`.

```bash
git log --oneline --graph --decorate --all -n 15
git log -p -n 3 -- README.md
git log --since="1 week ago" --author="Your Name"
```

- `--oneline` gives a compact identifier and subject for each commit.
- `--graph` draws branch and merge relationships.
- `--decorate` labels commits with branch and tag names.
- `--all` includes history reachable from all references.
- `-n 15` limits output to 15 commits.
- `-p` includes each commit's patch.
- `--since` and `--author` filter results.
- `-- README.md` limits history to a file.

Reference: [Git log documentation](https://git-scm.com/docs/git-log).

### 9. Compare changes with `git diff`

Use diff before staging and again before committing.

| Command | Comparison or output |
| --- | --- |
| `git diff` | Unstaged tracked changes: working tree versus staging area. |
| `git diff --staged` | Staged changes versus the last commit; `--cached` is equivalent. |
| `git diff HEAD` | Tracked working-tree contents versus the last commit, including staged and unstaged changes. |
| `git diff --stat` | Per-file summary of unstaged changes. |
| `git diff --name-only` | Names of changed files. |
| `git diff --word-diff` | Show differences at word level. |
| `git diff --staged --check` | Check staged changes for whitespace errors and conflict markers. |
| `git diff main...init` | Compare `init` with its common ancestor with `main`, useful when reviewing branch work. |

Ordinary diff does not display the contents of untracked files; stage a new file to inspect it with `--staged`. Add `-- README.md` to restrict a comparison to that path.

Reference: [Git diff documentation](https://git-scm.com/docs/git-diff).

### 10. Inspect one commit or file version with `git show`

Show is useful for examining a specific saved change or reading an old file without replacing your current copy.

```bash
git show HEAD
git show --stat COMMIT_ID
git show --name-status COMMIT_ID
git show --no-patch --format=fuller COMMIT_ID
git show HEAD~1:README.md
```

- `--stat` summarizes affected files and line counts.
- `--name-status` lists paths with statuses such as added, modified, or deleted.
- `--no-patch` hides the patch when you only need commit information.
- `--format=fuller` includes detailed author and committer information.
- `COMMIT_ID:path/to/file` displays that file as saved in a particular commit.

Many history commands open a pager for long output; press `q` to return to the terminal.

Reference: [Git show documentation](https://git-scm.com/docs/git-show).
