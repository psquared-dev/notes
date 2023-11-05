There are 3 situations where we explicitly write the type:

1. when a function returns any. For e.g: JSON.parse()
2. When we declare a variable and initialize it later
3. When variable variable type can't be inferred correctly.

#######################################################

To install typescript locally use the following command:

```bash
npm install typescript
```

The above command automatically creates the `package.json` if it doesn't exists. Otherwise, we can manually create
the file using `npm init -y`.

Now to invoke the typescript, use the following:

```bash
npx tsc
```

To create tsconfig.json, use the following:

```bash
npx tsc --init
```

The following are some imp configurations:

outdir - where the tsc should put the compiled output
rootdir - Specify the root folder within your source files

The following command watches the filesystem for changes:

```bash
tsc -w
```
