# docker-omniroute-source

A mirror of [diegosouzapw/OmniRoute](https://github.com/diegosouzapw/OmniRoute) that builds the upstream source from this repo and publishes the resulting container images to GitHub Container Registry (GHCR).

OmniRoute is a free AI gateway: one OpenAI-compatible endpoint in front of hundreds of AI providers (including many free tiers), with quota-aware auto-fallback and token-compression so your coding tools keep working without hitting limits.

## What this repo does

- Tracks the upstream default branch (`release/v*`) on a daily schedule
- Builds it from source into multi-arch (amd64/arm64) images
- Publishes the floating `latest` channel to `ghcr.io/thefrenchghosty/omniroute` (and `:latest-web` for the web build)
- Optionally builds immutable version tags (`v*`) via `workflow_dispatch`

Compared to upstream's publish workflow, all verification is disabled: no SBOM, no Trivy scans, and the Dockerfile's own post-build import check is stripped at build time (see below). The images are published as-is.

It exists only to publish self-built images to GHCR for those who prefer to run a build from source rather than the upstream's published images.

## Images

- `ghcr.io/thefrenchghosty/omniroute:latest` - base image
- `ghcr.io/thefrenchghosty/omniroute:latest-web` - web image
- `ghcr.io/thefrenchghosty/omniroute:<version>` - immutable version tags (built on demand)

## Known limitations

### 2026-08-12: upstream tip does not compile

The upstream default branch tip is currently broken upstream and fails to compile (tracked upstream as [diegosouzapw/OmniRoute#9985](https://github.com/diegosouzapw/OmniRoute/issues/9985); upstream's own Docker publish fails on every run). This repo applies a small vendored patch at build time ([patches/upstream-build-fixes.patch](patches/upstream-build-fixes.patch)) containing upstream's own pending fixes ([PR #10108](https://github.com/diegosouzapw/OmniRoute/pull/10108), plus one extra stale re-export removal in `catalog.ts`): a missing brace, an unclosed provider-catalog entry, a duplicate import, two bad import paths, an unresolved wasm URL, and a colliding migration.

Once upstream merges their fixes, the patch stops applying cleanly and the workflow prints a warning instead of failing; the patch file and the "Apply upstream build fixes" step should then be removed.

### 2026-08-09: onnxruntime native library missing

The upstream default branch has a build bug ([diegosouzapw/OmniRoute#9687](https://github.com/diegosouzapw/OmniRoute/issues/9687); fix [PR #9712](https://github.com/diegosouzapw/OmniRoute/pull/9712) was closed unmerged): the standalone bundle is missing the onnxruntime native library, which fails upstream's own Docker publish. This repo works around it by stripping the Dockerfile's post-build verification step.

Consequences for the published images:

- **LLMLingua prompt compression** (opt-in, off by default) does not apply; it fail-opens by design, so requests pass through uncompressed rather than erroring.
- **Memory with local transformers embeddings** (opt-in, off by default, lazy-loaded) is unavailable; API-based embedding providers are unaffected.
- Everything else (all providers, combo routing/fallback, dashboard, MCP/A2A, auth, etc.) works normally.

Once an upstream fix lands, the next scheduled build picks it up automatically and both features work again.

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