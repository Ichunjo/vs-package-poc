# VapourSynth Plugin Packaging PoC

This branch demonstrates a Proof of Concept for packaging VapourSynth plugins as Python wheels using the [scikit-build-core](https://scikit-build-core.readthedocs.io/) build backend
and [CMake](https://cmake.org/) to orchestrate the [Zig](https://ziglang.org/) build.

## Development Workflow

### 1. Initialize the Project

Initialize the Python project structure using `uv`.

```bash
uv init --name vapoursynth-zsmooth --bare --build-backend scikit --python 3.12
```

### 2. Configuration

The build logic is defined in `CMakeLists.txt`, which:

1. Detects the Zig compiler.
2. Invokes `zig build`.
3. Installs the compiled binaries into the `vapoursynth/plugins` directory of the wheel.

### 3. Build Process

Build the wheel using `uv build`.

```bash
uv build --sdist --wheel
```
