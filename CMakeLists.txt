# ----------------------------------------------------------------------------
#  Root CMake file for DfD Graph Cuts
# ----------------------------------------------------------------------------
#
# To perform an out of source build do the following on the command line:
# 1. navigate to the root project directory (in this case DfD2)
# 2. mkdir build
# 3. cd build
# 4. depending on your system (windows/linux) type the following
# Example CMake build line for windows & VS2015
# windows: cmake -G "Visual Studio 14 2015 Win64" -DUSE_AVX_INSTRUCTIONS=ON ..
# linux: cmake -DUSE_AVX_INSTRUCTIONS=ON ..
#
# 5. To compile:
# windows: cmake --build . --config Release
# linux: cmake --build . --config Release -- -j4
#


cmake_minimum_required(VERSION 2.8.12)

PROJECT(DFD_GC_Project)

set(exe_name "dfd_gc_ex")

# This looks for an environment variable called PLATFORM.  This can be used to point
# cmake to various locations based on the individual machines that the code is complied on
#set(PLATFORM "$ENV{PLATFORM}" CACHE INTERNAL "Copied from environment variable")
#message("Platform: " ${PLATFORM})
set(PLATFORM "MainGear")

if(${PLATFORM} STREQUAL "Laptop")
    set(COMMON_ROOT "D:/Common")
    set(DFD_COMMON_ROOT "D:/Projects/dfd_common")
elseif(${PLATFORM} STREQUAL "MainGear")
    set(COMMON_ROOT "~/Common")
    set(DFD_COMMON_ROOT "~/Projects/dfd_common")
elseif(${PLATFORM} STREQUAL "HPC")
    set(COMMON_ROOT "$HOME/Common")
    set(DFD_COMMON_ROOT "$HOME/Projects/dfd_common")
else()
    set(COMMON_ROOT "D:/Common")
    set(DFD_COMMON_ROOT "D:/Projects/dfd_common")
endif()

set(USE_AVX_INSTRUCTIONS ON CACHE BOOL "Use AVX instructions")
#include(${COMMON_ROOT}/cmake_utils/use_cpp_11.cmake)
#include(${COMMON_ROOT}/cmake_utils/use_sse_avx.cmake)


# look for any *.c and *.cpp files
file(GLOB_RECURSE SOURCES "src/*.c*" "${COMMON_ROOT}/src/getopt.c")

#set(dfd_includes "../common" "include" "src/gco-v3")
set(project_includes "include" "${COMMON_ROOT}/include" "${DFD_COMMON_ROOT}/include")

# Display the source files found
message(" ")
message("--------------------------------------------------------")
message("DfD Graph Cuts Project Source Files: ")
foreach(S ${SOURCES})
    message("  " ${S})
endforeach()
message("--------------------------------------------------------")
message(" ")


# find OpenCV
find_package(OpenCV REQUIRED)

if (OpenCV_FOUND)
    set(project_includes ${project_includes} ${OpenCV_INCLUDE_DIRS})

    message(" ")
    message("--------------------------------------------------------")
    message("DfD Graph Cuts Project Includes: ")
    foreach(S ${project_includes})
        message("  " ${S})
    endforeach()
    message("--------------------------------------------------------")
    message(" ")

    include_directories(${project_includes})
    add_executable(${exe_name} ${SOURCES})
    target_link_libraries(${exe_name} ${OpenCV_LIBS})

else()
    message("OpenCV not found, so we won't build the project.")
endif()

if(WIN32)
    # uncomment for MSVC and cmake greater than 3.6.0
    set(default_build_type "Release")
    if(MSVC AND (${CMAKE_VERSION} VERSION_GREATER "3.6.0"))
        target_compile_options(${exe_name} PRIVATE "/MP")
        set_property(DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR} PROPERTY VS_STARTUP_PROJECT ${exe_name})
    endif()

endif()

message(" ")
message(STATUS "Build Complete!")
message(" ")



