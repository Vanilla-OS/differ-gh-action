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
| `package_list_script` | no | `.github/gen_package_list.sh` | Script printing the package list JSON fragment |

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
      - uses: FangcatChina/differ-gh-action@main
        with:
          image: gnome
          legacy_image: desktop
          differ_url: ${{ vars.DIFFER_URL }}
          differ_user: ${{ secrets.DIFFER_USER }}
          differ_psw: ${{ secrets.DIFFER_PSW }}
```

The `packages: read` permission is required for the token used to list container package versions.
