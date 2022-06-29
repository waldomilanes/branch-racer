
## Inputs

### `GITHUB_TOKEN`

**Required** The token to be used for creating the pull request. Can be set to the one given for the workflow or another user.

### `FROM_BRANCH`

**Required** The branch you want to make the pull request from.

### `TO_BRANCH`

**Required** The branch you want to make the pull request to.

### `PULL_REQUEST_TITLE`

What you would like as the title of the pull request.

Default: `sync: {FROM_BRANCH} to {TO_BRANCH}`

### `PULL_REQUEST_BODY`

What you would like in the body of the pull request.

Default: `sync-branches: New code has just landed in {FROM_BRANCH} so let's bring {TO_BRANCH} up to speed!`

### `PULL_REQUEST_IS_DRAFT`

Set to `true` for the pull request to be opened as a draft.

Default: `false`

### `CONTENT_COMPARISON`

Set to `true` to force checking content comparison between branches.
No more empty pull requests being opened and triggering CI jobs.

Default: `false`
### `REVIEWERS`

JSON array of GitHub user `login`s that will be requested to review the PR. Example: '['reviewer1']'

Default: `[]`
### `TEAM_REVIEWERS`

JSON array of GitHub team `slug`s that will be requested to review the PR. Example: '['js-team']'

Default: `[]`

## Outputs

### `PULL_REQUEST_URL`

Set to the URL of either the pull request that was opened by this action or the one that was found to already be open between the two branches

### `PULL_REQUEST_NUMBER`

Pull request number from generated pull request or the currently open one

## Example usage

```YML
name: update-transition-to-master
on:
  push:
    branches:
      - main
      - develop
jobs:
  sync-branches:
    runs-on: ubuntu-latest
    name: Syncing branches
    steps:
      - name: Checkout
        uses: actions/checkout@v3
        with:
          fetch-depth: 2
      - name: Set up Node
        uses: actions/setup-node@v1
        with:
          node-version: 16
      - name: sync with master
        id: pull
        uses: waldomilanes/branch-racer@1.0.0
        with:
          GITHUB_TOKEN: ${{secrets.GITHUB_TOKEN}}
          FROM_BRANCH: "master"
          TO_BRANCH: "feature/lightspeed-transition"
          PULL_REQUEST_TITLE: "[CAT-10308] There is new code on the master branch, let's update lightspeed-transition"
          CONTENT_COMPARISON: true
      - name: second step
        uses: waldomilanes/branch-racer@1.0.0
        with:
          GITHUB_TOKEN: ${{secrets.GITHUB_TOKEN}}
          FROM_BRANCH: "feature/lightspeed-transition"
          TO_BRANCH: "master"
          PULL_REQUEST_TITLE: "[CAT-10308] Hey! It's time to merge new code from lightspeed-transition -> master"
          CONTENT_COMPARISON: true
```
