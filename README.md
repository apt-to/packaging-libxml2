# GetPatched recipe: libxml2

GetPatched maintains selected security backports for Ubuntu packages whose public Ubuntu support has ended. This repository is the **recipe** for the libxml2 package: source references, a pinned build target, patch metadata, the patch, and regression fixtures. The upstream source tree is fetched for each build and is not copied into this Git repository.

## Package and build target

| Field | Value |
|---|---|
| Ubuntu source package | `libxml2` |
| Ubuntu base | 20.04 LTS (`focal`), public `updates` source |
| Architecture | `amd64` |
| Pinned Ubuntu source version | `2.9.10+dfsg-5ubuntu0.20.04.10` |
| GetPatched package version | `2.9.10+dfsg-5ubuntu0.20.04.10+getpatched1` |
| Built runtime packages | `libxml2`, `libxml2-utils` |
| Access policy | Sponsor first; public after 168 hours once a release passes its gates |
| Release status | Development candidate; no package release is published |

`package-manifest.json` is the machine-readable source of truth for this target. It pins the Ubuntu `.dsc`, every source archive URL, file size, and SHA-256. The build downloads those exact files, checks their hashes, and lets `dpkg-source` verify the checksums recorded inside the source descriptor. The source code is the Ubuntu source package, including Ubuntu's Debian packaging and patches; a Git submodule to upstream alone would omit that distro-specific state. The descriptor signature has not been cryptographically verified; the manifest records that limitation.

## Versioning and applicability

Only the exact Ubuntu source version in the manifest is supported and tested. Patch metadata names that source version and target explicitly; there is no implied version range. The output version is the full Ubuntu version plus `+getpatched1`. A later rebuild of the same base advances the manifest and platform policy serial to `+getpatched2`, preserving immutable versioned bytes. A different Ubuntu source version needs its own pinned target and regression evidence before it is declared supported.

## Backport

`patches/CVE-2025-49794-49796.patch` backports GNOME's Schematron lifetime and node-type checks to the pinned focal source. It addresses **CVE-2025-49794** and **CVE-2025-49796**. The upstream fix is commit [`81cef8c5b5aec2acdf5707e57a6db0c8d1d0abca`](https://gitlab.gnome.org/GNOME/libxml2/-/commit/81cef8c5b5aec2acdf5707e57a6db0c8d1d0abca). The patch carries DEP-3 provenance; `patches/metadata.json` records its checksum, exact target version, CVEs, and regression evidence.

The fixture pairs in `tests/fixtures/` exercise both Schematron cases. The platform verification runner checks the vulnerable baseline under Valgrind and requires the installed backport to pass with no invalid reads, then runs a normal `xmllint` consumer check. The exact inputs and expectations are listed with the patch metadata.

## Build and verification

From a clean checkout, use the public platform builder on a disposable Linux Docker host:

```sh
git clone https://github.com/getpatched/platform.git
git clone --branch getpatched/focal https://github.com/getpatched/packaging-libxml2.git
python3 platform/scripts/build-package.py \
  --source packaging-libxml2 \
  --output build/libxml2
```

The builder uses the pinned focal archive snapshot, rebuilds the baseline and patched package images, runs the installed regressions, preserves raw Trivy and custom GetPatched scan reports, and tests signed APT installation of the binaries and source package. It does not publish the build. Ubuntu Pro/ESM compatibility is not tested or claimed.

The previous full-source repository snapshot is preserved on the [`legacy`](https://github.com/getpatched/packaging-libxml2/tree/legacy) branch. Copyright and license notices for the fetched Ubuntu source are retained in `LICENSES/ubuntu-source-copyright` and in the built source package.
