# pnpm and nextjs standalone bug

cloned from https://github.com/vercel/turborepo/tree/main/examples/with-pnpm

## How to reproduce the bug

run the build command twice to reproduce the bug `pnpm turbo run build --filter=web --force`

```
❯ pnpm turbo run build --filter=web --force
• Packages in scope: web
• Running build in 1 packages
web:build: cache bypass, force executing f9fd0723add3773b
web:build:
web:build: > web@1.0.0 build /Users/ducnguyen/Documents/code/pnpm-test/apps/web
web:build: > next build
web:build:
web:build: warn  - You have enabled experimental feature(s).
web:build: warn  - Experimental features are not covered by semver, and may cause unexpected or broken application behavior. Use them at your own risk.
web:build:
web:build: info  - Checking validity of types...
web:build: info  - Creating an optimized production build...
web:build: info  - Compiled successfully
web:build: info  - Collecting page data...
web:build: symlink ../../../node_modules/.pnpm/react@18.1.0/node_modules/react
web:build: symlink ../../../node_modules/.pnpm/next@12.1.6_ef5jwxihqo6n7gxfmzogljlgcm/node_modules/next
web:build: symlink ../../../@next+env@12.1.6/node_modules/@next/env
web:build: symlink ../../react@18.1.0/node_modules/react
web:build:
web:build: > Build error occurred
web:build: [Error: EEXIST: file already exists, symlink '../../../@next+env@12.1.6/node_modules/@next/env' -> '/Users/ducnguyen/Documents/code/pnpm-test/apps/web/node_modules/.pnpm/next@12.1.6_ef5jwxihqo6n7gxfmzogljlgcm/node_modules/@next/env'] {
web:build:   errno: -17,
web:build:   code: 'EEXIST',
web:build:   syscall: 'symlink',
web:build:   path: '../../../@next+env@12.1.6/node_modules/@next/env',
web:build:   dest: '/Users/ducnguyen/Documents/code/pnpm-test/apps/web/node_modules/.pnpm/next@12.1.6_ef5jwxihqo6n7gxfmzogljlgcm/node_modules/@next/env'
web:build: }
web:build:  ELIFECYCLE  Command failed with exit code 1.
web:build: Error: command finished with error: command (apps/web) pnpm run build exited (1)
command (apps/web) pnpm run build exited (1)

 Tasks:    0 successful, 1 total
Cached:    0 cached, 1 total
  Time:    4.319s
```
