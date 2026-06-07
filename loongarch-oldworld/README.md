# SkiaSharp LoongArch old-world ABI1.0

中文说明见 [../README.zh-CN.md](../README.zh-CN.md).

This fork carries a reproducible build path for `libSkiaSharp.so` targeting LoongArch old-world ABI1.0 (`linux-loongarch64`, LP64, glibc 2.28 compatible).

The default GitHub Actions build downloads the `loong64/cross-tools` `baseline` toolchain, uses the sysroot bundled in that toolchain as the base, then layers an old-world development sysroot overlay on top of it. The overlay keeps fontconfig, FreeType, X11, OpenGL and Vulkan development files available without disabling ClassIsland desktop rendering features.

Default overlay:

```text
https://github.com/YU322142/SkiaSharp-Loongarch-ABI1.0/releases/download/oldworld-dev-sysroot-20260607/loongarch64-oldworld-dev-sysroot-overlay-20260607.tar.xz
SHA256: AD9DD4DB6C74D085279FF017AB4F743DB2CBA9013A1739BE3C62E56B84CA2F30
```

## Build

```bash
bash loongarch-oldworld/build-skiasharp.sh
```

Useful overrides:

- `SKIASHARP_REF=v3.119.4` selects the SkiaSharp source tag.
- `TOOLCHAIN_URL=...` selects a different cross-tools archive.
- `TOOLCHAIN_SHA256=...` enforces the toolchain archive hash.
- `SYSROOT_URL=...` selects a development sysroot overlay to layer on top of the cross-tools bundled sysroot.
- `SYSROOT_SHA256=...` enforces the overlay archive hash.
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

The CI gate fails if the output is not LoongArch LP64 or if any required GLIBC symbol version is newer than `GLIBC_2.28`.
