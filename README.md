# svdpi

Single-header [SystemVerilog Direct Programming Interface (DPI)](https://en.wikipedia.org/wiki/SystemVerilog_DPI) header for C and C++, as defined in IEEE 1800.

`svedpi.h` provides the canonical data types, constants, and function prototypes used to write DPI-C imported functions and interface with the simulator.

## Features

- **Header-only** — no build step, no dependencies
- **C and C++ compatible** — `extern "C"` guards included; C++11 or later for C++ consumers
- **Portable** — handles MSVC, MinGW, Cygwin, Linux, and other Unix platforms
- **CMake support** — `svdpi::svdpi` interface target for `add_subdirectory` / FetchContent

## Getting started

### Standalone

Copy `svdpi.h` and add the project root to your include path:

```cpp
#include <svdpi.h>
```

### CMake

```cmake
# via add_subdirectory
add_subdirectory(path/to/svdpi)
target_link_libraries(my_app PRIVATE svdpi::svdpi)

# or via FetchContent
include(FetchContent)
FetchContent_Declare(svdpi
    GIT_REPOSITORY <repository-url>
    GIT_TAG        main
)
FetchContent_MakeAvailable(svdpi)
target_link_libraries(my_app PRIVATE svdpi::svdpi)
```

To install the header and export CMake package targets:

```bash
cmake -S . -B build
cmake --install build --prefix /usr/local   # installs svdpi.h to include/ and svdpiTargets.cmake to lib/cmake/svdpi
```

## API overview

### Data types

| Type | Description |
|------|-------------|
| `svBit`, `svLogic`, `svScalar` | 2-state / 4-state scalar values |
| `svBitVecVal` | Canonical packed 2-state vector (`uint32_t` chunks) |
| `svLogicVecVal` | Canonical packed 4-state vector (`aval`/`bval` pair per chunk) |
| `svOpenArrayHandle` | Opaque handle to an open (unsized) array |
| `svScope` | Opaque handle to a module/interface scope |
| `svTimeVal` | Simulation time value |

Canonical scalar values: `sv_0`, `sv_1`, `sv_z`, `sv_x`.

### Helper macros

- `SV_PACKED_DATA_NELEMS(WIDTH)` — number of 32-bit chunks for a packed array of the given width
- `SV_MASK(N)`, `SV_GET_UNSIGNED_BITS(V, N)`, `SV_GET_SIGNED_BITS(V, N)` — mask off unused bits

### Function groups

- **Bit- and part-select**: `svGetBitselBit`, `svPutBitselLogic`, `svGetPartselBit`, `svPutPartselLogic`, ...
- **Open-array queries**: `svLeft`, `svRight`, `svLow`, `svHigh`, `svSize`, `svDimensions`, `svGetArrayPtr`, `svGetArrElemPtr1/2/3`, ...
- **Array element copy** (simulator ↔ user space): `svGetBitArrElem1VecVal`, `svPutLogicArrElem3VecVal`, ...
- **DPI context / scope**: `svGetScope`, `svSetScope`, `svGetNameFromScope`, `svGetScopeFromName`
- **User data**: `svPutUserData`, `svGetUserData`
- **Disable protocol**: `svIsDisabledState`, `svAckDisabledState`
- **Time**: `svGetTime`, `svGetTimeUnit`, `svGetTimePrecision`
- **Misc**: `svDpiVersion`, `svGetCallerInfo`
- **Deprecated** (IEEE 1800-compliant tools may not support): `svBitVec32`/`svLogicVec32` based APIs — `svPutBitVec32`, `svGetPartSelectBit`, `svGetBits`, `svGet64Bits`, ...

See the comments in `svdpi.h` for full documentation of each function.

## Example

SystemVerilog side — declare an imported function:

```systemverilog
module top;
    import "DPI-C" function int factorial(int n);
    initial $display("factorial(5) = %0d", factorial(5));
endmodule
```

C++ side — implement it using this header:

```cpp
#include <svdpi.h>

extern "C" int factorial(int n) {
    return n <= 1 ? 1 : n * factorial(n - 1);
}
```

Compile as a shared library (e.g. `g++ -shared -fPIC -I/path/to/svdpi factorial.cpp -o factorial.so`) and load it with your simulator's DPI mechanism (e.g. `-sv_lib`).

## Notes

- **Declarations only.** This header declares the DPI functions; the implementations are provided by the simulator at load/link time. Do not attempt to link them from a static library.
- **Simulator compatibility.** Simulators ship their own `svdpi.h`. If you hit compatibility issues, use the header bundled with your simulator — the API surface is the same.
- **Windows.** `DPI_DLLESPEC` / `DPI_DLLISPEC` expand to `__declspec(dllexport)` / `__declspec(dllimport)` on MSVC/MinGW/Cygwin, and to nothing elsewhere.
- **Custom linkage.** Define `DPI_EXTERN` before including the header to override how the prototypes are emitted.
- The header also defines the PLI-compatible types `s_vpi_vecval` and `s_vpi_time` (guarded by `VPI_VECVAL` / `VPI_TIME`).

## References

- [IEEE 1800 SystemVerilog LRM](https://ieeexplore.ieee.org/document/8299595) — clause 35, "Direct programming interface"
- [Accellera SystemVerilog](https://www.accellera.org/downloads/standards/systemverilog)
