# rptadv-portaudio-alsa-adapter workflows

This repository owns the reusable GitHub Actions workflows for
[`rptadv-portaudio-alsa-adapter`](https://github.com/cpeter1207/rptadv-portaudio-alsa-adapter).
The source repository contains thin callers pinned to this repository's
`main` branch so validated workflow fixes take effect without a source change.

The workflow roles are deliberately separate:

- `preflight.yml` runs formatting, lint, and static analysis for ordinary
  source pushes.
- `quality.yml` is the required pull-request gate. It runs platform-independent
  checks once, then native Debian 13 amd64 and arm64 validation in parallel.
  Production coverage runs only on amd64.
- `release.yml` builds source, shared-object, and Debian 13 artifacts from a
  merged main revision. It verifies that the tag's normalized upstream version
  matches that exact revision's `debian/changelog` and that the Debian Rust
  build prerequisites are installed; it does not repeat the pull-request
  quality gate.
- `documentation.yml` publishes warning-free Doxygen documentation.
- `quality-image.yml` builds the adapter's native amd64 and arm64 quality
  images from the source repository's `containers/quality.Dockerfile` and
  publishes one multi-architecture GHCR manifest.

The source repository's `tools/run-in-quality-container.sh` is the local
container entry point. It labels every test container with the project and an
exact workspace scope, removes stale containers carrying those same labels
before a run, and removes its own container on every exit path. GitHub-hosted
job containers are isolated and removed by the runner.

The intended public image is
`ghcr.io/cpeter1207/rptadv-portaudio-alsa-adapter-quality:latest`. It extends
the shared Debian 13 quality base with the pinned Rust toolchains and
PortAudio/ALSA development dependencies. Publish its architecture images from
native runners only; do not use QEMU emulation for validation.

Workflow-only changes are validated by `validate.yml` with Actionlint. They
remain independently repairable when production quality is failing.
