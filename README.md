# Ring

[![Node-CI](https://github.com/thoughtminers/ring/actions/workflows/nodejs.yml/badge.svg)](https://github.com/thoughtminers/ring/actions/workflows/nodejs.yml)

This repo contains unofficial packages to enable interaction and automation with the majority of [Ring](https://ring.com/) products

## Fork Notice

This is a security-hardened fork of [dgreif/ring](https://github.com/dgreif/ring).

### Why this fork exists

The upstream `ring-client-api` package depends on `werift` (a WebRTC library) for live streaming support. `werift` and its transitive dependency `werift-ice` pull in the `ip` package, which has a **high-severity SSRF vulnerability** ([GHSA-2p57-rm9w-gvfp](https://github.com/advisories/GHSA-2p57-rm9w-gvfp)) with **no upstream fix available**.

This fork removes `werift` and all streaming-related code from `ring-client-api`, eliminating these vulnerabilities for consumers that do not require live streaming (such as [ring-mqtt](https://github.com/thoughtminers/ring-mqtt)).

### Changes from upstream

- Removed `werift` dependency from `ring-client-api`
- Removed `packages/ring-client-api/streaming/` directory (WebRTC connection, peer connection, streaming session code)
- Removed streaming methods from `RingCamera` (`startLiveCall`, `streamVideo`, `recordToFile`, `createSimpleWebRtcSession`)
- All non-streaming APIs (alarm, devices, camera snapshots, notifications, events) remain fully functional
- Added `npm audit` CI step and Dependabot configuration

## Troubleshooting Issues

If you are having issues, please look for related articles in the [wiki](https://github.com/dgreif/ring/wiki) and search existing [Issues](https://github.com/dgreif/ring/issues) before opening a new Issue/Discussion

## `ring-client-api`

The [ring-client-api](./packages/ring-client-api/) is a TypeScript package designed to be used by developers to create your own apps/programs which interact with Rings api

## `homebridge-ring`

[homebridge-ring](./packages/homebridge-ring/) allows users to easily integrate Ring products into Apple HomeKit via [homebridge](https://homebridge.io/)

## Examples

See the [examples directory](./packages/examples/) for examples using the `ring-client-api`. For a full project example, see https://github.com/dgreif/ring-client-example

## Credits

I'd like to give a big thanks to a number developers who have put a lot of hard work into analyzing the
Ring api and building similar libraries which were extremely valuable in my creation of this project. Thank you all
for your hard work!

- @davglass - https://github.com/davglass/doorbot - The original node project that proved we can interact with Ring's api
- @jimhigson - https://github.com/jimhigson/ring-api - A promisified api for Ring's original line of products
- @tchellomello - https://github.com/tchellomello/python-ring-doorbell - A python api which is widely used for Ring integrations
- @mrose17 - https://github.com/homespun/homebridge-platform-ring-video-doorbell - The original Ring camera homebridge plugin
- @codahq - Thanks for all your help debugging the Ring api
- @joeyberkovitz - Great discovery work on the Ring Alarm websockets api
