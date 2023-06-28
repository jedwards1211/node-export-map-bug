This repo demonstrates inconsistent export map behavior in Node when a package is inside `node_modules`
versus when it is not.

`import "my-package"` (the name of the root project) resolves to `dist/esm/index.js` which tries to `import "my-package/foo.js"`.
That should resolve to `dist/esm/foo.js`, but the presence of `dist/esm/package.json` with `{"type": "module"}`
seems to break it.

However, with this exact same package structure in `node_modules/my-package2`, `import "my-package2"` doesn't
have any errors - `node_modules/my-package2/dist/esm/package.json` doesn't seem to break the resolution in this
case.

## Running

**Do not `npm install`!** it would overwrite the `node_modules` directory I've set up to repro.
Just run `npm test`:

```
> test
> ./test

+ echo package in local directory
package in local directory
+ node -e 'require("my-package")'
hello from /Users/andy/gh/node-export-map-bug/dist/cjs/index.js
hello from /Users/andy/gh/node-export-map-bug/dist/cjs/foo.js
+ node -e 'import("my-package")'
node:internal/errors:496
    ErrorCaptureStackTrace(err);
    ^

Error [ERR_MODULE_NOT_FOUND]: Cannot find package 'my-package' imported from /Users/andy/gh/node-export-map-bug/dist/esm/index.js
    at new NodeError (node:internal/errors:405:5)
    at packageResolve (node:internal/modules/esm/resolve:781:9)
    at moduleResolve (node:internal/modules/esm/resolve:830:20)
    at defaultResolve (node:internal/modules/esm/resolve:1035:11)
    at DefaultModuleLoader.resolve (node:internal/modules/esm/loader:269:12)
    at DefaultModuleLoader.getModuleJob (node:internal/modules/esm/loader:153:32)
    at ModuleWrap.<anonymous> (node:internal/modules/esm/module_job:76:33)
    at link (node:internal/modules/esm/module_job:75:36) {
  code: 'ERR_MODULE_NOT_FOUND'
}

Node.js v20.3.0
+ echo equivalent package in node_modules
equivalent package in node_modules
+ node -e 'require("my-package2")'
hello from /Users/andy/gh/node-export-map-bug/node_modules/my-package2/dist/cjs/index.js
hello from /Users/andy/gh/node-export-map-bug/node_modules/my-package2/dist/cjs/foo.js
+ node -e 'import("my-package2")'
hello from file:///Users/andy/gh/node-export-map-bug/node_modules/my-package2/dist/esm/foo.js
hello from file:///Users/andy/gh/node-export-map-bug/node_modules/my-package2/dist/esm/index.js
```
