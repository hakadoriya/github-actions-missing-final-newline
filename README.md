# final-newline-checker

A workflow to check missing final newline.

## example

```yml
name: example

on:
  pull_request:

jobs:
  missing-final-newline:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      pull-requests: write
    steps:
      - uses: hakadoriya-actions/final-newline-checker@v0.0.1
        id: missing-final-newline
        with:
          # NOTE: If you want to fail on missing final newline, set this to true
          #fail-on-missing: true
          paths-ignore: |-
            ^README.md$
      - name: Submit PR comment if missing final newline
        if: ${{ steps.missing-final-newline.outputs.missing == 'true' }}
        env:
          GH_TOKEN: ${{ github.token }}
        run: |
          # Create comment body
          cat <<EOF | perl -pe 's/\\n/\n/g' | tee /tmp/gh-pr-comment-body.md
          ## 🚨 Missing final newline

          The following files are missing final newline.

          \`\`\`
          ${{ steps.missing-final-newline.outputs.missing-files }}
          \`\`\`
          EOF
          # Submit PR comment
          gh pr comment ${{ github.event.pull_request.number }} --body-file /tmp/gh-pr-comment-body.md
          # fail the workflow
          exit 1
```
