# Minimal example of issue with client/server mono-repo with npm workspaces

This repository is a minimal example of an issue I'm having with an npm workspaces mono-repo that contains both client and server code in TypeScript.

The client [has this line of code](packages/client/src/index.ts):

```ts
const timeout: number = setTimeout(() => {}, 1000);
```

The server [has this line of code](packages/server/src/index.ts):

```ts
const timeout: NodeJS.Timeout = setTimeout(() => {}, 1000);
```

The build fails because the type definition for `setTimeout` in the client is resolving to the node.js type definition (./node_modules/@types/node/timers.d.ts) rather than the browser type definition, despite the `"lib": ["ES2022", "DOM"]` line in the client's `tsconfig.json`. The `./node_modules/@types` package exists because it's a devDependency of the server package, but because an `npm install` is installing it into the root `node_modules`, it's also visible to the client which is causing a conflict.


## Steps to reproduce

```sh
npm install
npm run build
```

## Expected behavior

No errors


## Actual behavior

The following error:

```
packages/client/src/index.ts:2:7 - error TS2322: Type 'Timeout' is not assignable to type 'number'.

2 const timeout: number = setTimeout(() => {}, 1000);
        ~~~~~~~
```
