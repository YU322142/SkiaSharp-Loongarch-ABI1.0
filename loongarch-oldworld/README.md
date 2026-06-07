# SkiaSharp LoongArch old-world ABI1.0

中文说明见 [../README.zh-CN.md](../README.zh-CN.md).

This fork carries a reproducible build path for `libSkiaSharp.so` targeting LoongArch old-world ABI1.0 (`linux-loongarch64`, LP64, glibc 2.28 compatible).

The default GitHub Actions build downloads the `loong64/cross-tools` `baseline` toolchain and uses the sysroot bundled in that toolchain. A separate sysroot upload is not required for the normal build.

## Build

```bash
bash loongarch-oldworld/build-skiasharp.sh
```

Useful overrides:

- `SKIASHARP_REF=v3.119.4` selects the SkiaSharp source tag.
- `TOOLCHAIN_URL=...` selects a different cross-tools archive.
- `TOOLCHAIN_SHA256=...` enforces the toolchain archive hash.
- `SYSROOT_URL=...` optionally replaces the cross-tools bundled sysroot.
- `SYSROOT_SHA256=...` enforces the external sysroot archive hash.
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
