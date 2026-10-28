# GitHub Readme Stats Fast Action

[English](Readme.md) | [简体中文](README.zh-CN.md)

Generate GitHub profile cards as SVG files with GitHub Actions using [GitHub Readme Stats Fast](https://github.com/pranesh-2005/github-readme-stats-fast). Commit the generated files to your repository and embed them in your README—no self-hosted server required.

## Features

- Supports six card types: `stats`, `top-langs`, `streak`, `pin`, `wakatime`, and `gist`.
- Accepts card options as a query string or JSON.
- Lets you choose the output path for each SVG.
- Works with scheduled workflows or manual runs.
- Exposes the generated SVG path as an action output.

## Quick start

The example below is designed for your **GitHub profile repository**—the public repository whose name matches your GitHub username. It generates three cards and commits them to the repository.

Create `.github/workflows/readme-stats.yml`:

```yaml
name: Update README cards

on:
  schedule:
    - cron: "0 0 * * *"
  workflow_dispatch:

permissions:
  contents: write

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Check out repository
        uses: actions/checkout@v5

      - name: Generate stats card
        uses: zb479519891/github-readme-stats-fast-action@main
        with:
          card: stats
          options: username=${{ github.repository_owner }}&show_icons=true
          path: profile/stats.svg
          token: ${{ secrets.GITHUB_TOKEN }}

      - name: Generate top languages card
        uses: zb479519891/github-readme-stats-fast-action@main
        with:
          card: top-langs
          options: username=${{ github.repository_owner }}&layout=compact&langs_count=6
          path: profile/top-langs.svg
          token: ${{ secrets.GITHUB_TOKEN }}

      - name: Generate streak card
        uses: zb479519891/github-readme-stats-fast-action@main
        with:
          card: streak
          options: username=${{ github.repository_owner }}&layout=compact
          path: profile/streak.svg
          token: ${{ secrets.GITHUB_TOKEN }}

      - name: Commit generated cards
        run: |
          git config user.name "github-actions[bot]"
          git config user.email "41898282+github-actions[bot]@users.noreply.github.com"
          git add profile/*.svg
          git diff --cached --quiet && exit 0
          git commit -m "Update README cards"
          git push
```

**Important:**

- The workflow needs `permissions: contents: write` to commit generated SVG files.
- If the workflow cannot push changes, check **Settings → Actions → General → Workflow permissions** in your repository and allow read and write permissions.
- The schedule uses UTC. You can remove the `schedule` section if you prefer to run the workflow manually from the **Actions** tab.

## Display the cards in your README

Add the following Markdown to your profile repository's `README.md`. The leading `!` is required to render each SVG as an image rather than a text link.

```md
![GitHub stats](./profile/stats.svg)
![Top languages](./profile/top-langs.svg)
![GitHub streak](./profile/streak.svg)
```

To make an image clickable, wrap it in a link:

```md
[![Top languages](./profile/top-langs.svg)](./profile/top-langs.svg)
```

## Inputs

| Input | Required | Description |
| --- | --- | --- |
| `card` | Yes | Card type: `stats`, `top-langs`, `streak`, `pin`, `wakatime`, or `gist`. |
| `options` | No | Card options as a query string (for example, `key=value&...`) or a JSON object. Defaults to an empty string. |
| `path` | No | Output SVG file path. If omitted, the action uses its default path, `profile/<card>.svg`. |
| `token` | No | GitHub token passed to the renderer. Can be a `GITHUB_TOKEN` or a personal access token (PAT). Defaults to an empty string. |

The action exposes a `path` output containing the path of the generated SVG.

## Card examples

These examples show how to configure each supported card. Replace `octocat` and other sample values with your own details.

### Stats

```yaml
- name: Generate stats
  uses: zb479519891/github-readme-stats-fast-action@main
  with:
    card: stats
    options: username=octocat&show_icons=true&hide_rank=true&bg_color=0D1117
    path: profile/stats.svg
    token: ${{ secrets.GITHUB_TOKEN }}
```

### Top languages

```yaml
- name: Generate top languages
  uses: zb479519891/github-readme-stats-fast-action@main
  with:
    card: top-langs
    options: username=octocat&layout=compact&langs_count=6
    path: profile/top-langs.svg
    token: ${{ secrets.GITHUB_TOKEN }}
```

### Streak

```yaml
- name: Generate streak
  uses: zb479519891/github-readme-stats-fast-action@main
  with:
    card: streak
    options: username=octocat&layout=compact
    path: profile/streak.svg
    token: ${{ secrets.GITHUB_TOKEN }}
```

### Pinned repository

Set `repo` to the repository you want to feature.

```yaml
- name: Generate pinned repository card
  uses: zb479519891/github-readme-stats-fast-action@main
  with:
    card: pin
    options: username=octocat&repo=Hello-World
    path: profile/pin-hello-world.svg
    token: ${{ secrets.GITHUB_TOKEN }}
```

### WakaTime

```yaml
- name: Generate WakaTime card
  uses: zb479519891/github-readme-stats-fast-action@main
  with:
    card: wakatime
    options: username=octocat&layout=compact
    path: profile/wakatime.svg
    token: ${{ secrets.GITHUB_TOKEN }}
```

### Gist

Set `id` to the Gist ID you want to display.

```yaml
- name: Generate gist card
  uses: zb479519891/github-readme-stats-fast-action@main
  with:
    card: gist
    options: id=0123456789abcdef
    path: profile/gist.svg
    token: ${{ secrets.GITHUB_TOKEN }}
```

## Use JSON options

If you prefer JSON, wrap the entire JSON object in single quotes:

```yaml
- name: Generate stats with JSON options
  uses: zb479519891/github-readme-stats-fast-action@main
  with:
    card: stats
    options: '{"username":"octocat","show_icons":true,"hide_rank":true}'
    path: profile/stats.svg
    token: ${{ secrets.GITHUB_TOKEN }}
```

## Tokens and permissions

- For public profile cards, start with `${{ secrets.GITHUB_TOKEN }}`.
- Some cards or private-data use cases may require a personal access token with the appropriate access. Store it as a repository secret and pass it through `token`.
- Never hard-code a token in a workflow file or README.
- Grant `contents: write` only when your workflow needs to commit generated SVG files.
- If a run fails, open the repository's **Actions** tab and inspect the logs for the failed step.

## How it works

1. GitHub Actions checks out your repository.
2. The action runs the selected card renderer and writes the SVG to the specified `path`.
3. Your workflow commits the generated SVG files to the repository.
4. Your README embeds those files as images.

This action uses the renderers and data-fetching logic from [Pranesh-2005/github-readme-stats-fast](https://github.com/pranesh-2005/github-readme-stats-fast). For alternative deployment options, see the upstream project's [deployment documentation](https://github.com/pranesh-2005/github-readme-stats-fast#deploy-on-your-own).

## License

This project is licensed under the [MIT License](LICENSE).
