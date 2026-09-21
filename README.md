# `undici-types` repro for `bun`

`bun` has a new global store feature that keeps the contents of packages out of the local `./node_modules/` folder and in a centralized store. This can be combined with [isolated installs](https://bun.com/docs/pm/isolated-installs).

However, when these two features are combined, it causes type checking for `@types/bun` to fail.

This is because `undici-types` references the `node` types but does not declare a dependency on them — it expects to have ambient access to the `node` types. Given that `@types/node` already depends on `undici-types`, I expect that the TypeScript team would not want to declare a dependency on `@types/node` from `undici-types` — circular dependencies are possible but more likely to cause issues.

## Repro

```shell
git clone https://github.com/lgarron/bun-undici-types-repro && cd ./bun-undici-types-repro

bun install
npx -- typescript --project .
```

This fails with:

```
../../../../../.cache/.bun/install/cache/links/bun-types@1.4.2-f5499e5b4c2fb4dd/node_modules/bun-types/bun.d.ts:688:26 - error TS2307: Cannot find module 'undici-types' or its corresponding type declarations.

688     credentials?: import("undici-types").RequestCredentials | undefined;
                             ~~~~~~~~~~~~~~

../../../../../.cache/.bun/install/cache/links/bun-types@1.4.2-f5499e5b4c2fb4dd/node_modules/bun-types/fetch.d.ts:24:75 - error TS2307: Cannot find module 'undici-types' or its corresponding type declarations.

24     type LibOrFallbackHeaders = LibDomIsLoaded extends true ? {} : import("undici-types").Headers;
                                                                             ~~~~~~~~~~~~~~

../../../../../.cache/.bun/install/cache/links/bun-types@1.4.2-f5499e5b4c2fb4dd/node_modules/bun-types/fetch.d.ts:25:75 - error TS2307: Cannot find module 'undici-types' or its corresponding type declarations.

25     type LibOrFallbackRequest = LibDomIsLoaded extends true ? {} : import("undici-types").Request;
                                                                             ~~~~~~~~~~~~~~

../../../../../.cache/.bun/install/cache/links/bun-types@1.4.2-f5499e5b4c2fb4dd/node_modules/bun-types/fetch.d.ts:26:76 - error TS2307: Cannot find module 'undici-types' or its corresponding type declarations.

26     type LibOrFallbackResponse = LibDomIsLoaded extends true ? {} : import("undici-types").Response;
                                                                              ~~~~~~~~~~~~~~

../../../../../.cache/.bun/install/cache/links/bun-types@1.4.2-f5499e5b4c2fb4dd/node_modules/bun-types/fetch.d.ts:27:80 - error TS2307: Cannot find module 'undici-types' or its corresponding type declarations.

27     type LibOrFallbackResponseInit = LibDomIsLoaded extends true ? {} : import("undici-types").ResponseInit;
                                                                                  ~~~~~~~~~~~~~~

../../../../../.cache/.bun/install/cache/links/bun-types@1.4.2-f5499e5b4c2fb4dd/node_modules/bun-types/fetch.d.ts:30:21 - error TS2307: Cannot find module 'undici-types' or its corresponding type declarations.

30       : Omit<import("undici-types").RequestInit, "body" | "headers"> & {
                       ~~~~~~~~~~~~~~

../../../../../.cache/.bun/install/cache/links/bun-types@1.4.2-f5499e5b4c2fb4dd/node_modules/bun-types/globals.d.ts:56:76 - error TS2307: Cannot find module 'undici-types' or its corresponding type declarations.

56     type LibEmptyOrEventSource = LibDomIsLoaded extends true ? {} : import("undici-types").EventSource;
                                                                              ~~~~~~~~~~~~~~

../../../../../.cache/.bun/install/cache/links/undici-types@8.9.0-c15ec53fa9265eb7/node_modules/undici-types/content-type.d.ts:1:23 - error TS2688: Cannot find type definition file for 'node'.

1 /// <reference types="node" />
                        ~~~~

../../../../../.cache/.bun/install/cache/links/undici-types@8.9.0-c15ec53fa9265eb7/node_modules/undici-types/cookies.d.ts:1:23 - error TS2688: Cannot find type definition file for 'node'.

1 /// <reference types="node" />
                        ~~~~

../../../../../.cache/.bun/install/cache/links/undici-types@8.9.0-c15ec53fa9265eb7/node_modules/undici-types/fetch.d.ts:3:23 - error TS2688: Cannot find type definition file for 'node'.

3 /// <reference types="node" />
                        ~~~~

../../../../../.cache/.bun/install/cache/links/undici-types@8.9.0-c15ec53fa9265eb7/node_modules/undici-types/formdata.d.ts:2:23 - error TS2688: Cannot find type definition file for 'node'.

2 /// <reference types="node" />
                        ~~~~

../../../../../.cache/.bun/install/cache/links/undici-types@8.9.0-c15ec53fa9265eb7/node_modules/undici-types/patch.d.ts:1:23 - error TS2688: Cannot find type definition file for 'node'.

1 /// <reference types="node" />
                        ~~~~

../../../../../.cache/.bun/install/cache/links/undici-types@8.9.0-c15ec53fa9265eb7/node_modules/undici-types/websocket.d.ts:1:23 - error TS2688: Cannot find type definition file for 'node'.

1 /// <reference types="node" />
                        ~~~~


Found 13 errors in 9 files.

Errors  Files
     1  ../../../../../.cache/.bun/install/cache/links/bun-types@1.4.2-f5499e5b4c2fb4dd/node_modules/bun-types/bun.d.ts:688
     5  ../../../../../.cache/.bun/install/cache/links/bun-types@1.4.2-f5499e5b4c2fb4dd/node_modules/bun-types/fetch.d.ts:24
     1  ../../../../../.cache/.bun/install/cache/links/bun-types@1.4.2-f5499e5b4c2fb4dd/node_modules/bun-types/globals.d.ts:56
     1  ../../../../../.cache/.bun/install/cache/links/undici-types@8.9.0-c15ec53fa9265eb7/node_modules/undici-types/content-type.d.ts:1
     1  ../../../../../.cache/.bun/install/cache/links/undici-types@8.9.0-c15ec53fa9265eb7/node_modules/undici-types/cookies.d.ts:1
     1  ../../../../../.cache/.bun/install/cache/links/undici-types@8.9.0-c15ec53fa9265eb7/node_modules/undici-types/fetch.d.ts:3
     1  ../../../../../.cache/.bun/install/cache/links/undici-types@8.9.0-c15ec53fa9265eb7/node_modules/undici-types/formdata.d.ts:2
     1  ../../../../../.cache/.bun/install/cache/links/undici-types@8.9.0-c15ec53fa9265eb7/node_modules/undici-types/patch.d.ts:1
     1  ../../../../../.cache/.bun/install/cache/links/undici-types@8.9.0-c15ec53fa9265eb7/node_modules/undici-types/websocket.d.ts:1
```

## Workaround

Each project can workaround this by patching `undici-types` to declare its dependency on `node` types:

```diff
diff --git a/package.json b/package.json
index a5e7d9de2b58b575a07baf0929663210232e9429..9f169be8142d91b712379d0488dd24162811c481 100644
--- a/package.json
+++ b/package.json
@@ -15,6 +15,9 @@
   "files": [
     "*.d.ts"
   ],
+  "dependencies": {
+    "node": "*"
+  },
   "contributors": [
     {
       "name": "Daniele Belardi",
```

However:

- This is not a scalable approach.
- This is not possible if there are multiple versions of `undici-types` — which can happen easily because `bun` often [fails to deduplicate dependencies](https://github.com/oven-sh/bun/issues/1343).
- This causes `bun install` to materialize full copies of both `bun-types` and
  `undici-types` in `./node_modules/.bun`. This results in an extra 516 files
  and 5.7MB for the `./node_modules` folder, compared to only a few KB for a
  handful of symlinks. This negates a bunch of the benefit of using the global store in the first place.

See the `workaround` brach to see this in action.
