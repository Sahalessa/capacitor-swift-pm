# Provenance of release `8.5.2-cookiefix.1`

## What this release is

Capacitor iOS **8.5.2** — `ionic-team/capacitor`, tag `8.5.2` (annotated tag `3bf0f57d593a21a69bce573c0af144eac68d9b97`), commit **`5e0f67871994fec94e413cd0993dc6cd495431f7`** ("Release 8.5.2", 2026-09-11T15:02:23Z) — with one bounded source correction, `patches/capacitor-ios-8.5.2-set-cookie-parsing.patch` (SHA-256 **`b3fe130a3e3da2c3536ec9610f6eb5a230b26f9ffd4521cb693ff8fcca82c338`**), rebuilt as `Capacitor.xcframework` with upstream's own packaging process.

The patch touches only `ios/Capacitor/Capacitor/Plugins/HttpRequestHandler.swift` (+10 / −12 lines; file SHA-256 before `1e6619bf1268788c0e304a03975ed20b5e4515c8025289431f1590afe3ff8473`, after `ccd8404b29d2bc46d187698be22b293786b149ea11b69316e80e20184cf45664`). `setCookiesFromResponse(_:_:)` no longer splits the `Set-Cookie` header on commas — an RFC 1123 `Expires=` date contains one, and the split discarded `HttpOnly`, `Path`, `SameSite` and the expiry, then stored the fragment against the app's own local URL — and instead hands the response's own header fields to `HTTPCookie.cookies(withResponseHeaderFields:for:)` and stores them with `HTTPCookieStorage.shared.setCookies(_:for:mainDocumentURL:)`, scoped to the response URL (host-only when no `Domain=` is present). Nothing else in Capacitor is changed.

## The official distribution this fork is based on

