## Rebuild 

```bash
mvn clean install
```

## Rebuild without running tests

```bash
mvn clean install -DskipTests
```

## Rebuild and force fresh download of dependencies

```bash
mvn clean install -U
```