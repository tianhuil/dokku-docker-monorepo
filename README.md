# dokku-docker-monorepo
Robust dokku monorepo using Dockerfile.

## Install
```bash
dokku plugin:install https://github.com/tianhuil/dokku-docker-monorepo
```

## Usage
Setup your file structure like so

```
$ ls
.dokku-docker-monorepo
Dockerfile.app1
Dockerfile.app2
Dockerfile.app2-staging
...
```

The file `.dokku-docker-monorepo` can be empty and its presence activates the plugin.

```
$ git remote -v
app1            dokku@dokku.me:something
app2            dokku@dokku.me:something-else
app2-staging    dokku@dokku.me:something-else-staging
```

The suffix on `Dockerfile.*` must match the remote name.

## Comparison with other projects
The plugin [dokku-monorepo](https://github.com/notpushkin/dokku-monorepo) handle monorepos well and can handle native buildpacks.  We only handle `Dockerfile` builds.

However, it cannot handle a *sibling package dependencies* in monorepos (see below for details).  We can.  Given the prevelance of this use case and how easy it is to learn to build a monorepo, we believe this is a good tradeoff.

## Explanation
A *sibling package dependencies* is a dependency `packages/common` which is used by `packages/server-a` and `packages/server-b` and is a [common use case for monorepos](https://medium.com/@NiGhTTraX/how-to-set-up-a-typescript-monorepo-with-lerna-c6acda7d4559).  Fundamentally, dokku-monorepo replaces the pushed git folder (i.e. `.`) with just the package folder (`./packages/server-a`), so any sibling dependencies (e.g. `./packages/common`) are removed.

We take a different track, by copying the appropriate `Dockerfile` and running the monorepo from the root.