| | |
|---|---|
| upstream distribution repository | `ionic-team/capacitor-swift-pm`, tag `8.5.2` = commit `0b6882e9a3288342aacf36348e5a94e4f1dd7b13` |
| official `Capacitor.xcframework.zip` | SHA-256 `134c65a8bd30bfaa8ccb158d78142fa7231bf3f472e4a5d808c5fbed90fd4c75`, 6,611,661 bytes (GitHub's release-asset digest and the manifest checksum agree) |
| official `Cordova.xcframework.zip` | SHA-256 `57ff2c8f1e5dcd8d4379ac3cccde1407d81ccd575347dc2aff4f72272b8794a1`, 1,842,451 bytes — **kept unchanged by this fork's `Package.swift`** |
| official toolchain, read from the official frameworks | Xcode 26.0.1 (17A400), iOS 26.0 SDK, Swift 6.2 (swiftlang-6.2.0.19.9) |
| how the official binary maps to source | every npm-shipped iOS source file of 8.5.2 is blob-identical to the tag's tree (101 of 101); the framework's bundled `native-bridge.js` is 8.5.2's blob (`20a21b4f…`), not 8.5.1's; 8.5.2's one new private symbol is present in the simulator slice |

## Build inputs

- **source**: `ionic-team/capacitor` at `5e0f67871994fec94e413cd0993dc6cd495431f7`, checked out as `capacitor-checkout/`, the patch applied with `git apply` (exactly one modified file, verified and printed into the build log immediately before the build)
- **build**: `patches/build-cap-cookiefix` — upstream's `build-cap` step for step (schemes `Capacitor` and `Cordova`; simulator archive in Debug, device archive in Release; `SKIP_INSTALL=NO BUILD_LIBRARY_FOR_DISTRIBUTION=YES DEBUG_INFORMATION_FORMAT=dwarf-with-dsym`, device also `SWIFT_SERIALIZE_DEBUGGING_OPTIONS=NO`; nested `Frameworks/` removed; `xcodebuild -create-xcframework` with dSYMs) with three deviations: no clone step inside the script, `-derivedDataPath` under the build root so no local user name reaches the shipped dSYMs, and no code-signing step. The script fails closed: it refuses to build unless the checkout is at exactly `5e0f6787…`, the patched file hashes to exactly `ccd8404b…`, and nothing else is modified (each refusal was exercised on purpose before the shipped build; the refused runs invoke `xcodebuild` zero times)
- **packaging**: `zip -r Capacitor.xcframework.zip Capacitor.xcframework` — upstream's `package-cap`
- **toolchain**: Xcode 27.0 (27A266a); Apple Swift 6.4 (swiftlang-6.4.0.34.1 clang-2100.3.34.1); macOS 27.0 (26A428); iOS 27.0 SDK (`iphoneos27.0`, `iphonesimulator27.0`)
- **deployment target**: iOS 15.0, unchanged (`IPHONEOS_DEPLOYMENT_TARGET = 15.0` in the upstream project; `minos 15.0` in every produced slice)
- **slices**: `ios-arm64` (device) and `ios-arm64_x86_64-simulator`, each with dSYMs — the same two as upstream

## Outputs

| artifact | SHA-256 | bytes |
|---|---|---|
| `Capacitor.xcframework.zip` — **this release's asset** | `04c5adf4d232f5caed004fc04dc5dd9dfbe42d80adb6fda4e105f8ffa4e158c3` | 6,171,023 |
| `Cordova.xcframework.zip` — rebuilt for the comparison only, not published | `fda6afc638843eb3a8152ebfe17f8c995d059d5bae722c54d1def72c6bcb8812` | 1,592,936 |

Device-slice Mach-O UUID: `DDA0F907-ED21-3A02-98C6-A42A196A74F6`. `swift package compute-checksum` and `shasum -a 256` agree on the archive checksum.

## Reproducibility

Two consecutive builds from the same inputs on the same toolchain produced **byte-identical** xcframework trees — 102 files, `diff -r` empty, same UUID. The archive's checksum additionally depends on the file timestamps `zip` stores, so the checksum pinned in `Package.swift` is that of the archive actually published. This is reproducibility from the recorded inputs and toolchain; it is **not** byte-identity with Ionic's official binary, which was built with a different Xcode.

## Comparison against the official 8.5.2 artifact

| check | result |
|---|---|
| xcframework manifest (slices, architectures, platform variants, dSYM paths) | identical |
| minimum OS, per slice (`vtool -show-build`) | 15.0 in both |
| framework `Info.plist` | identical apart from the `DT*` / `BuildMachineOSBuild` toolchain stamps |
| exported symbols (`nm -gU`, per architecture) | identical sets — 1,264 (device), 1,650 (each simulator architecture) |
| Swift ABI descriptors (`.abi.json`, compared structurally by USR) | 573 (device) / 742 (simulator) declarations: 0 added, 0 removed; conformances: 0 removed, +16 / +17 to protocols introduced by the iOS 27 SDK (`CoreVideo.CVAttachmentValueRepresentable`, `ConvertibleToBytes`, `ConvertibleFromBytes`, `CustomDebugStringConvertible`) |
| `.swiftinterface` and `.private.swiftinterface` (six files) and the generated `Capacitor-Swift.h` | identical after normalizing compiler printing — `Module::Type` spelling, re-qualified nested types, `@available` platform order, the dropped `#if compiler(>=5.3)` guards, and the header prelude macros — 0 residual lines |
| `module.modulemap` | Swift 6.4 no longer emits `requires objc` (a relaxation, no API effect) |
| linked libraries | three additional Swift overlays autolinked by the iOS 27 SDK — `libswiftCoreLocation`, `libswiftSpatial`, `libswiftsimd` — all **weak** (`LC_LOAD_WEAK_DYLIB`), so no new launch-time dependency on older iOS |
| resources (`native-bridge.js`, `PrivacyInfo.xcprivacy`) | identical |
| the patched function in the shipped device binary | its body calls `cookiesWithResponseHeaderFields:forURL:` and `setCookies:forURL:mainDocumentURL:` directly; the official binary's body calls neither (both bodies are reached through a 4-byte thunk the optimizer emits) |
| code signature | the official artifact is signed with Ionic's Developer ID; this one is unsigned — the SPM checksum in `Package.swift` is the integrity control |
| embedded local paths | none in the frameworks; the dSYMs embed the neutral build path `/Users/Shared/capacitor-build`, as upstream's embed `/Users/runner/...` |

## Behaviour

The only behavioural delta is the corrected parser. A ten-case XCTest matrix, run in upstream's `CapacitorTests` bundle on the iOS Simulator (iPhone 17, iOS 27.0) with a real `InstanceConfiguration`, covers: an `Expires=` date containing a comma with every attribute preserved and the cookie stored host-only for the response host; `Max-Age`; a session cookie without expiry; explicit `Domain=` with `Secure` and `SameSite=Strict`; host-only behaviour with no `Domain=`; `Path` scoping; two folded `Set-Cookie` headers the first of which carries a date comma; `SameSite=None; Secure`; a response without `Set-Cookie`; and the naive split's own failure. Every case also asserts that the cookie is sent back to the host that set it and never exists for the app's local origin.

| source compiled into the test bundle | result |
|---|---|
| patched (`HttpRequestHandler.swift` `ccd8404b…`) | 10 executed, **0 failures** |
| stock 8.5.2 (`1e6619bf…`, the one file reverted) | 10 executed, **19 assertion failures across 8 cases**; the two that pass never reach the parser |

The tests exercise the patched source compiled into the test bundle, not the packaged binary; the packaged binary is accepted separately by the consuming app on a real device.
