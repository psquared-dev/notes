## Config

```bash
git config user.name "prateek"
git config user.email "prateek@mail.com"
```

## Branch

### Fetching remote branch

```bash
git fetch origin <branchName>
git checkout <branchName>
```

src: https://stackoverflow.com/questions/9537392/git-fetch-a-remote-branch


### Renaming remote branch

Checkout to the branch you want to rename:

```bash
git checkout branch_to_rename
```

Rename the branch using:

```bash
git branch -m new_name
```

Push the changes:

```bash
git push -u origin :old_name new_name
```

### View all branches local and remote

```bash
git branch -a
```

## Fetch

### Fetch and Merge

This command pull all changes from the `origin/master` to local. However, it doesn't merge it.

```bash
git fetch origin master
```

To merge changes, execute `git merge`:

```bash
git merge origin/master
```

Instead of executing the above two commands, just use `git pull`.

```bash
git pull origin master
```

### Pulling remote changes

```bash
git fetch origin
```

or

```bash
git fetch
```

Git downloads all the branch references from origin, but doesn't merge them.

## Pull

```bash
git pull origin
```


## Git stash

To stash do this:

```bash
git stash -u
```

The `-u` option also stashes the untracked changes.

View stash list:

```bash
git stash list
```

Apply the last stash

```bash
git stash apply 0
```
or

```bash
git stash pop
```

To pop a specific stack:

```bash
git stash pop stash@{2}
```

### View Changes in a stash

```bash
git stash show stash@{2}
```

This shows changes saved in the 3rd stash in your stack.



## Git log 

List changed files along with the log.

```bash
git log --name-only
```

Get logs in short.

```bash
git log --oneline
```

View graph:

```bash
git log --graph --decorate
```


View all commits on a remote branch:

```bash
git log origin/master
```


## Git revert

```bash
git revert hash_id
```

## Git rebase

### Interactive rebase

```bash
git rebase -i HEAD~5
```

Interactive rebase:

Lets you:

* reorder commits
* squash commits (combine multiple into one)
* edit commit messages

### Keep feature branch up-up date

Before rebase:

```text
main:   A---B---C
              \
feature:       D---E
```

After `git checkout feature` && `git rebase main`:

```text
main:   A---B---C
                  \
feature:           D'---E'
```

### Change branch base

Example: You branched `feature` from `dev`, but later realize it should be based on `main`.

```bash
git checkout feature
git rebase --onto main dev feature
```

## Git reset

`git reset` is one of those commands that changes ***where your branch is pointing*** and optionally affects your working directory/staging area.

There are 3 main modes:

1. `git reset --soft <commit>`

* Moves the branch pointer back to <commit>.
* Keeps all your changes staged
* Example:
    ```bash
    git reset --soft HEAD~1
    ```

    Undo the last commit, but keep its changes staged (ready for recommit).


2. `git reset --mixed <commit> (default)`

* Moves the branch pointer back to <commit>.
* Keeps changes in your working directory, but unstaged.
* Example:
    ```bash
    git reset HEAD~1
    ```

    Undo the last commit, keep changes in files, but not staged.


3. `git reset --hard <commit>`

* Moves the branch pointer back to <commit>.
* Discards all changes (both staged and unstaged) in your working directory.
* ⚠️ DANGEROUS — you lose work unless backed up.
* Example:
    ```bash
    git reset --hard HEAD~1
    ```

    Completely removes the last commit and its changes.