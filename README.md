# VapourSynth Plugin Packaging PoC

This branch demonstrates a Proof of Concept for packaging VapourSynth plugins as Python wheels using [meson-python](https://mesonbuild.com/meson-python/).

## Development Workflow

### 1. Initialize the Project

Initialize the Python project structure using `uv`.

```bash
uv init --name vapoursynth-awarp --bare --python 3.12
```

### 2. Configuration

Configure `pyproject.toml` and `meson.build`.

### 3. Build Process

Build the source distribution and the wheel.

```bash
uv build --sdist --wheel
```

## How it Works

The integration uses several clever Meson and `meson-python` interactions to satisfy both VapourSynth's and PEP 427's requirements:

1.  **Pure Pathing**: By installing to `py.get_install_dir()`, the DLL is placed in the `purelib` section of the wheel. This prevents `meson-python` from mapping it to a specific Python ABI (like `cp314`), keeping the `py3-none` tag. When installed, `pip`/`uv` will merge this into the standard `site-packages` directory.
2.  **Platform Tag Forcing**: Since VapourSynth plugins are platform-specific, the wheel MUST have a platform tag (e.g., `win_amd64`). We force this by installing a dummy file (`awarp.tag`) into the system `libdir`. The presence of a file in a platform-dependent directory triggers `meson-python` to add the platform suffix to the wheel filename.

**Note**:

These tricks assume `purelib` and `platlib` map to the same directory. On certain platforms (like Fedora), they are separate.

In such cases, the plugin must be built for each platform specifically using `cibuildwheel` to ensure it lands in the correct `platlib` path beside the VapourSynth engine. Using `cibuildwheel` would also simplify the `meson.build` file as the manual platform tag forcing wouldn't be necessary.
