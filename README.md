# GhosttyKit for Waycar

This repository publishes `GhosttyKit.xcframework.zip`, the libghostty
terminal core from [Ghostty](https://github.com/ghostty-org/ghostty) that the
Waycar iOS app links through Swift Package Manager. It is built without GNU
gettext (libintl).

Upstream's Apple builds statically link GNU gettext, which is LGPL. Waycar
does not need translated strings from the core, so this build turns
internationalization off and leaves libintl out completely. Nothing else
changes: the inputs are the ones the
[libghostty-spm](https://github.com/Lakr233/libghostty-spm) 1.6.20260909
release used.

## What is in the release

| | |
|---|---|
| Ghostty | `82938b633ba646db38591d969c3c526332bd7e65` |
| libghostty-spm (build scripts and Ghostty patch set) | `7e45d27160f9b34aca9ca5c9820e9207482f9f04` (tag `1.6.20260909`) |
| Zig | 0.16.0 |
| Xcode / SDK used | Xcode 27.0 (27A266a), iOS SDK 27.0; the objects declare iOS 15.0 as their minimum |
| Extra build flag | `-Di18n=false` |
| Extra patch | [`Scripts/0100-gate-libintl-on-i18n.patch`](Scripts/0100-gate-libintl-on-i18n.patch): in Ghostty's `src/build/SharedDeps.zig`, link libintl only when i18n is on |
| Slices | `ios-arm64`, `ios-arm64_x86_64-simulator`, `ios-arm64_x86_64-maccatalyst`, `macos-arm64_x86_64`, `xros-arm64`, `xros-arm64_x86_64-simulator` |
| Zip size | 76,042,185 bytes |
| SwiftPM checksum | `d64018c8fad48bf84a32622c653d33ef0d40ed154e2a760304924497b79c7de9` |

Compared with upstream's asset for the same Ghostty commit, every archive
has the same headers and loses exactly 30 members, all of them gettext and
gnulib objects. No slice has any gettext symbol, defined or referenced.

Use it from a package manifest:

```swift
.binaryTarget(
    name: "libghostty",
    url: "https://github.com/otreva/ghosttykit/releases/download/1.6.20260909-82938b6-nointl.1/GhosttyKit.xcframework.zip",
    checksum: "d64018c8fad48bf84a32622c653d33ef0d40ed154e2a760304924497b79c7de9"
)
```

## Release names

Tags read `<libghostty-spm release>-<Ghostty short commit>-nointl.<n>`. For
example, `1.6.20260909-82938b6-nointl.1` is the first build without gettext
from libghostty-spm 1.6.20260909 and Ghostty 82938b6. `n` goes up when the
same inputs are built again, for example with a newer Xcode. A rebuild always
has a new checksum, because the zip records file times.

## Rebuilding

You need macOS with Xcode, Zig 0.16.0, git and curl. Then run:

```sh
Scripts/build-libghostty.sh /path/to/work-dir
```

The script clones both projects at the pinned commits, adds the patch, and
builds all slices, which takes about 30 minutes. It fails if a slice is
missing or still contains gettext, and otherwise collects `LICENSES/` and
prints the zip's path and checksum.

## Licenses

The core contains code from Ghostty, libghostty-spm and the libraries and
fonts they build in. [`LICENSES/`](LICENSES) has every license text, and
[`LICENSES/README.txt`](LICENSES/README.txt) lists each component. The
release also attaches the same folder as `LICENSES.zip`. One component is
copyleft: z2d (MPL-2.0). Its source is at
<https://deps.files.ghostty.org/z2d-7dbae85c81784dba9988320bf9543ed9a81350c8.tar.gz>.

The scripts in `Scripts/` are Copyright 2026 OTREVA, LLC and licensed under
the Apache License 2.0 (see [`LICENSE`](LICENSE)). The patch changes Ghostty
and is MIT, like Ghostty
([`LICENSES/Ghostty-MIT.txt`](LICENSES/Ghostty-MIT.txt)).
