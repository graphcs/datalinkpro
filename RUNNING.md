# Running RustDesk from Source on macOS

This guide documents how to build and run RustDesk from source on macOS (Apple Silicon).

## Prerequisites

### Required Tools
- **Rust & Cargo**: RustDesk is written in Rust
  ```bash
  # Check installation
  rustc --version
  cargo --version
  ```

- **vcpkg**: Package manager for C++ libraries
  - RustDesk requires: `libyuv`, `libvpx`, `opus`, `aom`, `ffmpeg`

- **Build tools**:
  - Xcode Command Line Tools
  - `nasm` and `yasm` assemblers (for video codec compilation)

### Optional Tools
- **Flutter**: For the modern UI (recommended over legacy Sciter UI)
- **Python 3**: For using build scripts

## Setup Steps

### 1. Clone the Repository
```bash
git clone https://github.com/rustdesk/rustdesk.git
cd rustdesk
```

### 2. Initialize Git Submodules
RustDesk uses git submodules for core libraries:
```bash
git submodule update --init --recursive
```

### 3. Install vcpkg
```bash
cd ~
git clone https://github.com/Microsoft/vcpkg.git
cd vcpkg
./bootstrap-vcpkg.sh
```

### 4. Install Build Dependencies
```bash
# Install assemblers required for codec compilation
brew install nasm yasm
```

### 5. Install C++ Dependencies via vcpkg
RustDesk uses a `vcpkg.json` manifest file, so vcpkg will automatically install dependencies:

```bash
export VCPKG_ROOT=~/vcpkg
cd /path/to/rustdesk
~/vcpkg/vcpkg install
```

This installs:
- `libyuv` - YUV image conversion
- `libvpx` - VP8/VP9 video codec
- `opus` - Audio codec
- `aom` - AV1 video codec
- `ffmpeg` - Media framework (macOS static build)

The packages will be installed to `./vcpkg_installed/arm64-osx/` in the project directory.

## Building and Running

### Method 1: Using Cargo (Core/Headless)

Set the required environment variables and build:

```bash
export VCPKG_ROOT=/Users/$USER/vcpkg
export VCPKG_INSTALLED_ROOT=/Users/$USER/code/rustdesk/vcpkg_installed

cargo build --release
```

To run:
```bash
cargo run
```

**Note**: The basic `cargo run` will build successfully and start the server components (IPC server, rendezvous mediator, etc.) but will fail to launch the GUI because it tries to use the legacy Sciter UI library which is deprecated.

Example output:
```
[INFO  librustdesk::server] try start server
[INFO  librustdesk::ipc] Started ipc server at path: /tmp/RustDesk/ipc
[INFO  librustdesk::common] Testing nat ...
[INFO  librustdesk::rendezvous_mediator] start rendezvous mediator of rs-ny.rustdesk.com
```

### Method 2: Using Python Build Script (Recommended for Desktop)

For the Flutter-based desktop application:

```bash
export VCPKG_ROOT=/Users/$USER/vcpkg
python3 build.py --flutter
```

For release builds:
```bash
python3 build.py --flutter --release
```

With hardware codec support:
```bash
python3 build.py --flutter --hwcodec
```

### Method 3: Building Just the Rust Binary

If you only need the core Rust components without the UI:

```bash
export VCPKG_ROOT=/Users/$USER/vcpkg
export VCPKG_INSTALLED_ROOT=$(pwd)/vcpkg_installed
cargo build --release
```

The binary will be at `target/release/rustdesk`.

## Build Features

RustDesk supports various feature flags:

- `--features hwcodec` - Hardware video encoding/decoding
- `--features vram` - VRAM optimization (Windows only)
- `--features unix-file-copy-paste` - Unix file clipboard support
- `--features screencapturekit` - macOS ScreenCaptureKit support

Example:
```bash
cargo build --release --features hwcodec,screencapturekit
```

## Troubleshooting

### Issue: "libsciter.dylib not found"
The legacy Sciter UI is deprecated. Use the Flutter build instead:
```bash
python3 build.py --flutter
```

Or download the Sciter SDK if you need to use the legacy UI.

### Issue: "vpx/vp8.h file not found"
Make sure `VCPKG_INSTALLED_ROOT` points to the correct location:
```bash
export VCPKG_INSTALLED_ROOT=$(pwd)/vcpkg_installed
```

The headers should be in `vcpkg_installed/arm64-osx/include/`.

### Issue: "Could not find nasm"
Install the assembler:
```bash
brew install nasm yasm
```

### Issue: Submodule missing (hbb_common not found)
Initialize git submodules:
```bash
git submodule update --init --recursive
```

## Project Structure

- `src/` - Main Rust application code
- `libs/hbb_common/` - Core libraries (git submodule)
- `libs/scrap/` - Screen capture functionality
- `libs/enigo/` - Keyboard/mouse control
- `libs/clipboard/` - Clipboard implementation
- `flutter/` - Flutter UI (modern, recommended)
- `vcpkg.json` - C++ dependency manifest

## Environment Variables Summary

For a successful build, set these before building:

```bash
export VCPKG_ROOT=/Users/$USER/vcpkg
export VCPKG_INSTALLED_ROOT=$(pwd)/vcpkg_installed
```

You can add these to your `~/.zshrc` or `~/.bashrc` for persistence:

```bash
# Add to ~/.zshrc
export VCPKG_ROOT="$HOME/vcpkg"
```

## Success Indicators

When successfully built and run, you should see output like:
```
Started ipc server at path: /tmp/RustDesk/ipc
Testing nat ...
start rendezvous mediator of rs-ny.rustdesk.com
start udp: rs-ny.rustdesk.com:21116
```

This indicates the server components are running correctly.

## Next Steps

- For Flutter UI: Install Flutter SDK and use `python3 build.py --flutter`
- For mobile builds: See platform-specific build scripts in `flutter/` directory
- For server deployment: See RustDesk server documentation
