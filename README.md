<h2 align="center">
  <a href=#><img src="https://raw.githubusercontent.com/armbian/.github/master/profile/logosmall.png" alt="Armbian logo"></a>
  <br><br>
</h2>

### Purpose of This Repository

Build and publish the Docker images used across Armbian's automation and infrastructure, pushed to the [GitHub Container Registry](https://github.com/orgs/armbian/packages). Each image is produced by its own GitHub Actions workflow on a schedule (and on demand via *Run workflow*).

### Published images

- **`ghcr.io/armbian/docker-armbian-build:armbian-<os>-<release>-<arch>-latest`** ([`update_docker.yml`](.github/workflows/update_docker.yml)) — [`armbian/build`](https://github.com/armbian/build) `./compile.sh docker …` build environments.
- **`ghcr.io/armbian/repository-update:<release>-<arch>`** ([`build-docker-images.yml`](.github/workflows/build-docker-images.yml)) — internal package-publishing pipeline (aptly).
- **`ghcr.io/armbian/apt-cacher-ng:latest`** (`:trixie`) ([`build-apt-cacher-ng.yml`](.github/workflows/build-apt-cacher-ng.yml)) — `armbian-config` → `module_aptcacherng`, apt caching proxy.
- **`ghcr.io/armbian/git_cdn:latest`** ([`build-git-cdn.yml`](.github/workflows/build-git-cdn.yml)) — `armbian-config` → `module_git_cdn`, git+http caching proxy.

The build-framework and automation matrices are generated from the supported-release list in `armbian/build`, so they track upstream automatically.

### Maintenance

- [`maintenance-watchdog.yml`](.github/workflows/maintenance-watchdog.yml) — runs every 15 minutes and re-runs failed jobs for the daily `build-docker-images` and `update_docker` workflows, so a transient registry or network error self-heals without manual intervention.
- [`dependabot.yml`](.github/dependabot.yml) — keeps the workflows' GitHub Actions dependencies up to date.

> All images are multi-arch (`linux/amd64` + `linux/arm64`) built with Docker Buildx + QEMU. Dockerfiles are generated at build time inside each workflow rather than committed to this repository.
