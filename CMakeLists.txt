cmake_minimum_required(VERSION 3.14)

project(svdpi
    VERSION 1.1.0
    DESCRIPTION "IEEE 1800 SystemVerilog DPI and VPI headers"
    LANGUAGES C CXX
)

# Header-only library, no sources to compile
add_library(svdpi INTERFACE)
add_library(svdpi::svdpi ALIAS svdpi)

# All headers are installed together: vpi_user.h includes vpi_compatibility.h,
# and sv_vpi_user.h includes vpi_user.h.
set(SVDPI_HEADERS
    svdpi.h
    vpi_user.h
    vpi_compatibility.h
    sv_vpi_user.h
)

target_include_directories(svdpi INTERFACE
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
    $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>
)

# The headers are standard C/C++; C++11 is the minimum for C++ consumers
target_compile_features(svdpi INTERFACE cxx_std_11)
set_target_properties(svdpi PROPERTIES CXX_EXTENSIONS OFF)

# --- Installation ---
include(GNUInstallDirs)

install(TARGETS svdpi EXPORT svdpiTargets)
install(FILES ${SVDPI_HEADERS} DESTINATION ${CMAKE_INSTALL_INCLUDEDIR})
install(EXPORT svdpiTargets
    FILE svdpiTargets.cmake
    NAMESPACE svdpi::
    DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/svdpi
)
