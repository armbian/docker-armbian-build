<h2 align="center">
  <a href=#><img src="https://raw.githubusercontent.com/armbian/.github/master/profile/logosmall.png" alt="Armbian logo"></a>
  <br><br>
</h2>

### Purpose of This Repository

Build and publish Docker images used across Armbian's automation, pushed to the [GitHub Container Registry](https://github.com/orgs/armbian/packages):

- **Build-framework images** for [`armbian/build`](https://github.com/armbian/build) — isolated, reproducible, architecture-independent build environments consumed by `./compile.sh docker …` in CI and local development.
- **Repository-automation images** for the internal package-publishing pipeline (aptly and supporting tooling), with the supported-release matrix tracking `armbian/build` automatically.
