# AptTo focal backport

Public baseline: `2.9.10+dfsg-5ubuntu0.20.04.10`. Development version: baseline plus `+aptto1`. Binary access policy: 168 hours for sponsors before public promotion; all source remains public.

The quilt patch backports the upstream fix for CVE-2025-49794 and CVE-2025-49796. Its formatting/property calls are adapted to 2.9.10. Original notices and distro patches remain intact. Regression inputs in `debian/tests/fixtures` come from the referenced upstream commit under libxml2's existing licence.

This is an unverified development backport. A clean focal build, baseline failure, installed-package regression success, normal tests, consumer checks, version ordering, scan integration, independent review and signed publication are required before a release claim.
