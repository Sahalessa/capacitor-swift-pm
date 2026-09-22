# Swift Package Manager Support for Capacitor — pinned distribution fork

This is a fork of [ionic-team/capacitor-swift-pm](https://github.com/ionic-team/capacitor-swift-pm). It exists to distribute **one** binary: Capacitor iOS **8.5.2**, rebuilt from the official source with a single, bounded correction to `Set-Cookie` parsing in `HttpRequestHandler.setCookiesFromResponse`. The stock 8.5.2 code splits the `Set-Cookie` header on commas, so an RFC 1123 `Expires=` date (which contains a comma) truncates the cookie and drops `HttpOnly`, `Path`, `SameSite` and the expiry; the correction hands the response's own header fields to Foundation's cookie parser, scoped to the response URL. Nothing else in Capacitor is changed.

Release: **`8.5.2-cookiefix.1`**

- `Package.swift` points the `Capacitor` binary target at this fork's release asset and leaves the `Cordova` binary target on Ionic's official 8.5.2 asset (URL and checksum unchanged).
- `patches/capacitor-ios-8.5.2-set-cookie-parsing.patch` is the entire source change (one file, +10 / −12).
- `patches/build-cap-cookiefix` is the build script: upstream's `build-cap`, minus its clone step, plus a neutral DerivedData path.
- `PROVENANCE.md` records the upstream commit, the patch and archive checksums, the toolchain, and the comparison against Ionic's official artifact.

Not affiliated with or endorsed by Ionic. Upstream's MIT license (`LICENSE.md`) applies unchanged.

---

# Swift Package Manager Support for Capacitor

This repo is for hosting binary xcframework releases of Capacitor and CapacitorCordova for SPM support.

Only applies to Capacitor major versions 6, 7, and 8. Starting with Capacitor 9, the iOS platform is a source-first Swift package built directly from the [ionic-team/capacitor](https://github.com/ionic-team/capacitor) repository.
