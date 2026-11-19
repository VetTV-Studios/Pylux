# VetTV-Studios fork of Pylux

This repository is a fork of
[ForWard-Technologies-LLC/Pylux](https://github.com/ForWard-Technologies-LLC/Pylux),
which is itself built on [Chiaki](https://git.sr.ht/~thestr4ng3r/chiaki)
and [chiaki-ng](https://github.com/streetpea/chiaki-ng). All upstream
copyright notices, credits, and license files are preserved unchanged.

## License

Pylux and this fork are licensed under the **GNU Affero General Public
License v3.0 only, with the OpenSSL exception** — see
[`COPYING`](COPYING) and [`LICENSES/AGPL-3.0-only-OpenSSL.txt`](LICENSES/AGPL-3.0-only-OpenSSL.txt).
Nothing in this fork changes those terms:

- The complete corresponding source for every fork build is this public
  repository; Windows packages additionally carry `COPYING` and a
  generated `SOURCE_CODE.txt` (with the exact Git commit) inside
  `usr/share/licenses/pylux/`.
- Modified files carry the fork's changes in Git history; this file is
  the prominent notice of modification required by AGPL-3.0 §5.
- Third-party components keep their own licenses (see `third-party/`,
  submodules, and the dependency lists in `vcpkg.json` / the Windows
  workflow). The Windows build links an FFmpeg GPL shared build, which
  is compatible with distribution under AGPL-3.0.

## What this fork changes (and what it does not)

Intentionally minimal, so upstream fixes stay easy to pull:

1. **Safe release automation.** Dropbox publishing in
   `build-windows.yml` and the docs deploy in `deploy-docs.yml` only
   run in the upstream repository. In this fork the Windows workflow
   uploads the portable zip and installer as GitHub Actions artifacts
   instead. App-store deploy workflows remain manual-dispatch only and
   are not run by this fork.
2. **AGPL files in the Windows package.** The Windows workflow now
   runs upstream's own `scripts/add-agpl-compliance.sh`, so the
   portable zip and installer ship with the license text and source
   offer (upstream previously omitted them on Windows).
3. **Source offer points here.** `legal/agpl-source-notice.txt` names
   this repository instead of upstream's Dropbox archive.
4. **Documentation.** This file and a short fork notice in `README.md`.
5. **Purchased PS5/PS4 library ownership.** The shared catalog reads
   Sony's `purchasedTitlesRetrieve` purchased-library API (the source
   used by current PlayStation clients) and unions it with the legacy
   `internal_entitlements` flow. This fixes purchased/free-to-play
   titles whose launchable product and stream entitlement IDs differ
   from the public browse SKU, without losing legacy-only licenses
   such as disc upgrades. It only presents licenses returned for the
   signed-in account; it does not create or bypass entitlements.
6. **Session lifecycle hardening.** The fork carries upstream's
   still-open production crash-cluster fixes for controller/session
   thread joins and Qt live-session replacement.
7. **Build maintenance.** A shared-library unit-test workflow runs the
   MUnit suite, and the Windows build uses MSYS2's maintained UCRT64
   repository because current `hidapi` and `diffutils` packages are no
   longer published for legacy MINGW64.

The streaming wire protocol remains upstream's shared C library
(`lib/`). Fork changes to cloud code are limited to the owned-library
source, catalog cache invalidation, and the lifecycle fixes above.

## "Owned games via the cloud" — what actually gates it

Forking this code does not grant any PlayStation entitlement. Whether
cloud streaming of owned PS5 games works is decided entirely by
Sony's servers for the signed-in account:

- You sign in with **your own PSN account** in the app (QR or browser
  flow); the fork never sees anyone else's credentials and ships none.
- The account needs whatever subscription Sony requires for cloud
  streaming in your region (historically PlayStation Plus Premium),
  the title must be cloud-streaming-eligible, and your region must be
  supported. If Sony rejects the entitlement, the app surfaces that
  error — there is no bypass in this code, and none will be added.
- Remote Play to your own console (local network, or over the
  internet via PSN hole-punching / port forwarding) has separate
  requirements — see `docs/setup/remoteconnection.md`.

This project is not affiliated with or endorsed by Sony Interactive
Entertainment. "PlayStation", "PS4", and "PS5" are trademarks of Sony
Interactive Entertainment Inc.

## Building (Windows)

Trigger **Actions → Build Windows → Run workflow** on this fork, wait
for the run, and download `pylux-windows-portable` /
`pylux-windows-installer` from the run's Artifacts. The first run does
a full MSYS2/UCRT64 dependency install and takes a while.

For local builds follow upstream's workflow steps in
`.github/workflows/build-windows.yml` (MSYS2 UCRT64 + Qt6 + the
pinned FFmpeg build + `scripts/build-libplacebo-windows.sh`).

## Syncing with upstream

```bash
git fetch upstream
git merge upstream/master   # or cherry-pick, per upstream's CONTRIBUTING
```

Upstream CI targets `release/beta` for contributions; this fork tracks
`upstream/master` so fork builds stay on released code.
