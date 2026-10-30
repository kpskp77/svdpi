# svdpi

C and C++ headers for the [SystemVerilog Direct Programming Interface (DPI)](https://en.wikipedia.org/wiki/SystemVerilog_DPI) and the Programming Language Interface (PLI/VPI), as defined in IEEE Std 1800-2023.

`svdpi.h` provides the canonical data types, constants, and function prototypes used to write DPI-C imported functions and interface with the simulator. `vpi_user.h` and `sv_vpi_user.h` provide the corresponding VPI API used to write user system tasks/functions, callbacks, and other simulator extensions.

The headers follow the standard's own header listings from IEEE Std 1800-2023.

## Headers

| Header | Description |
|--------|-------------|
| `svdpi.h` | DPI data types, constants, and function prototypes (IEEE 1800 clause 35) |
| `vpi_user.h` | VPI object types, properties, structures, and routine declarations (clause 36); includes `vpi_compatibility.h` |
| `sv_vpi_user.h` | SystemVerilog VPI extensions — extra object types, properties, callbacks, coverage VPI, and assertion VPI; includes `vpi_user.h` |
| `vpi_compatibility.h` | Version-selected backwards-compatibility aliases for the VPI routines. Included by `vpi_user.h`; never include it directly |

## Features

- **Header-only** — no build step, no dependencies
- **DPI and VPI in one package** — `svdpi.h` and the VPI headers can be included from the same translation unit, in either order
- **C and C++ compatible** — `extern "C"` guards included; C++11 or later for C++ consumers
- **Portable** — handles MSVC, MinGW, Cygwin, Linux, and other Unix platforms
- **CMake support** — `svdpi::svdpi` interface target for `add_subdirectory` / FetchContent

## Getting started

### Standalone

Copy the headers and add the project root to your include path:

```cpp
#include <svdpi.h>       // DPI
#include <vpi_user.h>    // VPI (PLI) — IEEE 1364/1800 base API
#include <sv_vpi_user.h> // VPI plus SystemVerilog extensions
```

`sv_vpi_user.h` includes `vpi_user.h` for you; include it alone if you only need the base VPI API.

### CMake

```cmake
# via add_subdirectory
add_subdirectory(path/to/svdpi)
target_link_libraries(my_app PRIVATE svdpi::svdpi)

# or via FetchContent
include(FetchContent)
FetchContent_Declare(svdpi
    GIT_REPOSITORY https://github.com/kpskp77/svdpi.git
    GIT_TAG        main
)
FetchContent_MakeAvailable(svdpi)
target_link_libraries(my_app PRIVATE svdpi::svdpi)
```

To install the headers and export CMake package targets:

```bash
cmake -S . -B build
cmake --install build --prefix /usr/local   # installs all four headers to include/ and svdpiTargets.cmake to lib/cmake/svdpi
```

## References

- [IEEE Std 1800-2023](https://ieeexplore.ieee.org/document/10458102) — clause 35, "Direct programming interface"; clause 36, "Programming language interface (PLI/VPI)"
- [Accellera SystemVerilog](https://www.accellera.org/downloads/standards/systemverilog)
