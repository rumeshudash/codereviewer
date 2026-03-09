# AI Code Reviewer

AI Code Reviewer is a GitHub Action that uses **OpenAI (GPT)** or **Google Gemini** to review pull request diffs and post actionable comments. It helps improve code quality and saves time by automating the code review process.

## Features

- **Dual AI backends**: Use OpenAI (GPT-4, etc.) or Google Gemini — provide one API key and the action uses it.
- Actionable review comments with optional **inline code suggestions** (GitHub suggestion blocks).
- Exclude files via glob patterns (e.g. `**/*.json`, `**/*.md`).
- **Custom review rules** to align feedback with your project’s conventions.
- Runs on PR open and sync; only posts a review when there are concrete issues to report.

## Setup

1. **API key**: You need either an OpenAI API key or a Google Gemini API key (at least one).
   - OpenAI: sign up at [OpenAI](https://platform.openai.com/) and create an API key.
   - Gemini: get an API key from [Google AI Studio](https://aistudio.google.com/app/apikey).

2. Add the chosen key as a GitHub Secret:
   - For OpenAI: `OPENAI_API_KEY`
   - For Gemini: `GEMINI_API_KEY`  
   See [GitHub Encrypted Secrets](https://docs.github.com/en/actions/security-guides/encrypted-secrets).

3. Create a workflow (e.g. `.github/workflows/main.yml`):

```yaml
name: AI Code Reviewer

on:
  pull_request:
    types:
      - opened
      - synchronize
permissions: write-all
jobs:
  review:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Repo
        uses: actions/checkout@v4

      - name: AI Code Reviewer
        uses: rumeshudash/codereviewer@main
        with:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }} # The GITHUB_TOKEN is there by default so you just need to keep it like it is and not necessarily need to add it as secret as it will throw an error. [More Details](https://docs.github.com/en/actions/security-guides/automatic-token-authentication#about-the-github_token-secret)
          # Use OpenAI (set OPENAI_API_KEY secret)
          OPENAI_API_KEY: ${{ secrets.OPENAI_API_KEY }}
          OPENAI_API_MODEL: "gpt-4"  # optional, default: gpt-4
          # Or use Gemini (set GEMINI_API_KEY secret) — comment out OpenAI above
          # GEMINI_API_KEY: ${{ secrets.GEMINI_API_KEY }}
          # GEMINI_MODEL: "gemini-2.5-flash"  # optional, default: gemini-2.5-flash
          exclude: "**/*.json, **/*.md"  # optional: comma-separated globs
          custom_rules: "Check for proper error handling, Ensure functions are documented"  # optional
```

4. Replace `rumeshudash` with your GitHub username or org where this action repo lives. (Optional)

5. Set **either** `OPENAI_API_KEY` or `GEMINI_API_KEY` (and optionally adjust the model). If both are set, OpenAI is used.

6. Commit and push; the action will run on new and updated pull requests.

## How It Works

The action fetches the PR diff (on `opened` or `synchronize`), filters out files matching your `exclude` patterns, and sends the remaining diff to the chosen AI (OpenAI or Gemini). The model returns structured review items (file, line, comment, optional suggested code). The action posts these as PR review comments; when it can suggest a fix, it uses GitHub’s suggestion blocks so reviewers can apply changes with one click. If the AI finds no concrete issues, no review is posted.

### Custom Rules

Add `custom_rules` to steer the AI toward your project’s standards. Rules are appended to the review prompt.

```yaml
custom_rules: "Check for proper error handling, Ensure unit tests for new logic, Prefer descriptive variable names"
```

Use short, clear instructions; separate multiple rules with commas.

## Contributing

Contributions are welcome! Please feel free to submit issues or pull requests to improve the AI Code Reviewer GitHub
Action.

Let the maintainer generate the final package (`yarn build` & `yarn package`).

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for more information.
