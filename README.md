# Create GitHub Release Action

This action creates a GitHub release for a repository.

It is expected to run on a tag. By default, it fetches the release notes file
from the repository at the current ref using the GitHub Contents API, so the
file does not need to be checked out locally just to generate the release
notes. It can also upload assets to the release.

The action follows [semver][], so if the tag looks like a pre-release, it will
be created as a pre-release in GitHub too.

Here is an example demonstrating how to use it in a workflow:

```yaml
jobs:
  build:
    name: Build
    runs-on: ubuntu-slim
    permissions:
      # Needed to read the release notes file from the repository and create
      # the release.
      contents: write

    steps:
      - name: Create the GitHub release
        uses: frequenz-floss/gh-action-create-github-release@<hash>  # vX.Y.Z
```

If you want to upload local files as release assets, add your own checkout or
build steps before calling this action.

## Permissions

The token used by this action needs repository `contents` access:

- `contents: read` to fetch `release_notes_file` via the GitHub API.
- `contents: write` to create the release.

In practice, set:

```yaml
permissions:
  contents: write
```

This action is a composite action, so it cannot declare permissions itself.
The calling workflow must grant them.

## Inputs

* `release_notes_file`: The file name of the release notes file. Default:
  `RELEASE_NOTES.md`.

  It must exist in the repository at the current ref. The action fetches its
  contents through the GitHub API and uses them as the release notes. A local
  checkout is not required unless your workflow needs one for other reasons.

* `upload_files`: The files to be uploaded as release assets. Default: `""`.

  It can be a list of file paths or glob patterns that are expanded on the
  runner.

  If empty, nothing will be uploaded.

* `github_token`: The GitHub token to use for authentication. Default: `${{
  github.token }}`.

  This is required to fetch the release notes file and create the release. It
  should have `contents: write`, so you should add this to your job
  permissions:

  ```yaml
  permissions:
    contents: write
  ```

[semver]: https://semver.org
