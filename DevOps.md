# DevOps

## Git

HEAD is the alias for current working commit.

- Decorated log: `git log --online --decorate`, `git log --graph --online --decorate`
- [GitObs](https://www.atlassian.com/git/tutorials/gitops)
- Alias to create new commands:` git config --global alias.co checkout` will create a global alias for checkout. `git co` will be the same as `git checkout`.
- `git branch new-branch` creates a new branch without changing anything in current repository. To start using it, `git checkout` needs to be used.
- To create and checkout: `git checkout -b new-branch`.
- `git add` stages the changes. Git commit have `-a` for all changes and `-m` for message. Can be combined to `git commit -am "message"`. `got commit --amend` adds a new change to last commit.

Git diff shows changes between data sets.

- `git diff --color-words`: View exact words that changed instead of entire lines
- `git diff branch1..other-feature-branch`: Between branches
- `git diff staged`: compare to staged.

### Configuration

- `git config --global branch.autosetuprebase always`: always rebase when pulling from remote to local.
- VS Code "git.autofetch": true.

### Undo

Git clean: Operates on untracked files. `git clean -n` shows which files are going to be removed. `-di` for interactive mode.

Git revert: undoes changes without losing history.

Git reset: Moves the `HEAD` ref pointer.

- `-soft`: Keep the files and stage all changes back. Maybe to add a forgotten file.
- `-mixed`: default mode. Keeps Files, unstages change. Maybe to remove files from commit.
- `-hard`: removes everything

Remove local commits: `git reset --hard HEAD~2`.

Git rm: tell get not to track file anymore.

### "Oh fuck"

Git reflog gets you out of "got reset --hard". [Details](https://www.atlassian.com/git/tutorials/rewriting-history/git-reflog)

### Prepare Merge to Main

Local cleanup:

- `git checkout feature git rebase -i HEAD~3`: rewriting 3 commits on feature.
- `git merge-base feature main`: rewrite the entire feature.
-

#### Rewrite history

- Change multiple commits with git rebase.
- `-i` is for interactive. This allows changes to individual commits in the process rather then moving all commits.
- `s for squash` merges commits into previous commits. Use `r` for reword.

Tips on doing this with a VIM editor -`i` to edit. `esc` to stop edit. `:wq` to save and close.

When done, use `git push --force` to rewrite your branch.

### Merge or Rebase

- merge preserves the original branch history, while rebase rewrites it.
- merge creates a single commit that combines the changes from two branches, while rebase re-applies the commits from one branch onto another.
- merge is a good choice when you want to preserve the entire history editing of multiple branches, while rebase is useful when you want a clean, linear project history in a complex merge.

#### Rebase

To view your current Git rebase configuration, run git config --list | grep rebase

`git rebase branch`: This command applies the changes made in the specified branch to the current branch head.

#### rerere:

- reuse recorded resolution
- `git config rerere.enabled true`: Permanent global config change to .apply previous recorded merge conflicts to the next conflict.

### Conflicts

- Everything between <<<<<<< and ======= are your local changes.
- All the lines between ======= and >>>>>>> are the changes from the remote repository or another branch.

#### Strategies

- `recursive`: Default. Applies each commit on top of the new base.
- `resolve`: WIll only attempt to auto resolve conflicts. If it can't be done it will fail.

#### Rebase on top of changes

- `git pull --rebase origin main`:Move all commits to the top of the main branch ofter syncing with the changes from central repo.

#### Cherry pick

Pick commits from a branch and apply it to another. Like when a commit is made to the wrong branch. OR restoring a lost commit.

### Branch Hygiene

#### Git rebase Workflow

Make sure the commit history is useful by making sure each commit to main branch is useful and clear.

- a commit should be atomic: it should contain all and only the changes required for a feature or a fix
- a commit should have a clear, descriptive message, using "fix" and "feature". Others, like [Angular Commit Guidelines](https://github.com/angular/angular/blob/22b96b9/CONTRIBUTING.md#-commit-message-guidelines) are more detailed.

#### One Branch One Commit

Keep PR's small and always squash merges to main with a message following guidelines.

### VS Code Git Tips

- [Selecting Editor](https://www.kevinkuszyk.com/2016/03/08/git-tips-2-change-editor-for-interactive-git-rebase/)

### GitHub Actions
