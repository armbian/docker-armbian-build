<h2 align="center">
  <a href=#><img src="https://raw.githubusercontent.com/armbian/.github/master/profile/logosmall.png" alt="Armbian logo"></a>
  <br><br>
</h2>

# docker-armbian-build

## Purpose of This Repository

Build and publish the Docker images used across Armbian's automation and infrastructure. All images are produced by GitHub Actions on a schedule (and on demand via *Run workflow*) and pushed to the [GitHub Container Registry](https://github.com/orgs/armbian/packages).

## Published Images

| Image | Purpose |
| --- | --- |
| `ghcr.io/armbian/docker-armbian-build:armbian-<os>-<release>-<arch>-latest` | Build-framework containers used by [`armbian/build`](https://github.com/armbian/build) (`./compile.sh docker …`). Dockerfile is generated via the framework's `compile.sh generate-dockerfile` helper against each supported `<os>:<release>` and target `<arch>`. |
| `ghcr.io/armbian/repository-update:<release>-<arch>` | Internal package-publishing pipeline image (bundles `aptly` and the signing/keyring tooling). |
| `ghcr.io/armbian/apt-cacher-ng:latest` (also `:trixie`) | Caching apt proxy, built from Debian trixie's `apt-cacher-ng`. Replaces the long-abandoned `sameersbn/apt-cacher-ng:3.3` image. |
| `ghcr.io/armbian/git_cdn:latest` | Caching `git+http(s)` proxy / CDN (Groupe Renault's [`git_cdn`](https://gitlab.com/grouperenault/git_cdn)), built multi-arch from upstream source since the project only ships an x86-64 image. |

Where applicable, images are built multi-arch (`linux/amd64`, `linux/arm64`, and for the build-framework also `linux/riscv64` and `linux/arm/v7`) using Docker Buildx with QEMU, or natively on self-hosted/arch-specific runners (`ubuntu-*-arm`, `ubuntu-24.04-riscv`) where cross/emulated builds are impractical.

The build-framework image matrix is generated dynamically from the supported-release list in [`armbian/build`](https://github.com/armbian/build) (`config/distributions/*/support` and `config/distributions/*/architectures`), so it tracks upstream automatically — EOS'd and manually disabled releases are skipped.

## Dockerfiles

Dockerfiles are **generated at build time inside each workflow** (either inlined via a `cat > Dockerfile` heredoc, or produced by the Armbian framework's `compile.sh generate-dockerfile` helper) rather than committed to this repository.

## Repository Layout

```text
.
├── .github/
│   ├── dependabot.yml            # Keeps workflow Actions dependencies up to date
│   └── workflows/                # Image-build and maintenance workflows
├── LICENSE                       # GNU GPL v3
└── README.md
```

## Continuous Integration

Workflow run status, schedules, and build history for this repository are tracked on the Armbian CI dashboard:

<https://actions.armbian.com/?repo=docker-armbian-build>

A maintenance watchdog runs every 15 minutes and automatically re-runs failed jobs (up to a few attempts) for the daily image-build workflows, so transient registry or network errors self-heal without manual intervention.

## Built With

- **GitHub Actions** (YAML workflows) for orchestration
- **Bash** inside workflow `run:` steps for matrix generation, Dockerfile authoring, and `gh api` driven maintenance
- **Docker Buildx** + **QEMU** for multi-arch image builds
- **Python 3.12** + **Poetry** to build the `git_cdn` wheel from upstream source
- **`gh` CLI** / GitHub REST API for the watchdog's rerun logic

## Related Projects

- [`armbian/build`](https://github.com/armbian/build) — Armbian build framework; consumer of the per-release build images.
- Armbian documentation: <https://docs.armbian.com>
- Project website: <https://www.armbian.com>

## License

Distributed under the terms of the **GNU General Public License v3.0**. See [`LICENSE`](LICENSE) for the full text.
