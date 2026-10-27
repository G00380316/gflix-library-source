# Gflix library source

The source of the open-source libraries that Gflix, for iPhone, iPad, Mac and
Apple TV, ships as frameworks of their own, exactly as each Gflix build uses
them. The GNU Lesser General Public License, under which AetherEngine, FFmpeg,
libzvbi and GNU FriBidi are distributed, asks for this source to be available
from a designated place; this repository is that place for Gflix. Each release
here stays available for at least three years after the last Gflix build that
shipped it.

The source files themselves are attached to each release, with their SHA-256
checksums in `SHA256SUMS`.

## Release `aetherengine-7.44.0-ffmpegbuild-3.9.0`

Gflix 4.4 and the builds after it. Every archive is the upstream source at the
tag named, unchanged, except where it says otherwise. From the build after 4.4,
AetherEngine ships as `AetherEngineDynamic.framework`, made by the
`AetherEngineDynamic-gflix` package.

| Archive | What it is | Licence | Upstream |
| --- | --- | --- | --- |
| `AetherEngine-7.44.0.tar.xz` | AetherEngine and AetherEngineSMB, tag `7.44.0` (commit `e5ea8dac56a6962d7f6793fc80b7c84caa61b7af`) | LGPL-3.0 with an App Store exception | https://github.com/superuser404notfound/AetherEngine |
| `AetherEngineDynamic-gflix.tar.xz` | Gflix's package that builds AetherEngine and AetherEngineSMB into `AetherEngineDynamic.framework` and re-exports both | as AetherEngine | (Gflix) |
| `FFmpegBuild-3.9.0-source.tar.xz` | FFmpegBuild tag `3.9.0` (commit `3b398c8f6340`) without its prebuilt frameworks: `build.sh`, which fetches, patches, configures and builds FFmpeg, dav1d, zimg and libzvbi into the `AetherLib*` frameworks, and `patches/` | LGPL-2.1 | https://github.com/superuser404notfound/FFmpegBuild |
| `ffmpeg-n8.1.3.tar.xz` | FFmpeg, tag `n8.1.3` (commit `1041abdc962f4cc4f394aa8de9dc5236c0c3b9e7`) | LGPL-2.1-or-later (configured without `--enable-gpl` or `--enable-version3`) | https://github.com/FFmpeg/FFmpeg |
| `dav1d-1.5.4.tar.xz` | dav1d, tag `1.5.4` (commit `54706fc6bc0cdecab7e9593974a4039cc038fca7`) | BSD-2-Clause | https://code.videolan.org/videolan/dav1d |
| `zimg-release-3.0.6.tar.xz` | zimg, tag `release-3.0.6` (commit `f819b14e8f39d1282400b0d9543e8ef73c1b2bbd`), with its test submodule | WTFPL | https://github.com/sekrit-twc/zimg |
| `zvbi-0.2.45.tar.xz` | libzvbi, tag `v0.2.45` (commit `d3a5ee9f2b047bf16cd1ee5ccf6ec05ee75409d0`) | LGPL-2.0-or-later, `ure.c` MIT | https://github.com/zapping-vbi/zvbi |
| `fribidi-1.0.16.tar.xz` | GNU FriBidi 1.0.16, the release archive as published (SHA-256 `1b1cde5b…a5a9ea05c`), used by GPlayer's subtitle renderer | LGPL-2.1-or-later | https://github.com/fribidi/fribidi |

### The changes to FFmpeg and libzvbi

FFmpegBuild changes FFmpeg and libzvbi before building them, and the changes
are code in its `build.sh`, applied in this order after the sources are fetched:
`patch_ffmpeg`, `patch_ffmpeg_pgssub`, `patch_ffmpeg_visionos`,
`patch_ffmpeg_matroska_tts`, `patch_ffmpeg_vc1_parser`, `patch_ffmpeg_dav1_tag`,
`patch_ffmpeg_mpegts_mpeg1_probe`, `patch_ffmpeg_eac3_dec3`,
`patch_ffmpeg_movenc_fragment_end`, `patch_ffmpeg_dca_x_heights` (which applies
`patches/dca-xll-x-heights.patch`), then `patch_zvbi`, which leaves libzvbi's
three GPL-2 files out of the library and stands LGPL stubs in for the two entry
points they provided. FFmpegBuild's `README.md` explains each change, and
`LICENSES/README.md` its licences. dav1d and zimg are built unchanged.

The FFmpeg configure line is in `build.sh` (`build_one`) and is also recorded
in each built library: `avcodec_configuration()` returns it.

### Building a replacement

`build.sh` in `FFmpegBuild-3.9.0-source.tar.xz` builds all nine frameworks for
every Apple platform: `./build.sh`. It fetches the four sources above itself, at
the same tags, as git clones in its `build/` folder (`ffmpeg-src`, `dav1d-src`,
`zimg-src`, `zvbi-src`); the archives here are those trees, kept here so they stay
available. To build from them instead, unpack each into its folder there and
make it a git repository at the tag `build.sh` names (`git init`, commit, then
`git tag n8.1.3`, `1.5.4`, `release-3.0.6` or `v0.2.45`): `build.sh` re-fetches a
folder whose tag it can't read.

The frameworks keep the install names Gflix loads them by
(`@rpath/AetherLibavcodec.framework/AetherLibavcodec` and the rest, and on a Mac
`@rpath/AetherLibavcodec.framework/Versions/A/AetherLibavcodec`), so one you
build can replace the one in `Gflix.app/Frameworks` (on a Mac,
`Gflix.app/Contents/Frameworks`).

AetherEngine is replaced by building `AetherEngineDynamic.framework` from
`AetherEngineDynamic-gflix.tar.xz` against your AetherEngine; Gflix's
instructions are in `RELINKING-AetherEngine.md` here.

GPlayer's own FFmpeg, and the scripts that build replacements for it and for
FriBidi, are at https://github.com/G00380316/ffmpeg-patched.

## Contact

Questions about this source, or a request for it on a physical medium: the
Gflix Discord server (https://discord.gg/RdCgFVNHF4), or the contact address on
https://gflixhub.app.
