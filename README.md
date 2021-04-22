# github-stats (template)

This is a GitHub template that contains a working Action workflow to deploy
static pages containing GitHub stats as GitHub Pages using
[`github-stats-pages`](https://github.com/astrochun/github-stats-pages)

The full documentation and guide is available with `github-stats-pages`.
However, to make it easier, we provide pertinent details below:

## TL;DR
For easy deployment, try this
[GitHub template](https://github.com/astrochun/github-stats). Simply:

1. [Use it!](https://github.com/astrochun/github-stats/generate)
2. Add a Personal access token, as a repository secret, `GH_TOKEN`.
   See [above](#requirements) (Settings > Secrets)
3. Enable GitHub Actions (Settings > Actions)
4. Enable GitHub pages through the settings page and select `gh-pages`
   (Settings > Pages)
5. Sit back and enjoy that ☕️!

## The Nitty Gritty

GitHub Pages deployment is simple with the following GitHub Actions workflow:

```yaml
  - name: Checkout
    uses: actions/checkout@v2
  - name: Get current date
    id: date
    run: echo "::set-output name=date::$(date +'%Y-%m-%d')"
  - name: Build GitHub stats pages
    uses: astrochun/github-stats-pages@latest
    with:
      username: ${{ github.repository_owner }}
      token: ${{ secrets.GH_TOKEN }}
  - name: Upload data to main branch
    uses: EndBug/add-and-commit@v7.0.0
    with:
      add: 'data'
      branch: main
      message: "Update data: ${{ steps.date.outputs.date }}"
      author_name: 'github-actions[bot]'
      author_email: '41898282+github-actions[bot]@users.noreply.github.com'
  - name: Upload static files to gh-pages
    uses: peaceiris/actions-gh-pages@v3
    with:
      personal_token: ${{ secrets.GH_TOKEN }}
      publish_dir: ./public
      keep_files: false
      user_name: 'github-actions[bot]'
      user_email: '41898282+github-actions[bot]@users.noreply.github.com'
      publish_branch: gh-pages
      commit_message: "Update static pages: ${{ steps.date.outputs.date }}"
```

This workflow will run for all public repositories.

##### Inputs

| Variable        | Description                        | Required? | Type  | Defaults | Examples         |
| --------------- | ---------------------------------- | --------- | ----- | -------- | ---------------- |
| `username`      | GitHub username or organization    | **Yes**   | `str` | N/A      | `astrochun`      |
| `token`         | GitHub Personal Access Token (PAT) | **Yes**   | `str` | N/A      | `abcdef12345678` |
| `include-repos` | Comma-separated lists of repositories. This overrides the full list of public repositories | No | `str` | `''` | `'github-stats-pages,astrochun.github.io'`
| `exclude-repos` | Comma-separated lists of repositories to exclude from default public repository list | No | `str` | `''` | `'repo1'` |

##### Other GitHub Action deployment examples:

To override all public repositories and limit to a subset of public repositories,
specify a comma-separated list (_no spaces between commas_) for `include-repos` argument.

```yaml
  - name: Build GitHub stats pages
    uses: astrochun/github-stats-pages@latest
    with:
      username: ${{ github.repository_owner }}
      token: ${{ secrets.GH_TOKEN }}
      include-repos: "github-stats-pages"
```

Alternatively to exclude specific repositories from the list of public repositories,
use the `exclude-repos` argument with a comma-separated list (_no spaces between commas_).

```yaml
  - name: Build GitHub stats pages
    uses: astrochun/github-stats-pages@latest
    with:
      username: ${{ github.repository_owner }}
      token: ${{ secrets.GH_TOKEN }}
      exclude-repos: "repo1,repo2"
```

Note that you can only specify `include-repos` _or_ `exclude-repos`.
**Specifying both will fail**!
