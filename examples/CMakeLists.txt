# Copyright (c) 2017 The Chromium Embedded Framework Authors. All rights
# reserved. Use of this source code is governed by a BSD-style license that
# can be found in the LICENSE file.

#
# Shared resources. These are included by the individual executable targets so
# the paths must be relative to those directories.
#

# Shared Windows resources (version information, icons).
set(SHARED_RESOURCES_SRCS
  ../shared/win/big.ico
  ../shared/win/resource.h
  ../shared/win/small.ico
  )

# Manifest files used for executables.
set(SHARED_EXE_MANIFEST ../shared/win/shared.exe.manifest)
set(SHARED_COMPATIBILITY_MANIFEST ../shared/win/compatibility.manifest)

#
# Shared configuration.
#
# Support nice project/target organization in Visual Studio and Xcode.
if(OS_MACOSX OR OS_WINDOWS)
  # Enable the creation of project folders.
  set_property(GLOBAL PROPERTY USE_FOLDERS ON)

  # Group target contents by directory.
  macro(SET_EXAMPLE_SOURCE_GROUPS srcs)
    foreach(FILE ${srcs}) 
      # Get the absolute directory path.
      get_filename_component(ABS_FILE "${FILE}" ABSOLUTE)
      get_filename_component(PARENT_DIR "${ABS_FILE}" DIRECTORY)

      # Remove the common directory prefix. What remains in the group.
      string(REPLACE "${CMAKE_SOURCE_DIR}/examples/" "" GROUP "${PARENT_DIR}")

      # Convert to Windows slashes.
      string(REPLACE "/" "\\" GROUP "${GROUP}")

      source_group("${GROUP}" FILES "${FILE}")
    endforeach()
  endmacro()
endif()

# Set the binary output directory.
set(EXAMPLE_TARGET_OUT_DIR "${CMAKE_BINARY_DIR}/")

# Set properties common to all example targets.
macro(SET_EXAMPLE_PROPERTIES target)
  # Output all binaries to the configuration-specific build directory.
  set_target_properties(${target} PROPERTIES
                        ARCHIVE_OUTPUT_DIRECTORY "${EXAMPLE_TARGET_OUT_DIR}"
                        RUNTIME_OUTPUT_DIRECTORY "${EXAMPLE_TARGET_OUT_DIR}"
                        LIBRARY_OUTPUT_DIRECTORY "${EXAMPLE_TARGET_OUT_DIR}")

  if(OS_MACOSX OR OS_WINDOWS)
    # Place the target in the "examples" folder in Visual Studio and Xcode.
    set_property(TARGET ${target} PROPERTY FOLDER "examples")
  endif()
endmacro()

# Set properties on an example library target.
macro(SET_EXAMPLE_LIBRARY_TARGET_PROPERTIES target)
  SET_LIBRARY_TARGET_PROPERTIES(${target})
  SET_EXAMPLE_PROPERTIES(${target})
endmacro()

# Set properties on an example executable target.
macro(SET_EXAMPLE_EXECUTABLE_TARGET_PROPERTIES target)
  SET_EXECUTABLE_TARGET_PROPERTIES(${target})
  SET_EXAMPLE_PROPERTIES(${target})

  # Add the custom Windows manifest files to the executable.
  add_custom_command(
    TARGET ${target}
    POST_BUILD
    COMMAND "mt.exe" -nologo
            -manifest \"${CMAKE_CURRENT_SOURCE_DIR}/${SHARED_EXE_MANIFEST}\"
                      \"${CMAKE_CURRENT_SOURCE_DIR}/${SHARED_COMPATIBILITY_MANIFEST}\"
            -outputresource:"${EXAMPLE_TARGET_OUT_DIR}/${target}.exe"\;\#1
    COMMENT "Adding manifest..."
    )
endmacro()


#
# Target configuration.
#

# Static library shared by all example executable targets.
add_subdirectory(shared)

# Example executable targets.
add_subdirectory(minimal)
