# docker-omniroute-source

An [OmniRoute](https://github.com/diegosouzapw/OmniRoute) image, built from source

[Image page](https://github.com/TheFrenchGhosty/docker-omniroute-source/pkgs/container/omniroute)

This repo tracks the upstream default branch (`release/v*`) on a schedule (every 3 hours), builds it from source into multi-arch (amd64/arm64) images, and publishes them to `ghcr.io/thefrenchghosty/omniroute`. Compared to upstream's publish workflow, the security checks are disabled. No SBOM, no Trivy scans. The images are published as-is.

This repo exists to publish self-built images for anyone who'd rather run a source build than upstream's published images.

## Tags

- `latest` tracks the upstream default branch, built from source.
- `latest-web` is the web build of the above.
- `v*` are immutable version tags, built on demand via `workflow_dispatch`.

## Usage

- Download (or copy the content of) the `compose.yaml`.
- Fill in the required secrets: `JWT_SECRET`, `API_KEY_SECRET`, `MACHINE_ID_SALT`, `INITIAL_PASSWORD`. The `openssl rand` hints are in the file's comments.
- Create the data folders: `mkdir -p data redis-data`
- `docker compose up -d`
- The dashboard is available at `http://127.0.0.1:20128`.

## License

This repository is licensed under the Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC BY-NC-SA 4.0). See [LICENSE](LICENSE). Upstream OmniRoute is MIT licensed. This repo's build logic and meta-files are CC BY-NC-SA 4.0, and the upstream code shipped in the images remains under its own license.

---

## AI Acknowledgement

This was made with the help of LLM.

The project was written and refined with Kimi K3.

I knew exactly what I wanted - a mirror that builds OmniRoute from source and publishes to a image repository - I just couldn't be bothered to write the GitHub Actions workflow and the version/promotion logic by hand. I made the LLM do exactly what I wanted, and then I tweaked some of it by hand. There was some back and forth, and a bit of "human" work, but this is, at its core still a project made using LLMs.

All testing was done and validated by a Human.

This was for personal use first and foremost, I just decided to release it.

Consider this provided as is, as the LICENSE says.

AI sucks, but I'm not a developer, and I didn't feel like learning GitHub Actions to publish a container image. Blame capitalism, not me.
