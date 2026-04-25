# ggml-thin-zig

A lightweight, automatically synced fork of [ggml](https://github.com/ggml-org/ggml) optimized for the Zig package manager (`build.zig.zon`).

## Why does this exist?

The official `ggml` repository is an incredible piece of technology, but it is massive.

If you are building a modern C/C++ or Zig project and want to pull in `ggml` as a dependency via `build.zig.zon`, pointing to the upstream repository wastes bandwidth, bloats your global OS cache, and makes build scripts overly complex.

**`ggml-thin-zig` solves this by stripping the repository down to its absolute bare minimum.**

### What is Kept:
- The core C/C++ library (`ggml.h`, `ggml.cpp`, `gguf.cpp`, etc.)
- CPU Backend (with SIMD support)
- Apple Metal Backend
- NVIDIA CUDA Backend
- Vulkan Compute Backend

### What is Purged:
- `CMakeLists.txt` (Compile natively with Zig's `zig cc` / `zig c++`)
- `tests/`, `examples/`, `docs/`, `scripts/`, `python/`
- Niche/unused hardware backends (`sycl`, `opencl`, `cann`, `hexagon`, `rpc`, `webgpu`, `musa`, etc.)

## Automated Upstream Sync

You don't need to worry about this fork falling behind.

A GitHub Action runs automatically every night at 2:00 AM UTC. It fetches the latest `master` branch from `ggml-org/ggml`, deletes all excess directories and CMake files, and pushes the clean C/C++ core here. **No merge conflicts, no force-pushes, just linear history.**

## How to use in Zig (0.17-dev+)

### 1. Add to `build.zig.zon`

Because this repository updates daily, the commit hash changes. Add the following to your `build.zig.zon`, leave the `.hash` empty, and run `zig build`. Zig will download the repo, fail, and give you the correct hash to paste in.

```zig
.{
    .name = .your_project,
    .version = "0.1.0",
    .paths = .{ "src", "build.zig", "build.zig.zon" },
    .dependencies = .{
        .ggml = .{
            .url = "https://github.com/init-helpful/ggml-thin-zig/archive/refs/heads/master.tar.gz",
            .hash = "", // <-- Paste the hash provided by the compiler here
        },
    },
}
```
