# This is partially written by AI to help with scikit-build-core integration
cmake_minimum_required(VERSION 3.15)
project(Zsmooth LANGUAGES C)

# Ensure Zig is found
find_program(ZIG_EXE NAMES zig python-zig)
if(NOT ZIG_EXE)
    message(FATAL_ERROR "Zig compiler not found. Please install 'ziglang' python package or zig system-wide.")
endif()

message(STATUS "Found Zig: ${ZIG_EXE}")

# Output directory for zig
set(ZIG_OUT_DIR "${CMAKE_CURRENT_BINARY_DIR}/zig-out")

# Determine build type for zig
if(CMAKE_BUILD_TYPE STREQUAL "Debug")
    set(ZIG_OPTIMIZE "Debug")
else()
    set(ZIG_OPTIMIZE "ReleaseFast")
endif()

# Define the custom command to run zig build
add_custom_command(
    OUTPUT "${ZIG_OUT_DIR}/zsmooth.dll" "${ZIG_OUT_DIR}/libzsmooth.so" "${ZIG_OUT_DIR}/libzsmooth.dylib"
    COMMAND ${ZIG_EXE} build -Doptimize=${ZIG_OPTIMIZE} --prefix "${ZIG_OUT_DIR}"
    WORKING_DIRECTORY "${CMAKE_CURRENT_SOURCE_DIR}"
    COMMENT "Building Zig project..."
)

# We define a dummy target that depends on the zig output
add_custom_target(zsmooth_build ALL
    DEPENDS "${ZIG_OUT_DIR}/zsmooth.dll" "${ZIG_OUT_DIR}/libzsmooth.so" "${ZIG_OUT_DIR}/libzsmooth.dylib"
)

# Installation logic
# scikit-build-core uses the install(TARGETS ...) or install(FILES ...) commands to determine what goes into the wheel.
# We need to install the compiled plugin into the 'vapoursynth/plugins' folder.

install(DIRECTORY "${ZIG_OUT_DIR}/lib/"
        DESTINATION "vapoursynth/plugins"
        FILES_MATCHING
        PATTERN "*.dll"
        PATTERN "*.so"
        PATTERN "*.dylib")

# On Windows, zig put DLLs in 'bin/' instead of 'lib/' depending on how it's configured
install(DIRECTORY "${ZIG_OUT_DIR}/bin/"
        DESTINATION "vapoursynth/plugins"
        FILES_MATCHING
        PATTERN "*.dll")
