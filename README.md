# Publish Raycast Extension

This simple __GitHub Action__ allows you to store a [custom extension](https://github.com/raycast/extensions) for [Raycast](https://raycast.com/) in your own repo, and automatically publish changes to the central `raycast/extensions` repo which powers the [Raycast Store](https://www.raycast.com/store).

Here's how it works, step by step:

1. Set up your own repo for your custom Raycast extension
2. Set up GitHub Actions in your repo using the instructions below
3. Get your extension ready to publish for the first time, or make changes to it
4. Create a tag and then publish a release
5. A PR will automatically be created to `raycast/extensions` with your changes
6. If you need to update your PR, just make your changes, delete the tag, re-add the tag and then publish your release again.

## Usage

### Setting up the GitHub action

1. Create a fork of the `raycast/extensions` repo, if you don't already have one.
2. Push your Raycast extension to its own GitHub repo. The `package.json` should be in the root of the repo.
3. Create a GitHub personal access token with the `repo` and `workflow` permissions enabled, and copy it to your clipboard.
4. Add your GitHub personal access token as a "Repository secret" in the repo from step #1. To do that, navigate to the repo in GitHub, then click "Settings", then click the down arrow next to "Secrets", then "Actions". Click the "New repository secret" button, name your secret `gh_access_token`, paste in the access token and then click "Add secret".
4. Create a `.github/workflows/release.yml` file, and paste in the contents below:

```yaml
name: Push to the `raycast/extensions` repo

on:
  release:
    types: [published]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - uses: timrogers/publish-raycast-extension@main
        with:
          # CHANGE ME to the full name of your fork of the `raycast/extensions` repo (for example `timrogers/extensions`)
          extensions_fork: timrogers/extensions
          # CHANGE ME to your GitHub username
          github_username: timrogers
          # CHANGE ME to the name of your extension. This should be lower case  with dashes separating words. If you've already published your extension at least once to the `raycast/extensions` repo, this should be the name of the folder under `extensions/` where your extension lives.
          extension_name: iata-code-decoder
          # You don't need to do anything here. You've already configured your repository secret.
          github_access_token: ${{ secrets.gh_access_token }}
```

5. Update the four variables marked `CHANGE ME` in your YAML file.
6. Add, commit and push your `.github/workflows/release.yml` file to your repo.

### Publishing changes to your extension

1. Make changes to your extension and update the `CHANGELOG.md` file
2. Tag your commit with a version number (e.g. `git tag -a v1.0.1 -m 'v1.0.1`)
3. Push your tags to GitHub (e.g. `git push origin main --tags`)
4. Create a release in the GitHub UI by going to your repo, clicking "Releases" in the sidebar and then clicking the "Draft a new release" button. Pick your tag, fill in the body with a description of the changes and publish the release. The body will appear in your PR to `raycast/extensions`.
5. The action will run, and a PR will automatically be created in `raycast/extensions` with your changes. You can follow through - and watch out for any errors - in the "Actions" tab of your repo on GitHub. If this is the first ever release of your extension, you will want to manually update the PR title to say it's new ✨
6. If you need to make any more changes to your PR, just delete the tag (`git tag -d v1.0.1 && git push origin :v1.0.1`) and then follow steps 2 to 4. The PR will automatically be updated.

## Assumptions

* Your GitHub personal access token has `repo` and `workflow` permissions enabled.
* Your GitHub personal access token has access to the repo configured in `raycast_extensions_fork_full_name`. (This may not be the case if the repo belongs to an organisation with SSO enabled and you haven't gone through the authorization process - see [here](https://docs.github.com/en/enterprise-cloud@latest/authentication/authenticating-with-saml-single-sign-on/authorizing-a-personal-access-token-for-use-with-saml-single-sign-on)).
