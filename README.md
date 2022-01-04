# Serialize Workflow action

A GitHub Action to serialize workflow runs.

This action will poll the currently `queued` and `in_progress` workflow
runs. If there are one or more workflow runs whose *run number* is less than the
current run number, the actions will wait.

Requires `bash`, `gh` and `jq`, which should be all present on a GitHub hosted runner.

## Motivation

Implementing serialized workflow runs is not possible using
[`concurrency`][concurrency] since it will cancel any previously pending run as
new runs are queued. Sometimes you wish to run every queued workflow run,
rather than just the most recently queued one.

## Billing

Since this action is polling, a workflow run will be billed even if it is
waiting for its turn to run. This may be expensive if you have many or long
running jobs.

## Inputs

### `github-token`

A GitHub access token to perform API calls.
It must have the following permissions:

* `actions`: `read`

### `workflow`

**Optional**: The workflow ID or filename, if omitted will use the currently running workflow.

### `run`

**Optional**: The workflow run number, defaults to the current run number.

## Outputs

Presently, none.

## Example usage

```yaml
jobs:
  main:
    runs-on: ubuntu-latest
    permissions:
      actions: read
    timeout-minutes: 10  # recommended that you set an overall job timeout
    steps:
    - uses: jsok/serialize-workflow-action@v1
      with:
        github-token: ${{ secrets.GITHUB_TOKEN }}
```

[concurrency]: https://docs.github.com/en/actions/learn-github-actions/workflow-syntax-for-github-actions#concurrency
