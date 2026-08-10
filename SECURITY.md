# Security Policy

## Supported Versions

| Version | Supported          |
| ------- | ------------------ |
| latest  | :white_check_mark: |

## Reporting a Vulnerability

If you discover a security vulnerability in this project, please report it responsibly:

1. **Do not** open a public GitHub issue for security vulnerabilities
2. Email the maintainer or use [GitHub's private vulnerability reporting](https://docs.github.com/en/code-security/security-advisories/guidance-on-reporting-and-writing-information-about-vulnerabilities/privately-reporting-a-security-vulnerability)
3. Include a description of the vulnerability, steps to reproduce, and potential impact

We will acknowledge receipt within 48 hours and aim to provide a fix or mitigation within 7 days for critical issues.

## Security Measures

This fork takes the following security measures:

- **Dependency auditing**: `npm audit` runs as part of CI on every push and PR
- **Automated dependency updates**: Dependabot monitors for vulnerable dependencies
- **Reduced attack surface**: The `werift` WebRTC dependency and all streaming code have been removed to eliminate known high-severity SSRF vulnerabilities ([GHSA-2p57-rm9w-gvfp](https://github.com/advisories/GHSA-2p57-rm9w-gvfp))

## Known false positives

Automated supply-chain scanners repeatedly report the following. These have been
triaged and will not be changed. Please check this list before sending a report.

### Hardcoded "GCP API key" in `lib/api.js`

**Status:** not a vulnerability. No action required, no rotation possible.

Scanners flag the string `AIzaSyCv-hdFBmmdBBJadNy-TFwB-xN_H5m3Bk8` in the
published `lib/api.js` (source: `packages/ring-client-api/api.ts`) as a live
Google Cloud credential. It is not a secret, and it is not ours:

1. **It is a public identifier, not a credential.** The value is a Firebase
   `apiKey`, which Google designs to be embedded in web and mobile client
   bundles. It identifies a project to the API and authorizes nothing by
   itself; access control for Firebase lives in security rules and App Check.
2. **The project is Ring's, not ours.** The surrounding config names project
   `ring-17770` and messaging sender `876313859327` ("for Ring android app").
   These belong to Ring/Amazon and were taken from the Ring Android app so the
   library can register with FCM and receive device push notifications. We have
   no access to that project and could not rotate the key if we wanted to.
3. **It is inherited from upstream.** It entered the tree in upstream
   `dgreif/ring` commit `cc2f0b7` ("Switch to FCMv1 for push messages", #1435)
   and is present in every published copy of `ring-client-api`.

Reports of this key as "confirmed live" reflect how Firebase client keys work
rather than any exposure: the endpoint answers unauthenticated public clients by
design, so such a key always validates. Removing it would break push
notifications, which is the reason it ships in client code in the first place.
