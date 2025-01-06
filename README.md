# GitHub Actions Missing Final Newline

A workflow to check missing final newline.

## example

```yml
name: example

on:
  pull_request:
    # NO paths filter

jobs:
  missing-final-newline:
    runs-on: ubuntu-latest
    outputs:
      missing: ${{ steps.missing-final-newline.outputs.missing }}
      missing-files: ${{ steps.missing-final-newline.outputs.missing-files }}
    steps:
      - uses: hakadoriya/github-actions-missing-final-newline@main
        id: missing-final-newline
        with:
          # NOTE: If you want to fail on missing final newline, set this to true
          #fail-on-missing: true
          paths: |-
            ^action.yml
            ^missing-final-newline.md
  # > Note: A job that is skipped will report its status as "Success".
  # > It will not prevent a pull request from merging, even if it is a required check.
  # ref. https://docs.github.com/en/actions/using-jobs/using-conditions-to-control-job-execution#overview
  missing:
    runs-on: ubuntu-latest
    needs: missing-final-newline
    if: ${{ needs.missing-final-newline.outputs.missing == 'true' }}
    steps:
      - name: "missing final newline detected"
        run: |
            echo "needs.missing-final-newline.outputs.missing-files: ${{ needs.missing-final-newline.outputs.missing-files }}"
```
