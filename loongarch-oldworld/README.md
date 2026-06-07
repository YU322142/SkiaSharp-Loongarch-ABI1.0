# SkiaSharp LoongArch old-world ABI1.0

中文说明见 [../README.zh-CN.md](../README.zh-CN.md).

This fork carries a reproducible build path for `libSkiaSharp.so` targeting LoongArch old-world ABI1.0 (`linux-loongarch64`, LP64, glibc 2.28 compatible).

The default GitHub Actions build downloads the matching Linux x64 old-world GCC 14 toolchain and full old-world development sysroot published by `YU322142/loongarch-oldworld-sysroot`. The build passes `--sysroot` directly to that downloaded sysroot, so public cross-tools bundled sysroots are not used for normal linking.

Default toolchain:

```text
https://github.com/YU322142/loongarch-oldworld-sysroot/releases/download/oldworld-dev-sysroot-20260607/linux-x64-cross-tools-loongarch64-oldworld-gcc14-20260607.tar.xz
SHA256: 78335E5FCE4FDD6505B23734C539304547753FA1887CC199F5EA0C7554ED0185
```

Default sysroot:

```text
https://github.com/YU322142/loongarch-oldworld-sysroot/releases/download/oldworld-dev-sysroot-20260607/loongarch64-oldworld-dev-sysroot-20260607.tar.xz
SHA256: 5D442178DB80F8C1BC599B5C0E5963071BBBB33270DE05747959ADC65E7BC086
```

## Build

```bash
bash loongarch-oldworld/build-skiasharp.sh
```

Useful overrides:

- `SKIASHARP_REF=v3.119.4` selects the SkiaSharp source tag.
- `TOOLCHAIN_URL=...` selects a different cross-tools archive.
- `TOOLCHAIN_SHA256=...` enforces the toolchain archive hash.
- `SYSROOT_URL=...` selects the full development sysroot archive.
- `SYSROOT_SHA256=...` enforces the sysroot archive hash.
- `MAX_GLIBC=2.28` controls the ABI gate.

## Output

The script writes:

- `artifacts/loongarch-oldworld/out/libSkiaSharp.so`
- `artifacts/loongarch-oldworld/out/libSkiaSharp.so.119.0.0`
- `artifacts/loongarch-oldworld/out/native-build-manifest.txt`

The committed `prebuilt/linux-loongarch64/oldworld/libSkiaSharp.so` is the locally verified build used for ClassIsland testing.

## Feature intent

The build keeps the desktop rendering features needed by ClassIsland:

- Ganesh enabled
- X11 and OpenGL enabled
- Vulkan enabled
- fontconfig and FreeType enabled
- Skottie enabled
- bundled codec dependencies
- static `libstdc++` and `libgcc`

The GN args intentionally do not add `$SYSROOT/usr/include/freetype2` as a global include path. System headers are exposed through `--sysroot`, while Skia's bundled FreeType headers stay ahead for bundled FreeType sources. This avoids mixing old sysroot FreeType public headers with bundled FreeType internal headers.

The CI gate fails if the output is not LoongArch LP64 or if any required GLIBC symbol version is newer than `GLIBC_2.28`.
