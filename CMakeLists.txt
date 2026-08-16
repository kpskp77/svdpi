cmake_minimum_required(VERSION 3.14)

project(svdpi
    VERSION 1.0.0
    DESCRIPTION "SystemVerilog DPI (Direct Programming Interface) header"
    LANGUAGES C CXX
)

# Header-only library, no sources to compile
add_library(svdpi INTERFACE)
add_library(svdpi::svdpi ALIAS svdpi)

target_include_directories(svdpi INTERFACE
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
    $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>
)

# The header is standard C/C++; C++11 is the minimum for C++ consumers
target_compile_features(svdpi INTERFACE cxx_std_11)
set_target_properties(svdpi PROPERTIES CXX_EXTENSIONS OFF)

# --- Installation ---
include(GNUInstallDirs)

install(TARGETS svdpi EXPORT svdpiTargets)
install(FILES svdpi.h DESTINATION ${CMAKE_INSTALL_INCLUDEDIR})
install(EXPORT svdpiTargets
    FILE svdpiTargets.cmake
    NAMESPACE svdpi::
    DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/svdpi
)
