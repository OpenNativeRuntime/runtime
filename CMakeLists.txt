
cmake_minimum_required(VERSION 3.25)

project(
    OpenNativeRuntime
    VERSION 0.1.0
    DESCRIPTION "A cross-platform native application runtime"
    LANGUAGES CXX
)

set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

include(CTest)

option(ONR_BUILD_TESTS "Build OpenNativeRuntime tests" ON)
option(ONR_ENABLE_WARNINGS "Enable additional compiler warnings" ON)

add_library(onr_build_options INTERFACE)

target_compile_features(onr_build_options INTERFACE cxx_std_20)

if(ONR_ENABLE_WARNINGS)
    if(CMAKE_CXX_COMPILER_ID MATCHES "GNU|Clang")
        target_compile_options(
            onr_build_options
            INTERFACE
            -Wall
            -Wextra
            -Wpedantic
            -Wconversion
            -Wsign-conversion
        )
    elseif(MSVC)
        target_compile_options(
            onr_build_options
            INTERFACE
            /W4
            /permissive-
        )
    endif()
endif()

# Runtime targets will be added as implementation begins.
# Keep platform-specific dependencies isolated in their adapters.

if(ONR_BUILD_TESTS AND BUILD_TESTING)
    # Add unit and integration test subdirectories here
    # when their first targets are implemented.
endif()
