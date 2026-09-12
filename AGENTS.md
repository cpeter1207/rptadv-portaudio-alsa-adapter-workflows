# Workflow development rules

This repository owns reusable GitHub Actions implementations for
`rptadv-portaudio-alsa-adapter`. It contains no production audio code.

Workflow edits are validated with Actionlint only. Do not make a workflow
repair depend on an unrelated production quality result. The workflows here
must not alter production sources or run source-formatting tools that rewrite
files.

For the adapter source repository, ordinary pushes run formatting, lint, and
static analysis once. Pull requests run those platform-independent checks and
Doxygen once, then native Debian 13 amd64 and arm64 build, test, package, and
staged-install jobs. Production line and branch coverage is required only on
native Debian 13 amd64. Releases build Debian 13 artifacts from a main revision
already accepted by the pull-request gate; they do not repeat the full gate.

The adapter quality image is built natively for amd64 and arm64 and published
as a single GHCR manifest. Local source work must use the source repository's
labeled container launcher, which removes only exact project/workspace test
containers before and after a run. Never deploy to a node or alter its
configuration without explicit approval.
