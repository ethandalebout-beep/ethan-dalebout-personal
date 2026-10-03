# Personal DevOps Project

This project uses the [Docker node-bulletin-board sample](https://github.com/dockersamples/node-bulletin-board). The application source and original licenses are preserved; see [README.upstream.md](README.upstream.md) for the original instructions.

## Run the application

With Docker running, execute from the repository root:

```bash
docker build -t bulletin-board ./bulletin-board-app
docker run --rm --name bulletin-board -p 8080:8080 bulletin-board
```

Open <http://localhost:8080>. Stop the container from another terminal with `docker stop bulletin-board`.

## Everyday Git workflow

Git saves project history locally; GitHub hosts a shared copy. The **staging area** holds changes selected for the next commit. **Origin** names the remote repository, and **HEAD** refers to your current commit.

Start new work from an updated `main`, then save and publish a separate branch:

```bash
git switch main
git pull --ff-only origin main
git switch -c improve-readme
# Edit README.md.
git status --short
git add README.md
git diff --staged
git commit -m "Improve Git instructions"
git push -u origin improve-readme
```

Open a pull request into `main` and obtain the required review before merging. The examples below are separate choices, not one script. Replace `COMMIT_ID` with an identifier from `git log`.

### 1. [Add](https://git-scm.com/docs/git-add): select changes

```bash
git add README.md
```

Stage one file for the next commit. Useful flags: `-A` stages all additions, edits, and deletions; `-u` stages only tracked-file changes; `-p` lets you select portions interactively. `git add .` stages changes under the current directory. Review staged contents before committing and exclude credentials.

### 2. [Push](https://git-scm.com/docs/git-push): upload commits

```bash
git push -u origin init
```

Use this for the first push of `init`. `-u` remembers its upstream branch so later `git push` commands know the destination. `--dry-run` previews an update; `--verbose` adds detail. Uncommitted edits are not uploaded. If a push is rejected, inspect remote changes instead of using `--force`.

### 3. [Pull](https://git-scm.com/docs/git-pull): receive changes

```bash
git pull --ff-only origin main
```

Run this while on `main`, with unfinished work committed or stashed. Pull fetches and integrates changes into the current branch. `--ff-only` stops if histories have diverged; `--rebase` replays local commits and is best reserved for unpublished work; `--prune` removes obsolete remote-tracking references.

### 4. [Remote](https://git-scm.com/docs/git-remote): change origin

```bash
git remote set-url origin git@github.com:ethandalebout-beep/ethan-dalebout-personal.git
git remote -v
```

The first command changes the repository address without uploading anything. `-v` displays fetch and push addresses. `git remote get-url --all origin` lists configured fetch URLs; add `--push` to inspect push URLs. Verify the owner and repository before pushing.

### 5. [Stash](https://git-scm.com/docs/git-stash): pause unfinished work

```bash
git stash push -u -m "README draft"
git stash apply
```

The first command saves work; the second restores it when ready. `-u` includes untracked files but excludes ignored files; `-m` adds a description; `-p` selects portions to stash. `apply` keeps the saved entry. `git stash pop` restores and removes it after successful application.

### 6. [Revert](https://git-scm.com/docs/git-revert): undo shared changes

```bash
git revert --no-edit COMMIT_ID
```

Start with a clean working tree. Revert creates a new commit reversing an earlier change while preserving history, making it appropriate for shared commits. `--no-edit` accepts the generated message; `--no-commit` leaves the reversal uncommitted for review; `--abort` cancels an in-progress revert.

### 7. [Reset](https://git-scm.com/docs/git-reset): revise local work

```bash
git reset --soft HEAD~1
```

This undoes the latest local commit while keeping changes staged. `--mixed` keeps file changes unstaged. `--hard` discards uncommitted tracked changes and may remove obstructing untracked files—save needed work first. Use history-moving reset only for unpublished commits. To unstage without changing files, use `git reset -- README.md`.

### 8. [Log](https://git-scm.com/docs/git-log): browse history

```bash
git log --oneline --graph -n 10
```

Find commits and their identifiers. `--oneline` shows compact entries; `--graph` draws branch relationships; `-n 10` limits output to ten commits. Press `q` to close the history viewer.

### 9. [Diff](https://git-scm.com/docs/git-diff): inspect changes

```bash
git diff --staged
```

Review exactly what the next commit will contain. Without flags, diff shows unstaged tracked changes. `--staged` compares staged contents with the last commit; `--stat` summarizes changed-line counts; `--name-only` lists affected paths. New untracked files appear after staging.

### 10. [Show](https://git-scm.com/docs/git-show): inspect a commit

```bash
git show --stat HEAD
```

Inspect the latest commit, or replace `HEAD` with a commit identifier. `--stat` summarizes changed files and line counts; `--name-only` lists affected filenames; `--no-patch` hides changes when you only need commit details.
