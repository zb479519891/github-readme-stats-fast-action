# GitHub Readme Stats Fast Action

Generate SVG cards from [GitHub Readme Stats Fast](https://github.com/pranesh-2005/github-readme-stats-fast) with GitHub Actions, commit the files to your repository, and display them in your README—without running your own server.

## Features

- Generate `stats`, `top-langs`, `streak`, `pin`, `wakatime`, and `gist` cards.
- Configure cards with a query string or JSON options.
- Save generated SVGs to a path you choose.
- Use the generated files directly in a GitHub README.
- Run on a schedule or manually with `workflow_dispatch`.

## Quick start

The following example is intended for your **profile repository** (the repository named after your GitHub username). It generates three cards and commits the SVG files to that repository.

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

> **Note:** `permissions: contents: write` is needed for the workflow to commit generated files. If your repository's settings restrict GitHub Actions from writing to the repository, enable read and write permissions in the repository's Actions settings.

## Display the cards

Add these lines to your profile repository's `README.md`. The leading `!` is required to render each SVG as an image rather than a text link.

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
| `options` | No | Card options as a URL query string (`key=value&...`) or a JSON object. Defaults to an empty string. |
| `path` | No | Destination path for the SVG. Defaults to `profile/<card>.svg`. |
| `token` | No | GitHub token passed to the card renderer. Defaults to an empty string. |

The action exposes a `path` output containing the output path of the generated SVG.

## Examples

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

```yaml
- name: Generate gist card
  uses: zb479519891/github-readme-stats-fast-action@main
  with:
    card: gist
    options: id=0123456789abcdef
    path: profile/gist.svg
    token: ${{ secrets.GITHUB_TOKEN }}
```

### JSON options

If you prefer JSON, wrap the JSON object in single quotes:

```yaml
- name: Generate stats with JSON options
  uses: zb479519891/github-readme-stats-fast-action@main
  with:
    card: stats
    options: '{"username":"octocat","show_icons":true,"hide_rank":true}'
    path: profile/stats.svg
    token: ${{ secrets.GITHUB_TOKEN }}
```

## Token and permissions

- For public repository cards, start with `${{ secrets.GITHUB_TOKEN }}`.
- Some cards or private-data use cases may need a personal access token (PAT) with appropriate access. Store it as a repository secret and pass that secret through `token`; do not hard-code tokens in the workflow or README.
- The workflow needs `contents: write` only if it commits generated SVG files back to the repository.
- If a workflow fails, open the **Actions** tab in your repository and inspect the failed step's log.

## How it works

1. GitHub Actions checks out your repository.
2. This action runs the selected card renderer and writes the SVG to `path`.
3. Your workflow commits the generated SVG files.
4. Your README embeds those committed files as images.

This action uses the renderers and fetchers from [Pranesh-2005/github-readme-stats-fast](https://github.com/pranesh-2005/github-readme-stats-fast). For more deployment options, see that project's [deployment documentation](https://github.com/pranesh-2005/github-readme-stats-fast#deploy-on-your-own).

## License

This project is licensed under the [MIT License](LICENSE).
