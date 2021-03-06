#
# leptonica
#

###############################################################################
#
# cmake settings
#
###############################################################################

cmake_minimum_required(VERSION 2.8.11)

# In-source builds are disabled.
if (${CMAKE_SOURCE_DIR} STREQUAL ${CMAKE_BINARY_DIR})
    message(FATAL_ERROR
        "CMake generation is not possible within the source directory!"
        "\n Remove the CMakeCache.txt file and try again from another folder, e.g.:"
        "\n "
        "\n rm CMakeCache.txt"
        "\n mkdir build"
        "\n cd build"
        "\n cmake .."
    )
endif()

set(CMAKE_MODULE_PATH "${CMAKE_MODULE_PATH};${CMAKE_SOURCE_DIR}/cmake")

set(EXECUTABLE_OUTPUT_PATH "${CMAKE_BINARY_DIR}/bin")
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY "${EXECUTABLE_OUTPUT_PATH}")

# Use solution folders.
set_property(GLOBAL PROPERTY USE_FOLDERS ON)
set_property(GLOBAL PROPERTY PREDEFINED_TARGETS_FOLDER "CMake Targets")

###############################################################################
#
# project settings
#
###############################################################################

project(leptonica C CXX)

set(VERSION_MAJOR 1)
set(VERSION_MINOR 73)
set(VERSION_PLAIN ${VERSION_MAJOR}.${VERSION_MINOR})

find_package(GIF)
find_package(JPEG)
find_package(PNG)
find_package(TIFF)
find_package(ZLIB)

find_package(PkgConfig)

if (PKG_CONFIG_FOUND)
pkg_check_modules(WEBP libwebp)
pkg_check_modules(JP2K libopenjp2)
endif()

if(TIFF_FOUND)
include_directories(${TIFF_INCLUDE_DIRS})
endif()
if(PNG_FOUND)
include_directories(${PNG_INCLUDE_DIRS})
endif()
if(JPEG_FOUND)
include_directories(${JPEG_INCLUDE_DIR})
endif()
if(ZLIB_FOUND)
include_directories(${ZLIB_INCLUDE_DIRS})
endif()

###############################################################################
#
# compiler and linker
#
###############################################################################
option(BUILD_SHARED "Build Shared" ON)
if(BUILD_SHARED)
	set(LIBRARY_TYPE SHARED)
else()
	set(STATIC 1)
	if (STATIC)
		set(LIBRARY_TYPE STATIC)
	endif()
endif()

if (WIN32)
    if (MSVC)
        set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} /W1")
        set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} /MP")
    endif()
endif()

###############################################################################
#
# configure
#
###############################################################################

set(AUTOCONFIG_SRC ${CMAKE_BINARY_DIR}/config_auto.h.in)
set(AUTOCONFIG ${CMAKE_BINARY_DIR}/src/config_auto.h)

include(Configure)

configure_file(${AUTOCONFIG_SRC} ${AUTOCONFIG} @ONLY)

set(INCLUDE_DIR ${CMAKE_SOURCE_DIR}/src)

configure_file(
    ${CMAKE_SOURCE_DIR}/cmake/templates/LeptonicaConfig-version.cmake.in
    ${CMAKE_BINARY_DIR}/LeptonicaConfig-version.cmake @ONLY)
#configure_file(
#    ${CMAKE_SOURCE_DIR}/cmake/templates/LeptonicaConfig.cmake.in
#    ${CMAKE_BINARY_DIR}/LeptonicaConfig.cmake @ONLY)
configure_file(
    ${CMAKE_SOURCE_DIR}/cmake/templates/LeptonicaConfigInstall.cmake.in
    ${CMAKE_BINARY_DIR}/LeptonicaConfig.cmake @ONLY)

###############################################################################
#
# build
#
###############################################################################

add_definitions(-DHAVE_CONFIG_H)

include_directories(${CMAKE_CURRENT_SOURCE_DIR}/src)
include_directories(${CMAKE_CURRENT_BINARY_DIR}/src)

if (BUILD_PROG)
add_subdirectory(prog)
endif()

add_subdirectory(src)

###############################################################################
