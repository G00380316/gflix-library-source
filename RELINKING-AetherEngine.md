# Replacing AetherEngine in Gflix

Gflix uses AetherEngine, a playback engine licensed under the GNU Lesser General
Public License version 3.0 with its App Store exception. As that licence asks,
AetherEngine is not compiled into Gflix's own executable: it ships as a
framework of its own, `AetherEngineDynamic.framework`, in the app's `Frameworks`
folder, and Gflix loads it from there when it starts. You may replace it with a
build of your own, made from a modified AetherEngine, and run Gflix with it.

The licence text, AetherEngine's version and a link to its source are in the
app under **Settings › About › Open Source**. That link is the exact source Gflix
ships, kept for at least three years after the last Gflix build that shipped
it: https://github.com/G00380316/gflix-library-source/releases/tag/aetherengine-7.51.0-ffmpegbuild-3.10.2,
with AetherEngine 7.51.0, this package and the FFmpeg build beside it.

## What the framework is

`Packages/AetherEngineDynamic` in Gflix's source is a Swift package with one
dynamic library product, `AetherEngineDynamic`. It holds AetherEngine and
AetherEngineSMB, from AetherEngine's own repository
(https://github.com/superuser404notfound/AetherEngine), and re-exports both.
The package is the whole interface between Gflix and AetherEngine:

```swift
@_exported import AetherEngine
@_exported import AetherEngineSMB
```

AetherEngine's FFmpeg libraries already ship as frameworks of their own
(`AetherLibavcodec.framework` and the others, from FFmpegBuild), and are
replaced the same way.

## Building a replacement

1. Get the AetherEngine source at the version Gflix lists in Open Source, and
   make your changes. Keep its public interface exactly as that version
   declares it: the same functions and types, and for its public structs and
   enums the same stored properties and cases, in the same order, and the same
   default argument values. AetherEngine is built without library evolution,
   so Gflix has those layouts and defaults compiled into it.
2. Make a package like the one above, a dynamic library product named
   `AetherEngineDynamic` with a target that depends on your AetherEngine (for
   example `.package(path: "../AetherEngine")`) and re-exports it.
3. Build it for the platform of the Gflix you have, with the Xcode version
   Gflix was built with (Swift modules are tied to their compiler), for
   example:

   ```sh
   xcodebuild -scheme AetherEngineDynamic -configuration Release \
     -destination 'generic/platform=macOS' -derivedDataPath build
   ```

   The framework is `build/Build/Products/Release/PackageFrameworks/AetherEngineDynamic.framework`.

## Putting it in place

- **Mac:** replace `Gflix.app/Contents/Frameworks/AetherEngineDynamic.framework`
  with yours, then sign the app again with your own development identity and a
  provisioning profile carrying Gflix's entitlements (App Sandbox, iCloud, its
  app group), so it keeps its container, settings and iCloud Sync. An ad hoc
  signature (`codesign --force --deep --sign - Gflix.app`) also runs it, but
  without those entitlements: it opens with an empty library and no iCloud.
- **iPhone, iPad and Apple TV:** replace `Gflix.app/Frameworks/AetherEngineDynamic.framework`
  in an app you build or re-sign yourself, and install it with your own
  developer account. App Store copies are signed by Apple, which AetherEngine's
  App Store exception covers.

A replacement missing a function or type Gflix uses fails to load. One that
changes a public type's layout or a default value still loads, and then
misbehaves or crashes: Gflix checks neither.
