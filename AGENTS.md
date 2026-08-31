# AGENTS.md

Guidance for AI coding agents working in `CiscoDevNet/homebrew-tap`.

This repository is a [Homebrew](https://brew.sh) tap: a collection of formulae
(`Formula/*.rb`) that let users install CiscoDevNet command-line tools with `brew`. The
qualified tap name is `CiscoDevNet/tap`, so formulae install as
`CiscoDevNet/tap/<name>`. There is no application source code here — the "source" is the
Ruby formula files and the CI workflow.

## Dev environment tips

- **Homebrew required**: install from https://brew.sh and ensure `brew` is on `PATH`.
- **Register this working copy as a tap** so `brew` can operate on the local files:
  ```bash
  TAPS="$(brew --repository)/Library/Taps/ciscodevnet"
  mkdir -p "$TAPS"
  ln -sfn "$PWD" "$TAPS/homebrew-tap"
  ```
- **Formula style**: every formula uses `Language::Python::Virtualenv` with
  `virtualenv_install_with_resources`, targets `depends_on "python@3.12"`, and declares a
  SPDX `license`. Do not use PyInstaller or other prebuilt-binary formulae here.
- **Never hand-write dependency `resource` blocks or their hashes.** Generate them:
  ```bash
  brew update-python-resources CiscoDevNet/tap/<name>
  ```

## Testing instructions

Run these locally before opening a PR; all must pass:

```bash
brew style   CiscoDevNet/tap/<name>
brew audit --new --formula CiscoDevNet/tap/<name>
brew install --build-from-source CiscoDevNet/tap/<name>
brew test    CiscoDevNet/tap/<name>
```

- CI (`.github/workflows/tests.yml`) runs `brew test-bot` on macOS and Ubuntu for every PR.
- On Homebrew 6.0+, set `HOMEBREW_NO_REQUIRE_TAP_TRUST=1` when driving the local tap in
  non-interactive contexts.

## PR instructions

- **Security**: never commit real credentials or tokens. Formulae reference only public
  PyPI artifacts.
- **One formula per change** where practical; keep resource-block updates generated, not
  edited by hand.
- **Version bumps are automated.** Each tool's source repo opens a bump PR against this
  tap on release (for `sccfm-cli`, from `CiscoDevNet/sccfm-devkit`). Do not add a bump
  workflow to this repo.

## Contribution conventions

- **Source file headers**: prepend to every formula and workflow file:
  ```
  # Copyright 2026 Cisco Systems, Inc. and its affiliates
  #
  # SPDX-License-Identifier: Apache-2.0
  ```
- **Headings**: sentence case.
- **Backward compatibility**: renaming a formula changes its install name and breaks
  existing users — avoid unless clearly intended and documented.

## References

- Homebrew Formula Cookbook: https://docs.brew.sh/Formula-Cookbook
- Python for Formula Authors: https://docs.brew.sh/Python-for-Formula-Authors
- Cisco DevNet: https://developer.cisco.com/
