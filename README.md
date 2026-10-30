# what's true? GitHub Action

Runs [what's true?](https://true.krnl.studio) on pull requests and on the default branch: it builds the docs
manifest if your build did not, pushes it, and runs the guard, with each finding on its line in the PR. The job fails
on block findings. what's true? checks for drift and risky specifics. It is not legal review.

```yaml
# .github/workflows/whatstrue.yml
name: what's true?
on:
  pull_request:
  push:
    branches: [main]
jobs:
  docs:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          # Check the PR's own commit, not the merge commit GitHub makes.
          ref: ${{ github.event.pull_request.head.sha || github.sha }}
      # Manifest mode: run your own build here, writing whatstrue/manifest.json.
      - uses: rohit3a/whatstrue-action@v1
        with:
          token: ${{ secrets.WHATSTRUE_TOKEN }}
```

In Markdown mode what's true? reads the docs from the repo itself, so leave out `token` and the Action only checks.

| Input | Default | |
| --- | --- | --- |
| `token` | | A product token with the write scope. Without one, nothing is pushed. |
| `config` | `whatstrue/config.yaml` | |
| `manifest` | `whatstrue/manifest.json` | Your build's manifest. If it is not there, it is built from the Markdown docs. |
| `check` | `true` | Run the guard, and fail on block findings. |
| `push` | `auto` | Push when a token is set. |
| `origin` | `https://true.krnl.studio` | |
| `working-directory` | `.` | |

Outputs: `blocking`, `warnings` and `commit`.

The Action is under the MIT licence. `dist/THIRD_PARTY_NOTICES.txt` lists the packages it bundles, with their licences.
