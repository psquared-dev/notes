### Create skeleton 

```bash
helm create nginx-chart
```

### Lint chart

```bash
helm lint ./nginx-chart
```

### Render the template

```bash
helm template ./nginx-chart
```

To view error in detail:

```bash
helm template ./nginx-chart --debug
```

there might still be errors, which only k8s can tell:

```bash
helm install hello-world-1 ./nginx-chart --dry-run
```

