# docker-omniroute-source

A mirror of [diegosouzapw/OmniRoute](https://github.com/diegosouzapw/OmniRoute) that builds the upstream source from this repo and publishes the resulting container images to GitHub Container Registry (GHCR).

OmniRoute is a free AI gateway: one OpenAI-compatible endpoint in front of hundreds of AI providers (including many free tiers), with quota-aware auto-fallback and token-compression so your coding tools keep working without hitting limits.

## What this repo does

- Tracks the upstream default branch (`release/v*`) on a daily schedule
- Builds it from source into multi-arch (amd64/arm64) images
- Publishes the floating `latest` channel to `ghcr.io/thefrenchghosty/omniroute` (and `:latest-web` for the web build)
- Optionally builds immutable version tags (`v*`) via `workflow_dispatch`

Compared to upstream's publish workflow, the security checks are disabled: no SBOM and no Trivy scans. The images are published as-is.

It exists only to publish self-built images to GHCR for those who prefer to run a build from source rather than the upstream's published images.

## Images

- `ghcr.io/thefrenchghosty/omniroute:latest` - base image
- `ghcr.io/thefrenchghosty/omniroute:latest-web` - web image
- `ghcr.io/thefrenchghosty/omniroute:<version>` - immutable version tags (built on demand)

## Known limitations

None currently. The images track the upstream default branch and build with the full upstream Dockerfile verification.

Resolved workarounds (kept for reference):

- **2026-08-12: upstream tip did not compile** ([diegosouzapw/OmniRoute#9985](https://github.com/diegosouzapw/OmniRoute/issues/9985)). This repo temporarily applied a vendored patch at build time (upstream's own pending fixes). Fixed upstream the same day by the merge of [PR #10131](https://github.com/diegosouzapw/OmniRoute/pull/10131); patch and workflow step removed.
- **2026-08-09: onnxruntime native library missing from the standalone bundle** ([diegosouzapw/OmniRoute#9687](https://github.com/diegosouzapw/OmniRoute/issues/9687)), which failed the Dockerfile's post-build verification. This repo temporarily stripped that check. Upstream's own Docker publish now passes with the check intact, so the workaround was removed and LLMLingua prompt compression and local memory embeddings work in current images.

## License

This repository is licensed under the GNU Affero General Public License v3.0. See [LICENSE](LICENSE). Note that upstream OmniRoute is MIT licensed; this repo's build logic and meta-files are AGPL-3.0, and the upstream code shipped in the images remains under its own license.

---

## AI Acknowledgement

This was made with the help of LLM.

The project was written and refined with Kimi K3.

I knew exactly what I wanted - a mirror that builds OmniRoute from source and publishes to my own GHCR namespace - I just couldn't be bothered to write the GitHub Actions workflow and the version/promotion logic by hand. I made the LLM do exactly what I wanted, and then I tweaked some of it by hand. There was some back and forth, and a bit of "human" work, but this is, at its core still a project made using LLMs.

All testing was done and validated by a Human.

This was for personal use first and foremost, I just decided to release it.

Consider this provided as is, as the LICENSE says.

AI sucks, but I'm not a developer, and I didn't feel like learning GitHub Actions to publish a container image. Blame capitalism, not me.