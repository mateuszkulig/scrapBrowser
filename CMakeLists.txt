# Copyright (c) 2016 The Chromium Embedded Framework Authors. All rights
# reserved. Use of this source code is governed by a BSD-style license that
# can be found in the LICENSE file.

# For VS2022 and Xcode 12+ support.
cmake_minimum_required(VERSION 3.21)

# Only generate Debug and Release configuration types.
set(CMAKE_CONFIGURATION_TYPES Debug Release)

# Project name.
project(cef)

# Use folders in the resulting project files.
set_property(GLOBAL PROPERTY USE_FOLDERS ON)

#
# CEF configuration.
#

# Determine the platform.
if("${CMAKE_CXX_COMPILER_ARCHITECTURE_ID}" STREQUAL "ARM64")
  set(CEF_PLATFORM "windowsarm64")
elseif(CMAKE_SIZEOF_VOID_P MATCHES 8)
  set(CEF_PLATFORM "windows64")
else()
  set(CEF_PLATFORM "windows32")
endif()


set(CEF_ROOT "C:/home/programy/cef" CACHE INTERNAL "CEF_ROOT")
# Add the CEF binary distribution's cmake/ directory to the module path.
set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "C:/home/programy/cef/cmake")

# Load the CEF configuration (executes FindCEF.cmake).
find_package(CEF REQUIRED)


#
# Python configuration.
#

# Support specification of the Python executable path via the command-line.
if(DEFINED ENV{PYTHON_EXECUTABLE})
  file(TO_CMAKE_PATH "$ENV{PYTHON_EXECUTABLE}" PYTHON_EXECUTABLE)
endif()

if(NOT PYTHON_EXECUTABLE)
  unset(PYTHON_EXECUTABLE)

  # Find the python interpreter.
  cmake_policy(SET CMP0148 OLD)
  find_package(PythonInterp)

  if(NOT ${PYTHONINTERP_FOUND})
    message(FATAL_ERROR "A Python installation is required. Set the "
                        "PYTHON_EXECUTABLE environment variable to explicitly "
                        "specify the Python executable path.")
  endif()
endif()

message(STATUS "Using Python: ${PYTHON_EXECUTABLE}")

#
# Target configuration.
#

# Include the libcef_dll_wrapper target (executes libcef_dll/CMakeLists.txt).
add_subdirectory(${CEF_LIBCEF_DLL_WRAPPER_PATH} libcef_dll_wrapper)

# Allow includes relative to the current source directory.
include_directories(${CMAKE_CURRENT_SOURCE_DIR})

add_subdirectory(examples)

# Display configuration settings.
PRINT_CEF_CONFIG()
