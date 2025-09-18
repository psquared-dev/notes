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