# Android arm64 comparison build

This CMake build produces an executable PRoot ELF and its freestanding loader
with the `.so` names expected by Lyra's Android native-library packaging:

- `dist/libproot_exec.so`
- `dist/libproot_loader.so`

The `out` directory keeps unstripped intermediates for ELF debugging; use the
stripped files in `dist` for packaging and device tests.

It deliberately targets Android API 24, statically links talloc 2.5.0, and
does not enable `libandroid-shmem`. This matches the important build traits of
the currently bundled RikkaHub binaries while keeping the source and settings
auditable in the local PRoot fork.

Configure with the NDK Android toolchain, for example:

```powershell
cmake -S android -B build/android-arm64 -G Ninja `
  -DCMAKE_TOOLCHAIN_FILE=G:/sdk/ndk/29.0.14206865/build/cmake/android.toolchain.cmake `
  -DANDROID_ABI=arm64-v8a `
  -DANDROID_PLATFORM=android-24
cmake --build build/android-arm64
```

The loader is external. `PROOT_LOADER` supplied by the app takes precedence;
when it is absent, PRoot looks for `libproot_loader.so` beside `/proc/self/exe`.
There is no compiled package-specific or Termux fallback path. A missing loader
produces an explicit error instead of silently attempting another package's
private directory.
