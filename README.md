# lgtm-ai-action

GitHub Action to run [lgtm-ai](https://github.com/elementsinteractive/lgtm-ai).

This action can be used to perform automatic code-reviews or write reviewer guides using LLMs, thanks to lgtm-ai.

## Usage

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `ai-api-key` | API key for AI service (OpenAI, Anthropic, Google, etc.) | ✅ | - |
| `git-api-key` | API key for GitHub (you can use GITHUB_TOKEN) | ✅ | - |
| `pr-number` | Pull request number to review | ✅ | - |
| `model` | AI model to use (e.g. gpt-4o, claude-3-5-sonnet-latest, gemini-2.0-flash) | ❌ | *Uses config file or tool default* |
| `version` | LGTM AI version (latest, v0.7.2, etc.) | ❌ | `latest` |
| `publish` | Whether to publish the review as PR comments | ❌ | `true` |
| `exclude` | File patterns to exclude (e.g. '*.md *.json package-lock.json') | ❌ | `""` (none) |
| `config` | Path to lgtm.toml configuration file (e.g. '.lgtm.toml') | ❌ | `""` (none) |
| `verbose` | Enable extra verbose output (-vv instead of -v) | ❌ | `false` |

### Quick Usage

```yaml
- name: AI Code Review
  uses: elementsinteractive/lgtm-ai-action@v1
  with:
    ai-api-key: ${{ secrets.AI_API_KEY }}
    git-api-key: ${{ secrets.GITHUB_TOKEN }}
    model: 'gpt-5'
    pr-number: ${{ github.event.issue.number }}
```

### Full Workflow Example

This simple workflow will perform a review whenever someone comments `/lgtm review` in a pull request.

```yaml
name: LGTM Review

on:
  issue_comment:
    types: [created]

jobs:
  lgtm-review:
    needs: check-permission
    if: |
      github.event.issue.pull_request &&
      startsWith(github.event.comment.body, '/lgtm review')
    runs-on: ubuntu-latest
    steps:
      - name: Checkout PR code
        uses: actions/checkout@v4
        with:
          ref: refs/pull/${{ github.event.issue.number }}/merge

      - name: Run LGTM Review
        uses: elementsinteractive/lgtm-ai-action@v1
        with:
          ai-api-key: ${{ secrets.AI_API_TOKEN }}
          git-api-key: ${{ secrets.GITHUB_TOKEN }}
          pr-number: ${{ github.event.issue.number }}
          model: 'gpt-5'
```

> [!TIP]
> The action will autodiscover any `lgtm.toml` file in your repository so you can 
> fully configure `lgtm-ai`. See the [configuration documentation](https://github.com/elementsinteractive/lgtm-ai?tab=readme-ov-file#configuration).
