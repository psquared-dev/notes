## Check port is open or not

```bash
lsof -i:8080
```

## Checkout to master and take pull

```bash
find . -type d -name .git -execdir git checkout master \;
find . -type d -name .git -execdir git pull \;
```



