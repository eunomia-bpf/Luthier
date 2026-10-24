<div align="center">
  <img src="./icons/logo.png" alt="Luthier Logo" width="100">
</div>

# Luthier

Luthier is a dynamic binary instrumentation framework for writing tools targeting AMD GPUs.
It supports (or aims to support):

- Analyzing the content of the code objects loaded on the GPU at runtime; This includes kernels, device functions,
  static variables, etc.
- Inserting calls to (multiple) device instrumentation functions.
- Removal/modification/addition of instructions.
- Querying/modification of the GPU's ISA visible state.

## What AMD GPU Applications Luthier Supports?

Luthier supports any [ROCm](https://www.amd.com/en/products/software/rocm.html)-backed application on Linux.
This includes any applications that uses HIP, OpenMP, OpenCL, or even the 
[ROCM runtime](https://github.com/RadeonOpenCompute/ROCR-Runtime/) directly to load and launch kernels on the GPU,
regardless of how their module (code object) was created and loaded.

Luthier does not support the [Platform Abstraction Layer (PAL)](https://github.com/GPUOpen-Drivers/pal)-based 
applications or Windows.

## How Can I get started?

For more information on how to build, get started, and contribute, see the [documentation](docs) folder.
To report bugs, feel free to file an issue on GitHub. 

## Package ROCm 10.1 / LLVM 24 support

The `bpftime-rocm10-llvm24` branch of the eunomia-bpf fork adapts Luthier to
package-installed ROCm 10.1, LLVM 24, and ROCprofiler SDK 1.4.1. It updates
LLVM's internal AMDGPU APIs and instruction definitions, handles HIP's
`hsa_amd_queue_create` dispatch path, and adds `LUTHIER_BUILD_SHARED_TOOLING`.
The descriptor queue path preserves priority and CU masks for system-memory
compute queues; unsupported queue types and memory placements pass through.

Use the LLVM sources matching the installed compiler's commit for
`LUTHIER_LLVM_SRC_DIR`. These supply internal headers and TableGen definitions;
LLVM itself does not need modifications or rebuilding. This branch targets
LLVM 24; compatibility with earlier LLVM versions has not been validated.

For a self-contained instrumentation DSO, configure
`-DLUTHIER_BUILD_SHARED_TOOLING=OFF`, link `LuthierTooling` into the tool, and
use hidden symbol visibility plus `-Wl,--exclude-libs,ALL -Wl,-Bsymbolic`.
This keeps its static LLVM instance separate from ROCm's shared LLVM. The
shared LLVM library does not export all AMDGPU internals required by Luthier.
The default remains a shared `LuthierTooling` library.

The bpftime smoke examples validate binary reconstruction, LLVM IR probes at
kernel entry and wave termination, and inactive-tool/error paths on a Radeon
RX 9070 XT (`gfx1201`, wave32). They are synchronous experiments, not a guarantee
of arbitrary-kernel compatibility or recovery from Luthier's fatal-error paths.

## Found a Bug? Having Issues? Experiencing Crashes? Have a Suggestion or a feature request?
Feel free to file an issue. If you're reporting a crash, please include the stack trace of the
error, and preferably, compile Luthier with `CMAKE_BUILD_TYPE=Debug` and include the debug info alongside
the stack trace of the crash.
