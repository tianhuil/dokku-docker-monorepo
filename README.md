# dokku-docker-monorepo
Robust dokku monorepo using `Dockerfile`.  Easy to setup and configure.

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

The file `.dokku-docker-monorepo` can be empty: its presence activates the plugin.

```
$ git remote -v
foo            dokku@dokku.me:app1
bar            dokku@dokku.me:app2
baz            dokku@dokku.me:app2-staging
```

Note that the suffix on `Dockerfile.*` must match the remote app name.

## Comparison with other projects
The plugin [**dokku-monorepo**](https://github.com/notpushkin/dokku-monorepo) handles monorepos well and can handle native buildpacks.  **dokku-docker-monorepo** only handle `Dockerfile` builds.

However, dokku-monorepo cannot handle *sibling package dependencies* in monorepos (see below for details).  dokku-docker-monorepo can handle them.  Given the prevalence of this use case and how easy `Dockerfile`s are to manage, we believe this is a good tradeoff.

## Explanation
A *sibling package dependencies* is a dependency `packages/common` which is used by `packages/server-a` and `packages/server-b`.  It is a [common use case for monorepos](https://medium.com/@NiGhTTraX/how-to-set-up-a-typescript-monorepo-with-lerna-c6acda7d4559).  Fundamentally, dokku-monorepo replaces the pushed git folder (i.e. `./`) with just the package folder (`./packages/server-a`), so any sibling dependencies (e.g. `./packages/common`) are deleted.

We take a different strateguy, by copying the appropriate `Dockerfile.*` and running the monorepo from the root.
