## Fetching remote branch

```bash
git fetch origin <branchName>
git checkout <branchName>
```

src: https://stackoverflow.com/questions/9537392/git-fetch-a-remote-branch


## Renaming remote branch

Check on which branch you are using the command below:

```bash
git branch -a 
```

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