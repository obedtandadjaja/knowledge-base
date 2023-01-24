# TS Monorepo

Monorepo makes it easy for multiple projects to share the same library and utility classes. It also reduces the amount of repo and packages that we have to maintain to make code sharable. Other benefits:

- Dependencies can be easily tracked
- Discoverability
- Improved collaboration
- Standardization and simplified organization

Monorepo aren't strictly superiod to manyrepos. They're not strictly worse, either. Know exactly why and when to use them in your project.

## Options

1. Yarn workspaces.
1. Lerna
1. Nx
1. Turborepo

## Yarn workspaces

TL;DR have a root `package.json` that will specify the other nested `package.json` underneath. It dedupes the `node_modules` and only generates once for a particular package.

Learn more: https://classic.yarnpkg.com/lang/en/docs/workspaces/

Yarn workspaces is one of the core technology that other monorepo technology uses. Especially for those dealing with Javascript/Typescript.

## Lerna

Javascript monorepo: https://lerna.js.org/

Makes it easy to house and publish multiple Javascript packages. However, it does not handle:

1. Long build times
1. Long install times

Nx has taken over stewardship of Lerna (https://dev.to/nrwl/lerna-is-dead-long-live-lerna-3jal).

## Nx

Smart build system by building a dependency tree to not execute build on everything.

Mental model: https://nx.dev/concepts/mental-model

Created by ex-Googlers. Inspired by Blaze/Bazel. Criticized for being too complicated for the average user.

- Built in Typescript
- Has support for storing caches remotely so that other users don't have to build the entire app. Hosted by Nx Cloud
- Supports everything that Turborepo supports
- Supports both integrated repo and package-based repo
  - Integrated repo
    - Projects depend on each other through standard import statements.
    - Requires a lot more work to setup since build tools like Jest or Webpack need to be wrapped in order to work properly
    - More efficient and easy to maintain in the long term.
  - Package-based repo
    - Collection of packages that depend on each other via `package.json` and nested `node_modules`.
    - No change needed to build tooling.
- Does better job at build caching compared to Turborepo. A little bit more clever in know what files have been restored
- Supports distributed task execution. Run different build commands in multiple machines. Not supported in Turborepo
- Based on Nx's benchmark it is faster than Turborepo by 9.4x.

https://github.com/nrwl/nx

## Turborepo

Slimmer version of Nx. Praised for being simpler than Nx.

- Built in Go
- Has support for remote cache. Hosted by Vercel.
- Does not have all the fancy features. Just enough to make it work
- Supports only package-based repo.

https://turbo.build/