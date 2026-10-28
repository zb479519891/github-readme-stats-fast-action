# GitHub Readme Stats Fast Action

Generate [GitHub Readme Stats Fast](https://github.com/pranesh-2005/github-readme-stats-fast) cards in your GitHub Actions workflow, commit them to your profile repository, and embed them directly from there.

## Quick start

Add this workflow to your profile repository at `.github/workflows/readme-stats.yml`. Replace the example pin repository values if you want to display a different project.

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
      - uses: actions/checkout@v4

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

      - name: Generate pin card
        uses: zb479519891/github-readme-stats-fast-action@main
        with:
          card: pin
          options: username=pranesh-2005&repo=github-readme-stats-fast
          path: profile/pin-github-readme-stats-fast.svg
          token: ${{ secrets.GITHUB_TOKEN }}

      - name: Commit cards
        run: |
          git config user.name "github-actions[bot]"
          git config user.email "41898282+github-actions[bot]@users.noreply.github.com"
          git add profile/*.svg
          git commit -m "Update README cards" || echo "No changes to commit"
          git push
```

Then embed from your profile README:

```md
![Stats](./profile/stats.svg)
![Top Languages](./profile/top-langs.svg)
![Streak](./profile/streak.svg)
![Pinned](./profile/pin-github-readme-stats-fast.svg)
```

## Deployment options

This action is one way to generate cards without hosting a separate web service. See the [GitHub Readme Stats Fast README](https://github.com/pranesh-2005/github-readme-stats-fast#deploy-on-your-own) for other deployment options.

## Inputs

- `card` (required): Card type. Supported: `stats`, `top-langs`, `streak`, `pin`, `wakatime`, `gist`.
- `options`: Card options as a query string (`key=value&...`) or JSON. If `username` is omitted, the action uses the repository owner.
- `path`: Output path. Defaults to `profile/<card>.svg`.
- `token`: GitHub token (PAT or `GITHUB_TOKEN`). For private repository stats, use a [personal access token](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens) with the required `repo` and `read:user` scopes.

The action exposes the generated SVG path as the `path` output.

## Examples

Stats card:

```yaml
with:
  card: stats
  options: username=octocat&show_icons=true&hide_rank=true&bg_color=0D1117
  token: ${{ secrets.GITHUB_TOKEN }}
```

Top languages card:

```yaml
with:
  card: top-langs
  options: username=octocat&layout=compact&langs_count=6
  token: ${{ secrets.GITHUB_TOKEN }}
```

Streak card:

```yaml
with:
  card: streak
  options: username=octocat&layout=compact
  token: ${{ secrets.GITHUB_TOKEN }}
```

WakaTime card:

```yaml
with:
  card: wakatime
  options: username=octocat&layout=compact
  token: ${{ secrets.GITHUB_TOKEN }}
```

Gist card:

```yaml
with:
  card: gist
  options: id=0123456789abcdef
  token: ${{ secrets.GITHUB_TOKEN }}
```

JSON options:

```yaml
with:
  card: stats
  options: '{"username":"octocat","show_icons":true,"hide_rank":true}'
  token: ${{ secrets.GITHUB_TOKEN }}
```

## Notes

This action uses the same renderers and fetchers as [Pranesh-2005/github-readme-stats-fast](https://github.com/pranesh-2005/github-readme-stats-fast).
