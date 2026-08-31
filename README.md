# CiscoDevNet homebrew tap

This is the shared [Homebrew](https://brew.sh) tap for the CiscoDevNet organization. It
hosts formulae for CiscoDevNet command-line tools so they can be installed and updated
with `brew`.

## Install

```sh
brew tap CiscoDevNet/tap
brew trust CiscoDevNet/tap        # required once, on Homebrew 6.0+
brew install CiscoDevNet/tap/sccfm-cli
```

After the tap is added you can also install by short name, e.g. `brew install sccfm-cli`.

The `brew trust` step is new in Homebrew 6.0: third-party taps must be explicitly trusted
before their formulae will build. It is a one-time action per machine.

## Formulae

| Formula | Description |
| --- | --- |
| `sccfm-cli` | Cisco SCC Firewall Manager CLI and Python automation library ([sccfm-devkit](https://github.com/CiscoDevNet/sccfm-devkit)) |

## How releases work

Version bumps are automated. Each tool's source repository owns its release process; on a
new release, a workflow in that repository opens a pull request against this tap to update
the corresponding formula (new `url`, `sha256`, and regenerated resource blocks). Maintainers
review and merge that PR, after which the new version is available through `brew`.

For `sccfm-cli`, the bump PR is opened by `.github/workflows/bump-homebrew.yml` in
[CiscoDevNet/sccfm-devkit](https://github.com/CiscoDevNet/sccfm-devkit). The automation
authenticates as a GitHub App installed on this repository with Contents and Pull requests
write permissions; its `TAP_BOT_APP_ID` and `TAP_BOT_APP_KEY` secrets live in the
`sccfm-devkit` repository, not here.

## Adding a new tool to this tap

1. Add `Formula/<name>.rb` using `Language::Python::Virtualenv` and
   `virtualenv_install_with_resources`. Set `desc`, `homepage`, the PyPI sdist `url`,
   `sha256`, `license`, and `depends_on "python@3.12"`.
2. Generate dependency resources with tooling — never hand-write hashes:
   ```sh
   brew update-python-resources CiscoDevNet/tap/<name>
   ```
3. Verify locally:
   ```sh
   brew style CiscoDevNet/tap/<name>
   brew audit --new --formula CiscoDevNet/tap/<name>
   brew install --build-from-source CiscoDevNet/tap/<name>
   brew test CiscoDevNet/tap/<name>
   ```
4. Open a pull request. CI (`.github/workflows/tests.yml`) lints and tests the formula.

## License

Apache-2.0. See [LICENSE](LICENSE).
