<h2 align="center">
  <a href=#><img src="https://raw.githubusercontent.com/armbian/.github/master/profile/logosmall.png" alt="Armbian logo"></a>
  <br><br>
</h2>

# docker-armbian-build

## Purpose of This Repository

Builds and publishes the Docker images Armbian uses across its build framework and infrastructure automation, pushing multi-arch images to the [GitHub Container Registry](https://github.com/orgs/armbian/packages). All Dockerfiles are generated on the fly inside GitHub Actions workflows rather than committed as files.

## Published Images

| Image | Tags | Purpose |
| --- | --- | --- |
| `ghcr.io/armbian/docker-armbian-build` | `armbian-<os>-<release>-<arch>-latest` | Build environments used by [`armbian/build`](https://github.com/armbian/build)'s `./compile.sh docker …` flow. One tag per supported `(os, release, arch)` combination (Ubuntu jammy/noble/resolute, Debian bookworm/trixie; `amd64`, `arm64`, and `riscv64` where supported). |
| `ghcr.io/armbian/repository-update` | `<release>-<arch>` | Internal package-publishing pipeline (ships `aptly` plus the keyrings and helpers needed to sign and push the Armbian apt repositories). Matrix is generated from `armbian/build`'s `config/distributions/*` support flags. |
| `ghcr.io/armbian/apt-cacher-ng` | `latest`, `trixie` | Current `apt-cacher-ng` from Debian trixie, pre-configured with Armbian mirror remaps and an entrypoint that discovers live mirrors from `apt.armbian.com` / `beta.armbian.com`. Replaces the abandoned `sameersbn/apt-cacher-ng:3.3` image. Backs `armbian-config`'s `module_aptcacherng`. |
| `ghcr.io/armbian/git_cdn` | `latest` | Multi-arch build of Groupe Renault's [git_cdn](https://gitlab.com/grouperenault/git_cdn), the caching git+http(s) proxy / CDN. Backs `armbian-config`'s `module_git_cdn`. |

All images are built multi-arch (`linux/amd64` + `linux/arm64`, plus `linux/riscv64` for the framework images where applicable) via Docker Buildx with QEMU emulation; `riscv64` framework images build natively on self-hosted RISC-V runners.

## How It Works

- The build-framework matrix (`docker-armbian-build` image tags) and the repository-update matrix are generated at run time from `armbian/build`'s `config/distributions/*/support` (releases marked EOS are skipped) and `architectures` files, so supported releases track the upstream framework automatically.
- Dockerfiles for the per-release framework images are produced by `./compile.sh generate-dockerfile` from a checkout of `armbian/build`. For `trixie` and `resolute` an additional layer installs GCC 13 (native + cross toolchains) alongside the newer default compiler, which some vendor sources require.
- `apt-cacher-ng` and `git_cdn` images are self-contained: their Dockerfiles are written inline in their workflows, built with `docker/build-push-action`, and pushed with GHA cache (`type=gha`).
- A watchdog job runs every 15 minutes and automatically re-runs failed jobs (up to 3 attempts) for the daily framework and repository-update builds, so transient registry/network failures self-heal.

## Repository Layout

```
.github/
  dependabot.yml           # keeps workflow Actions versions up to date
  workflows/               # image build + maintenance workflows (YAML)
LICENSE                    # GNU GPL v3
README.md
```

The repository contains no application source code of its own: all build logic lives inside the GitHub Actions workflow definitions under `.github/workflows/` (YAML with inline `bash` for Dockerfile generation and matrix construction).

## Continuous Integration

Image builds, scheduled rebuilds, and the maintenance watchdog all run as GitHub Actions. For a live overview of the workflows in this repository and their status, see:

<https://actions.armbian.com/?repo=docker-armbian-build>

## Related Projects

- [`armbian/build`](https://github.com/armbian/build) — the Armbian build framework; its `config/distributions/` data drives the matrices here.
- [`armbian/armbian-config`](https://github.com/armbian/armbian-config) — ships `module_aptcacherng` and `module_git_cdn`, which consume the `apt-cacher-ng` and `git_cdn` images.
- [Armbian documentation](https://docs.armbian.com/) · [armbian.com](https://www.armbian.com/)

## License

Released under the [GNU General Public License v3.0](LICENSE).
