# VapourSynth Plugin Packaging PoC

This branch demonstrates a Proof of Concept for packaging VapourSynth plugins as Python wheels using the [Hatch](https://hatch.pypa.io/) build backend and [Zig](https://ziglang.org/).

## Development Workflow

### 1. Initialize the Project

Initialize the Python project structure using `uv`.

```bash
uv init --name vapoursynth-zsmooth --bare --build-backend hatch --python 3.12
```

### 2. Configuration

Configure `pyproject.toml` and the dev dependencies.

### 3. Environment Setup

Create a virtual environment for development, including auto-completion and language server support.

```bash
uv sync --only-dev
```

### 4. Implement build logic

- Implement the custom build logic in `hatch_build.py`.
- Once you're done, add the `[tool.hatch.build.targets.wheel.hooks.custom]` section to the pyproject.

### 5. Build Process

Build the source distribution and the wheel. It is recommended to specify `--sdist --wheel` so they get build separately and subsequent wheel builds can benefit from the `zig-cache`.

```bash
uv build --sdist --wheel
```

The custom build hook in `hatch_build.py` handles the Zig compilation and places the resulting binaries into the correct package structure (`vapoursynth/plugins`), ensuring they are correctly discovered by VapourSynth.
