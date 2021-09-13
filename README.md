# testing-action-deploy

Shared Github Actions for ioBroker testing workflows: Deploy step

## Inputs

| Input                       | Description                                                                                | Required?             |            Default            |
| --------------------------- | ------------------------------------------------------------------------------------------ | --------------------- | :---------------------------: |
| `node-version`              | Node.js version to use in tests. Should be LTS.                                            | ✔                     |               -               |
| `install-command`           | Overwrite the default install command                                                      | ❌                    |          `'npm ci'`           |
| `build`                     | Set to `'true'` when the adapter needs a build step before testing                         | ❌                    |            `false`            |
| `build-command`             | Overwrite the default build command                                                        | ❌                    |       `'npm run build'`       |
| `npm-token`                 | The token to use to publish to npm                                                         | ✔                     |               -               |
| `github-token`              | The token to use to create a GitHub release                                                | ✔                     |               -               |

If Sentry integration is desired, the following inputs are used to configure it:

| Input                       | Description                                                                                | Required?             |            Default            |
| --------------------------- | ------------------------------------------------------------------------------------------ | --------------------- | :---------------------------: |
| `sentry`                    | Set to `'true'` to enable Sentry releases integration                                      | ❌                    |            `false`            |
| `sentry-token`              | The token to use to create a Sentry release                                                | if `sentry` is `true` |               -               |
| `sentry-url`                | Under which URL the Sentry instance is running                                             | ❌                    | `https://sentry.iobroker.net` |
| `sentry-org`                | Which Sentry organization the project is under                                             | ❌                    |          `iobroker`           |
| `sentry-project`            | The project name on Sentry                                                                 | if `sentry` is `true` |               -               |
| `sentry-version-prefix`     | The prefix for release versions on Sentry. Should be something like `iobroker.adaptername` | if `sentry` is `true` |               -               |
| `sentry-github-integration` | Set to true once Github integration is set up in Sentry                                    | ❌                    |            `false`            |
| `sentry-sourcemap-paths`    | If sourcemaps should be uploaded to Sentry, specify their path here                        | ❌                    |               -               |

## Usage

```yml
# ... rest of your workflow ...

jobs:
  deploy:
    needs: [check-and-lint, adapter-tests]

    # Trigger this step only when a commit on any branch is tagged with a version number
    if: |
      contains(github.event.head_commit.message, '[skip ci]') == false &&
      github.event_name == 'push' &&
      startsWith(github.ref, 'refs/tags/v')

    runs-on: ubuntu-latest

    steps:
      - uses: ioBroker/testing-action-deploy@v1
        with:
          node-version: "14.x" # This should be LTS
          # build: 'true' # optional
          npm-token: ${{ secrets.NPM_TOKEN }}
          github-token: ${{ secrets.GITHUB_TOKEN }}
          # If you want Sentry:
          sentry-token: ${{ secrets.SENTRY_AUTH_TOKEN }}
          sentry-project: "iobroker-my-adapter"
          sentry-version-prefix: "iobroker.my-adapter"
          # ... other options
```
