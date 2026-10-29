# GetPatched recipe: libxml2

GetPatched maintains selected, tested security backports for package versions whose normal public support has ended. This repository contains the recipe and evidence inputs; it does not vendor a duplicate upstream source tree.

## Current target

| Field | Value |
|---|---|
| Target ID | ubuntu-focal-amd64 |
| Distribution | Ubuntu 20.04 LTS (focal), public updates source |
| Source package and pinned version | libxml2 · 2.9.10+dfsg-5ubuntu0.20.04.10 |
| GetPatched version | 2.9.10+dfsg-5ubuntu0.20.04.10+getpatched1 |
| Runtime packages | libxml2, libxml2-utils |
| Access | Sponsor first; public after 168 hours and release checks |
| Release state | Development candidate; no production package is published |

## Recipe structure and target support

package-manifest.json is the package index. It points to targets/ubuntu/focal-amd64.json, which holds this target's exact base, source publication, pinned .dsc and source archives, supported source versions, package version, binary names, patch IDs, and changelog entry. The platform repository's packages.json separately pins the target's build image, snapshot repositories, runner, scan ecosystem, release policy, and whether repository publication is supported.

A target ID names a distribution, release codename, and architecture, such as ubuntu-focal-amd64 or debian-bookworm-arm64. Add another target as a separate file under targets/<distribution>/<codename>-<architecture>.json, then add its independent platform profile and package policy. Do not claim support or enable publication until that exact combination passes a clean build, installed regressions, vulnerability scans, and an APT client test. This pilot currently supports and can publish only Ubuntu Focal amd64. Debian, other Ubuntu releases, and arm64 are not yet claimed.

The fetched source is the distribution's complete source package, including distro-specific packaging and existing patches. An upstream Git submodule alone would omit that state. Source files are pinned by URL, size, and SHA-256. The source descriptor's OpenPGP signature has not been verified; the recipe records that limitation.

## Patch and version policy

patches/metadata.json records each patch's purpose, checksum, upstream provenance, CVEs, regression tests, and appliesTo targets. An applicability entry may pin one exact source version or express inclusive/exclusive Debian-version bounds. The target's supportedSourceVersions remains an explicit allowlist: a version inside a patch range is not enough to build unless the target lists it and the build and regressions pass. Current patches are pinned to the exact version above.

The output version retains the full Ubuntu source version and adds +getpatched1. A new patch release from the same base advances the serial (+getpatched2); a changed distro source version gets its own reviewed target pin and release policy.

CVE-2025-49794-49796.patch adapts GNOME's Schematron lifetime and node-type checks for the pinned Focal source. It addresses CVE-2025-49794 and CVE-2025-49796, from upstream commit 81cef8c5 (https://gitlab.gnome.org/GNOME/libxml2/-/commit/81cef8c5b5aec2acdf5707e57a6db0c8d1d0abca). The pinned fixtures exercise both cases under Valgrind against the vulnerable baseline and installed patched package, followed by an xmllint consumer check.

## Sponsorship and licensing

Every active individual GitHub Sponsors sponsorship grants access to official early-channel binaries for this sponsor-first target, regardless of tier. A one-time sponsorship qualifies only while GitHub reports it active. Organization sponsorships do not grant access to every member. Access ends when GitHub reports the sponsorship as inactive; device credentials are checked against the current sponsor snapshot and fail closed if it is stale. Early binaries may be used privately and commercially, but may not be redistributed before public release. See LICENSES/GETPATCHED-EARLY-ACCESS.md.

GetPatched-authored recipes, patches, tests, and documentation are MIT-licensed from day one. Anyone may build from public source and redistribute an independent build under MIT and applicable third-party licenses. The MIT file does not replace upstream or Ubuntu copyright and licensing obligations in the source or compiled package. Preserve LICENSES/ubuntu-source-copyright and comply with each included component's license. When this target becomes public, the early-binary redistribution restriction ends for its official binaries.

## Build and verification

From a clean checkout on a disposable Linux Docker host:

    git clone https://github.com/getpatched/platform.git
    git clone --branch getpatched/focal https://github.com/getpatched/packaging-libxml2.git
    python3 platform/scripts/build-package.py --source packaging-libxml2 --target ubuntu-focal-amd64 --output build/libxml2

The builder consumes the target's pinned inputs, runs baseline and patched installed-package checks, retains scan evidence, and tests signed APT installation of the binary and source packages. It does not publish binaries. Ubuntu Pro/ESM compatibility is not tested or claimed. The prior full-source snapshot remains on the legacy branch: https://github.com/getpatched/packaging-libxml2/tree/legacy
