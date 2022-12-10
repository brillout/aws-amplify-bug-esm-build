```bash
git clone git@github.com:brillout/aws-amplify-bug-esm-build
cd aws-amplify-bug-esm-build/
npm install # or pnpm
node test.mjs
```

Same as single line (copy-paste me):

```shell
git clone git@github.com:brillout/aws-amplify-bug-esm-build && cd aws-amplify-bug-esm-build/ && npm install && node test.mjs
```

Throws:

```
(node:30335) Warning: To load an ES module, set "type": "module" in the package.json or use the .mjs extension.
(Use `node --trace-warnings ...` to show where the warning was created)
/home/romuuu/tmp/aws-amplify-bug-esm-build/node_modules/.pnpm/@aws-amplify+ui-react@4.2.0_qsen2ibz3ktiygtazonh2f6haa/node_modules/@aws-amplify/ui-react/dist/esm/index.js:1
SyntaxError: Cannot use import statement outside a module
    at Object.compileFunction (node:vm:352:18)
    at wrapSafe (node:internal/modules/cjs/loader:1033:15)
    at Module._compile (node:internal/modules/cjs/loader:1069:27)
    at Module._extensions..js (node:internal/modules/cjs/loader:1159:10)
    at Module.load (node:internal/modules/cjs/loader:981:32)
    at Module._load (node:internal/modules/cjs/loader:827:12)
    at ModuleWrap.<anonymous> (node:internal/modules/esm/translators:170:29)
    at ModuleJob.run (node:internal/modules/esm/module_job:198:25)
    at async Promise.all (index 0)
    at async ESMLoader.import (node:internal/modules/esm/loader:409:24)

Node.js v18.0.0
```

The fix is to set `"type": "module"` in `node_modules/@aws-amplify/ui-react/package.json`, but then another error is thrown:


```
node:internal/process/esm_loader:91
    internalBinding('errors').triggerUncaughtException(
                              ^

Error [ERR_MODULE_NOT_FOUND]: Cannot find module '/home/romuuu/tmp/aws-amplify-bug-esm-build/node_modules/.pnpm/@aws-amplify+ui-react@4.2.0_qsen2ibz3ktiygtazonh2f6haa/node_modules/lodash/debounce' imported from /home/romuuu/tmp/aws-amplify-bug-esm-build/node_modules/.pnpm/@aws-amplify+ui-react@4.2.0_qsen2ibz3ktiygtazonh2f6haa/node_modules/@aws-amplify/ui-react/dist/esm/primitives/Collection/Collection.js
Did you mean to import lodash@4.17.21/node_modules/lodash/debounce.js?
    at new NodeError (node:internal/errors:372:5)
    at finalizeResolution (node:internal/modules/esm/resolve:405:11)
    at moduleResolve (node:internal/modules/esm/resolve:966:10)
    at defaultResolve (node:internal/modules/esm/resolve:1176:11)
    at ESMLoader.resolve (node:internal/modules/esm/loader:605:30)
    at ESMLoader.getModuleJob (node:internal/modules/esm/loader:318:18)
    at ModuleWrap.<anonymous> (node:internal/modules/esm/module_job:80:40)
    at link (node:internal/modules/esm/module_job:78:36) {
  code: 'ERR_MODULE_NOT_FOUND'
}

Node.js v18.0.0
```

The problem here is that `node_modules/@aws-amplify/ui-react/dist/esm/primitives/Collection/Collection.js` contains `import a from"lodash/debounce"` but it should be `import a from"lodash/debounce.js"` instead. (If you change that line, you'll see the error go away but another similar error is thrown then.)
