# Differ Action

GitHub composite action for [Differ](https://github.com/Vanilla-OS/Differ).

## Inputs

| Name | Required | Default | Description |
| --- | --- | --- | --- |
| `image` | yes | — | Primary image name in differ, i.e. the old `gnome` |
| `legacy_image` | no | `''` | Compatibility image name in differ, i.e. the old `desktop`; empty means only `image` is reported |
| `differ_url` | yes | — | differ base URL, e.g. `${{ vars.DIFFER_URL }}` |
| `differ_user` | yes | — | differ basic auth user |
| `differ_psw` | yes | — | differ basic auth password |
| `packages_owner` | no | repository owner | Owner of the container package queried through the GitHub API |

## Outputs

| Name | Description |
| --- | --- |
| `digest` | Digest of the image that was reported to differ |

## Usage

```yaml
  differ:
    needs: merge
    if: github.repository == 'vanilla-os/desktop-image' && github.ref_type == 'tag'
    runs-on: ubuntu-latest
    container:
      image: ghcr.io/vanilla-os/gnome:latest
    permissions:
      contents: read
      packages: read
    steps:
      - uses: actions/checkout@v7
      - uses: vanilla-os/differ-gh-action@main
        with:
          image: gnome
          legacy_image: desktop
          differ_url: ${{ vars.DIFFER_URL }}
          differ_user: ${{ secrets.DIFFER_USER }}
          differ_psw: ${{ secrets.DIFFER_PSW }}
```

The `packages: read` permission is required for the token used to list container package versions.

Before reporting each release the action calls `POST /images/new`, so images do not need to be created in differ beforehand. The image URL is filled with the repository running the workflow; a duplicate `name` may be rejected by differ, which is ignored.

The package list is collected by the `gen_package_list.sh` bundled with this action, so image repositories do not need to ship their own script. It is the same logic as Differ's [`get_packages.sh`](https://github.com/Vanilla-OS/Differ/blob/main/get_packages.sh).
