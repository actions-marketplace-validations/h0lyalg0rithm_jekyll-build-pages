# jekyll-build-pages

A simple GitHub Action for producing Jekyll build artifacts compatible with GitHub Pages.

## Scope

This is used along with [`actions/deploy-pages`](https://github.com/actions/deploy-pages) as part of the official support for building Pages with Actions (currently in public beta for public repositories).

## Usage

A basic workflow with the `jekyll-build-pages` action looks like this.

```yaml
name: Build Jekyll site
on:
 push:
   branches: ["main"]
permissions:
  contents: read
  pages: write
  id-token: write
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v3
      - name: Setup Pages
        uses: actions/configure-pages@v3
      - name: Build
        uses: actions/jekyll-build-pages@v1
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v1
  deploy:
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v2
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
```

To write to a different destination directory, match the inputs of both the `jekyll-build-pages` and `upload-pages-artifact` actions.

```yaml
steps:
  - name: Build
    uses: actions/jekyll-build-pages@v1
    with:
      destination: "./output"
  - name: Upload artifact
    uses: actions/upload-pages-artifact@v1
    with:
      path: "./output"
```

### Action inputs

| Input | Default | Description |
|-------|---------|-------------|
| `source` | `./` | The directory to build from |
| `destination` | `./_site` | The directory to write output into<br>(this should match the `path` input of the `actions/upload-pages-artifact` action) |
| `future` | `false` | If `true`, writes content dated in the future |
| `build_revision` | `$GITHUB_SHA` | The SHA-1 of the Git commit for which the build is running |
| `verbose` | `false` | If `true`, prints verbose output in logs |
| `token` | `$GITHUB_TOKEN` | The GitHub token used to authenticate API requests |

## Release instructions

In order to release a new version of this Action:

1. Locate the semantic version of the [upcoming release][release-list] (a draft is maintained by the [`draft-release` workflow][draft-release]).

2. Prepare a pull request to update [`action.yml`][action.yml] to reference the incoming version

3. Publish the draft release from the `main` branch with semantic version as the tag name, _with_ the checkbox to publish to the GitHub Marketplace checked. :ballot_box_with_check:

4. After publishing the release, the [`release` workflow][release] will automatically run to create/update the corresponding the major version tag such as `v1`.

   ⚠️ Environment approval is required. Check the [Release workflow run list][release-workflow-runs].

## License

The scripts and documentation in this project are released under the [MIT License](LICENSE).

<!-- references -->
[release-list]: https://github.com/actions/jekyll-build-pages/releases
[draft-release]: .github/workflows/release.yml
[release]: .github/workflows/release.yml
[release-workflow-runs]: https://github.com/actions/deploy-pages/actions/workflows/release.yml
[action.yml]: https://github.com/actions/jekyll-build-pages/blob/649f5d3c2b2462620c8945f034200e431ceddd29/action.yml#LL31C54-L31C60
