# GitHub Readme Stats Fast Action

**English | [简体中文](#简体中文)**

Generate SVG cards from [GitHub Readme Stats Fast](https://github.com/pranesh-2005/github-readme-stats-fast) with GitHub Actions, commit the files to your repository, and display them in your README—without running your own server.

## Features

- Generate `stats`, `top-langs`, `streak`, `pin`, `wakatime`, and `gist` cards.
- Configure cards with a query string or JSON options.
- Save generated SVGs to a path you choose.
- Embed the generated files directly in a GitHub README.
- Run on a schedule or manually with `workflow_dispatch`.

## Quick start

The following example is intended for your **profile repository** (the repository named after your GitHub username). It generates three cards and commits the SVG files to that repository.

Create `.github/workflows/readme-stats.yml`:

以下示例适用于你的 **个人主页仓库（profile repository）**，即仓库名称与你的 GitHub 用户名相同的仓库。工作流会生成三张卡片，并将 SVG 文件提交回该仓库。

创建文件 `.github/workflows/readme-stats.yml`：

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

> **Note:** `permissions: contents: write` is required for the workflow to commit generated files. If GitHub Actions is restricted from writing to the repository, enable read and write permissions in the repository's Actions settings.

> **注意：** 工作流需要 `permissions: contents: write` 才能将生成的文件提交回仓库。如果仓库设置限制了 GitHub Actions 的写入权限，请在仓库的 Actions 设置中启用读写权限。

## Display the cards | 在 README 中显示卡片

Add these lines to your profile repository's `README.md`. The leading `!` is required to render an SVG as an image instead of a text link.

将以下内容添加到个人主页仓库的 `README.md` 中。注意，图片语法开头必须有 `!`，否则只会显示文字链接，不会显示图片。

```md
![GitHub stats](./profile/stats.svg)
![Top languages](./profile/top-langs.svg)
![GitHub streak](./profile/streak.svg)
```

To make an image clickable, wrap it in a link:

如果希望点击图片后打开 SVG 文件，可以将图片嵌套在链接中：

```md
[![Top languages](./profile/top-langs.svg)](./profile/top-langs.svg)
```

## Inputs | 输入参数

| Input 参数 | Required 必填 | Description 描述 |
| --- | --- | --- |
| `card` | Yes 是 | Card type: `stats`, `top-langs`, `streak`, `pin`, `wakatime`, or `gist`. 卡片类型，可选以上六种。 |
| `options` | No 否 | Card options as a query string (`key=value&...`) or JSON. Defaults to empty. 使用查询字符串或 JSON 设置卡片选项，默认为空。 |
| `path` | No 否 | Output SVG path; defaults to `profile/<card>.svg`. SVG 输出路径，默认是该格式。 |
| `token` | No 否 | GitHub token passed to the renderer. Defaults to empty. 传递给渲染器的 GitHub Token，默认为空。 |

The action exposes a `path` output containing the generated SVG path.

Action 还会输出名为 `path` 的值，表示生成的 SVG 文件路径。

## Examples | 使用示例

### Stats card | 统计卡片

```yaml
- name: Generate stats
  uses: zb479519891/github-readme-stats-fast-action@main
  with:
    card: stats
    options: username=octocat&show_icons=true&hide_rank=true&bg_color=0D1117
    path: profile/stats.svg
    token: ${{ secrets.GITHUB_TOKEN }}
```

### Top languages card | 最常用语言卡片

```yaml
- name: Generate top languages
  uses: zb479519891/github-readme-stats-fast-action@main
  with:
    card: top-langs
    options: username=octocat&layout=compact&langs_count=6
    path: profile/top-langs.svg
    token: ${{ secrets.GITHUB_TOKEN }}
```

### Streak card | 连续贡献卡片

```yaml
- name: Generate streak
  uses: zb479519891/github-readme-stats-fast-action@main
  with:
    card: streak
    options: username=octocat&layout=compact
    path: profile/streak.svg
    token: ${{ secrets.GITHUB_TOKEN }}
```

### Pinned repository card | 仓库置顶卡片

```yaml
- name: Generate pinned repository card
  uses: zb479519891/github-readme-stats-fast-action@main
  with:
    card: pin
    options: username=octocat&repo=Hello-World
    path: profile/pin-hello-world.svg
    token: ${{ secrets.GITHUB_TOKEN }}
```

### WakaTime card | WakaTime 编码时长卡片

```yaml
- name: Generate WakaTime card
  uses: zb479519891/github-readme-stats-fast-action@main
  with:
    card: wakatime
    options: username=octocat&layout=compact
    path: profile/wakatime.svg
    token: ${{ secrets.GITHUB_TOKEN }}
```

### Gist card | Gist 卡片

```yaml
- name: Generate gist card
  uses: zb479519891/github-readme-stats-fast-action@main
  with:
    card: gist
    options: id=0123456789abcdef
    path: profile/gist.svg
    token: ${{ secrets.GITHUB_TOKEN }}
```

### JSON options | 使用 JSON 配置

If you prefer JSON, wrap the JSON object in single quotes.

如果使用 JSON 格式配置，请用单引号包裹整个 JSON 对象。

```yaml
- name: Generate stats with JSON options
  uses: zb479519891/github-readme-stats-fast-action@main
  with:
    card: stats
    options: '{"username":"octocat","show_icons":true,"hide_rank":true}'
    path: profile/stats.svg
    token: ${{ secrets.GITHUB_TOKEN }}
```

## Token and permissions | Token 与权限

- For public repository cards, start with `${{ secrets.GITHUB_TOKEN }}`.
- Some cards or private-data use cases may require a personal access token (PAT) with appropriate access. Store it as a repository secret and pass it through `token`; never hard-code tokens in a workflow or README.
- The workflow needs `contents: write` only when it commits generated SVG files back to the repository.
- If a workflow fails, open the repository's **Actions** tab and inspect the failed step's log.

- 对于公开仓库的卡片，可以先尝试使用 `${{ secrets.GITHUB_TOKEN }}`。
- 某些卡片或私有数据场景可能需要具有相应权限的个人访问令牌（PAT）。请将其保存为仓库 Secret，再通过 `token` 传入；不要将令牌直接写进工作流或 README。
- 只有在需要将生成的 SVG 文件提交回仓库时，工作流才需要 `contents: write` 权限。
- 如果工作流运行失败，请打开仓库的 **Actions** 页面，查看失败步骤的日志。

## How it works | 工作原理

1. GitHub Actions checks out your repository. / GitHub Actions 检出你的仓库。
2. The action runs the selected card renderer and writes the SVG to `path`. / Action 运行所选卡片的渲染器，并将 SVG 写入 `path`。
3. Your workflow commits the generated SVG files. / 工作流将生成的 SVG 文件提交回仓库。
4. Your README embeds those files as images. / README 将这些文件作为图片显示。

This action uses the renderers and fetchers from [Pranesh-2005/github-readme-stats-fast](https://github.com/pranesh-2005/github-readme-stats-fast). For other deployment options, see the upstream project's [deployment documentation](https://github.com/pranesh-2005/github-readme-stats-fast#deploy-on-your-own).

本 Action 使用 [Pranesh-2005/github-readme-stats-fast](https://github.com/pranesh-2005/github-readme-stats-fast) 提供的渲染器和数据获取逻辑。其他部署方式请参考上游项目的[部署文档](https://github.com/pranesh-2005/github-readme-stats-fast#deploy-on-your-own)。

## License | 许可证

This project is licensed under the [MIT License](LICENSE).

本项目采用 [MIT License（MIT 许可证）](LICENSE)。
