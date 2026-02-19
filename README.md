# vs-package-poc

Multi-backend packaging Proof of Concept (PoC) for VapourSynth plugins.

The goal of this PoC is to demonstrate how to package native VapourSynth plugins (compiled from C++, Zig, or Rust) into standard Python wheels (`.whl`).
This would allows users to install plugins via `pip` while ensuring they are correctly placed for VapourSynth to auto-load.

## Techniques

### Tagging Strategy & ABI Compatibility

Normally, wheels containing native code are tagged with a specific Python version and ABI (e.g., `cp312-cp312-win_amd64`). However, VapourSynth plugins typically depend only on the VapourSynth C API, making them ABI-independent regarding Python itself.

We use a tag normalization trick to produce `py3-none-<platform>` wheels (e.g., `py3-none-win_amd64`). This ensures the wheel can be installed on any Python 3.x version while still being restricted to the correct OS and architecture.

### Binary Placement

To support seamless integration, these PoCs install binaries into the `vapoursynth/plugins/` sub-package within `site-packages`.

### Build Hook Orchestration

Instead of relying on Python-only build systems, we use "glue" backends to orchestrate native compilers:

- **Hatchling + Custom Hooks**: `hatch_build.py` handles the invocation of `meson`, `zig build`, or `cargo`, and manages the placement of the resulting artifacts.
- **scikit-build-core**: CMake-based backend that simplifies native compilation and handles platform tagging out of the box.
- **meson-python**: Specialized backend for Meson-based projects. (Although the implementation has some compromises. See the meson.build file.)

## PoC Branches

| Branch              | Language | Build Tool  | Packaging Backend       |
| :------------------ | :------- | :---------- | :---------------------- |
| `cpluscplus-hatch`  | C++      | Meson       | Hatchling + Custom Hook |
| `cplusplus-mesonpy` | C++      | Meson       | `meson-python`          |
| `zig-hatch`         | Zig      | Zig         | Hatchling + Custom Hook |
| `zig-scikit`        | Zig      | CMake + Zig | `scikit-build-core`     |

> [!NOTE]
> Compiling Rust-based projects would follow the same pattern as `zig-hatch` using Hatchling with a custom build hook to orchestrate the native build.
