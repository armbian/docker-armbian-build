<h2 align="center">
  <a href=#><img src="https://raw.githubusercontent.com/armbian/.github/master/profile/logosmall.png" alt="Armbian logo"></a>
  <br><br>
</h2>

# docker-armbian-build

## Purpose of This Repository

This repository builds and publishes the Docker images used across Armbian's automation and infrastructure. Every image is produced by a GitHub Actions workflow on a schedule (and on demand via *Run workflow*), and pushed to the [GitHub Container Registry](https://github.com/orgs/armbian/packages).

## Published images

| Image | Purpose |
| --- | --- |
| `ghcr.io/armbian/docker-armbian-build:armbian-<os>-<release>-<arch>-latest` | Build environments for [`armbian/build`](https://github.com/armbian/build) (`./compile.sh docker …`), generated per supported OS/release/arch. |
| `ghcr.io/armbian/repository-update:<release>-<arch>` | Internal package-publishing pipeline (aptly-based), matrix generated from the framework's supported distributions. |
| `ghcr.io/armbian/apt-cacher-ng:latest` / `:trixie` | Multi-repo caching apt proxy, built from Debian trixie's `apt-cacher-ng`. |
| `ghcr.io/armbian/git_cdn:latest` | Caching git+http(s) proxy / CDN (Groupe Renault `git_cdn`), built multi-arch from upstream. |

The `docker-armbian-build` and `repository-update` matrices are generated from the supported-release list in [`armbian/build`](https://github.com/armbian/build) (`config/distributions/*/support` and `.../architectures`), so they track upstream automatically. Both `apt-cacher-ng` and `git_cdn` correspond to the `armbian-config` modules `module_aptcacherng` and `module_git_cdn` respectively.

Most images are multi-arch (`linux/amd64` + `linux/arm64`, and additionally `linux/riscv64` / `linux/arm/v7` where applicable) built with Docker Buildx + QEMU, or natively on architecture-specific GitHub-hosted runners. Dockerfiles are generated at build time inside each workflow rather than committed to this repository.

## Repository layout

```
.github/
├── dependabot.yml          # keeps GitHub Actions dependencies up to date
└── workflows/              # image build + maintenance workflows
LICENSE                     # GNU GPL v3
README.md
```

## Continuous integration

All image builds happen in GitHub Actions. For a live overview of the workflows in this repository, their schedules and recent runs, see:

<https://actions.armbian.com/?repo=docker-armbian-build>

A dedicated watchdog runs every 15 minutes and automatically re-runs failed jobs of the daily image-build workflows (up to a bounded number of attempts), so transient registry or network errors self-heal without manual intervention.

## Related resources

- Armbian build framework: <https://github.com/armbian/build>
- Armbian documentation: <https://docs.armbian.com>
- Armbian project site: <https://www.armbian.com>

## License

Released under the [GNU General Public License v3.0](LICENSE).
