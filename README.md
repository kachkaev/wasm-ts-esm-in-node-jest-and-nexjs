# WASM + TypeScript + ESM in Node.js, Jest and Next.js

This repo contains a web app that depends on an NPM package with [WASM](https://webassembly.org/), TypeScript and native ESModules.
WASM code is successfully used in a Node.js script, a Jest test and a Next.js app within a page and an API route.

The demo was created while working on [blockprotocol/blockprotocol](https://github.com/blockprotocol/blockprotocol) and [hashintel/hash](https://github.com/hashintel/hash).

## Prerequisites

- [Node.js](https://nodejs.org/en/) v16 or higher
- [Yarn](https://yarnpkg.com/)
- [Rust](https://www.rust-lang.org/tools/install) and [`wasm-pack`](https://rustwasm.github.io/wasm-pack/installer/) (unless you want to skip steps 2 and 3 of the demo)

## Demo steps

1.  Clone the repo and install dependencies from the root folder:

    ```sh
    yarn install
    ```

1.  _(can be skipped)_ Generate `wasm-package` from `wasm-crate`:

    ```sh
    cd wasm-crate
    wasm-pack build --out-dir ../wasm-package --target=bundler
    cd ..
    ```

1.  _(can be skipped)_ Tweak `wasm-package`:

    ```sh
    ## Enable native ESM in package.json
    yarn replace-in-file "/\"module\":/" "\"type\": \"module\", \"main\":" wasm-package/package.json --isRegex
    yarn prettier --write wasm-package/package.json

    ## Remove autogenerated .gitignore
    rm wasm-package/.gitignore
    ```

1.  🎉 Run a Node.js script that uses the `wasm-package`:

    ```sh
    yarn workspace web-app exe scripts/wasm-package-answer.ts
    ```

1.  🎉 Run unit tests ([patched](./.yarn/patches) Jest) referring to the `wasm-package`:

    ```sh
    yarn workspace web-app test
    ```

    For built-in support of WASM in Jest see [facebook/jest#11011 (comment)](https://github.com/facebook/jest/issues/11011#issuecomment-1278111232).

1.  🎉 Run Next.js dev server and check if `wasm-package` works there too:

    ```sh
    yarn workspace web-app dev
    ```

    Open [localhost:3000](http://localhost:3000) and [localhost:3000/api/wasm-package-answer](http://localhost:3000/api/wasm-package-answer) to see the result.

    ***

1.  ⚠️ Build and run production Next.js app:

    ```sh
    yarn workspace web-app build
    yarn workspace web-app start
    ```

    > ⚠️ I could not get WASM working in API routes.
    > Requesting [localhost:3000/api/wasm-package-answer](http://localhost:3000/api/wasm-package-answer) returns `Internal Server Error`.
    >
    > Upstream discussion: [vercel/next.js#29362 (comment)](https://github.com/vercel/next.js/issues/29362#issuecomment-1278868452).
