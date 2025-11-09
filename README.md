# Vertex AI PR Summarizer

This GitHub Action uses Google's Vertex AI to automatically generate PR summaries and test requirements for your pull requests.

## Features

- 📝 **Automatic PR Summary**: Generates a concise summary of changes (within 200 characters)
- 🔄 **Change Analysis**: Lists detailed changes with impact and review priority ratings
- ✅ **Test Requirements**: Suggests test cases in checkbox format
- 🎯 **Flexible Output**: Post as a PR comment or update the PR description
- 🤖 **Multi-Model Support**: Works with both Gemini and Claude models

## Usage

### Basic Setup

1. **Create a workflow file** in your repository (e.g., `.github/workflows/pr-summarize.yml`):

```yaml
name: PR Summary Generator

on:
  pull_request:
    types: [opened, synchronize]

jobs:
  summarize:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      pull-requests: write
    steps:
      - name: Generate PR Summary
        uses: Kazuya4395/vertex-api-pr-summarize@v1
        with:
          github-token: ${{ secrets.GITHUB_TOKEN }}
          gcp-project-id: ${{ secrets.GCP_PROJECT_ID }}
          gcp-credentials: ${{ secrets.GCP_CREDENTIALS }}
```

2. **Set up secrets** in your repository settings (`Settings` > `Secrets and variables` > `Actions`):
   - `GCP_PROJECT_ID`: Your Google Cloud Project ID
   - `GCP_CREDENTIALS`: The JSON content of your GCP service account key

### Advanced Configuration

```yaml
- name: Generate PR Summary
  uses: Kazuya4395/vertex-api-pr-summarize@v1
  with:
    github-token: ${{ secrets.GITHUB_TOKEN }}
    gcp-project-id: ${{ secrets.GCP_PROJECT_ID }}
    gcp-credentials: ${{ secrets.GCP_CREDENTIALS }}
    gcp-location: "us-east5" # Optional: defaults to 'us-east5'
    model: "gemini-2.5-flash" # Optional: defaults to 'gemini-2.5-flash'
    comment-location: "summary" # Optional: 'comment' or 'summary' (defaults to 'comment')
    system-prompt-path: ".github/prompts/custom-prompt.md" # Optional
    diff-size-limit: "200000" # Optional: defaults to '100000'
    timeout: "180000" # Optional: defaults to '120000' (2 minutes)
```

## Action Inputs

| Input                | Description                                                                         | Default            | Required |
| -------------------- | ----------------------------------------------------------------------------------- | ------------------ | -------- |
| `github-token`       | The GITHUB_TOKEN secret                                                             | N/A                | Yes      |
| `gcp-project-id`     | Your Google Cloud Project ID                                                        | N/A                | Yes      |
| `gcp-credentials`    | The JSON content of your GCP service account key                                    | N/A                | Yes      |
| `gcp-location`       | The Google Cloud region for your project                                            | `us-east5`         | No       |
| `model`              | The Vertex AI model to use (e.g., `gemini-2.5-flash`, `claude-sonnet-4-5@20250929`) | `gemini-2.5-flash` | No       |
| `comment-location`   | Where to post the summary: `comment` (PR comment) or `summary` (PR description)     | `comment`          | No       |
| `system-prompt-path` | Path to a custom system prompt file                                                 | Built-in prompt    | No       |
| `diff-size-limit`    | Maximum diff size (in bytes) to process                                             | `100000`           | No       |
| `timeout`            | Timeout (in milliseconds) for the Vertex AI API call                                | `120000`           | No       |

## Output Format

The generated summary follows this structure:

```markdown
## 📝 概要

[Concise summary of changes within 200 characters]

## 🔄 変更点

- **[Change description]**
  - 影響度: 🔴大 / 🟡中 / 🟢小
  - レビュー重要度: 🔴大 / 🟡中 / 🟢小
  - 説明: [Detailed explanation]

## ✅ テスト要件

- [ ] [Test case 1]
- [ ] [Test case 2]
- [ ] [Test case 3]
```

### Impact Level Criteria

- 🔴 **High**: System-wide impact, database schema changes, API specification changes
- 🟡 **Medium**: Multiple modules/features affected, behavior changes
- 🟢 **Low**: Single module changes, refactoring, documentation updates

### Review Priority Criteria

- 🔴 **High**: Security-related, data integrity, significant performance impact
- 🟡 **Medium**: Logic changes, error handling, important feature additions
- 🟢 **Low**: Code style, minor refactoring, logging additions

## Custom Prompts

You can customize the AI's behavior by providing a custom prompt file. Create a markdown file (e.g., `.github/prompts/custom-prompt.md`) and reference it in the `system-prompt-path` input.

See the [default prompt](./prompts/summarize-prompt.md) for reference.

## Available Models

### Gemini Models

- `gemini-2.5-flash` (recommended)
- `gemini-2.0-flash-exp`
- `gemini-1.5-pro`
- `gemini-1.5-flash`

### Claude Models

Note: Claude models are only available in specific regions like `us-east5`.

- `claude-sonnet-4-5@20250929`
- `claude-3-5-sonnet@20240620`
- `claude-3-opus@20240229`

See [Vertex AI documentation](https://cloud.google.com/vertex-ai/generative-ai/docs/learn/model-versions) for the latest available models.

## Development

1. Clone the repository
2. Install dependencies: `npm install`
3. Run tests: `npm test`
4. Build the project: `npm run build`

## License

MIT

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request.
