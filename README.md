This repo demonstrates inconsistent export map behavior in Node when a package is inside `node_modules`
versus when it is not.

`import "my-package"` (the name of the root project) resolves to `dist/esm/index.js` which tries to `import "my-package/foo.js"`.
That should resolve to `dist/esm/foo.js`, but the presence of `dist/esm/package.json` with `{"type": "module"}`
seems to break it.

However, with this exact same package structure in `node_modules/my-package2`, `import "my-package2"` doesn't
have any errors - `node_modules/my-package2/dist/esm/package.json` doesn't seem to break the resolution in this
case.
