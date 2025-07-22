This is the repository with the main CI file which is used by all projects which
needs to deploy.

The CI uses org secrets to access the [vault](https://vault.esek.se).

## Example usage

```yaml
name: Docker Image CI

on:
    push:
        branches: [main]
    pull_request:
        types: [opened, closed, reopened, synchronize]
    release:
        types: [released, prereleased]

jobs:
    main:
        uses: esek/workflows/.github/workflows/main.yaml@main
        secrets: inherit
        with:
            disable_testing: true
            only_prod: true
```

### "with" flags

- `disable_testing`: should be enabled for every project but "ekorre" or
  "efterphest" (yes IK this should be an opt-in flag for testing instead)
- `extended_testing`: switches on "ekorre" testing instead of "efterphest"
  testing
- `only_prod`: don't push to staging & testing on non-release updates, deploy
  directly to `DEPLOY_URL`. It's also recommended to comment out `pull_request`
  & `release` from `on:` in the yaml.
- `unlimited_service_deploy`: some `docker-compose.ci.yml` files expect the
  `docker compose up` to be ran as
  `docker compose up <project (or well, technically docker service) name, e.g. ekorre>`.
  Others don't expect the project name. Projects which do not expect the project
  name need this flag. If the build fails at `docker compose up`, try changing
  this flag.
- `fe_and_be`: release two containers instead of one: `<container>-be` built in
  `./backend` and `<container>-fe` built in `./frontend`. Used for projects with
  separate backend- and frontend containers.
- `*_quirk`: small changes which make a specific project work. It's ugly, I know
  😥
