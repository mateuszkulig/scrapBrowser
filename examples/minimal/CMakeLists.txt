# Copyright (c) 2017 The Chromium Embedded Framework Authors. All rights
# reserved. Use of this source code is governed by a BSD-style license that
# can be found in the LICENSE file.

#
# Source files.
#

# Main executable sources.
set(EXAMPLE_SRCS
  app_browser_minimal.cc
  client_minimal.cc
  client_minimal.h
  main_minimal.cc
  )
set(EXAMPLE_SRCS_WINDOWS
  resource.h
  )
APPEND_PLATFORM_SOURCES(EXAMPLE_SRCS)
  

# On Windows and Linux the same executable is used for all processes.
set(EXAMPLE_SRCS
  ${EXAMPLE_SRCS}
  app_other_minimal.cc
  app_renderer_minimal.cc
  )


# Main executable resources.
set(EXAMPLE_RESOURCES_SRCS
  )
set(EXAMPLE_RESOURCES_SRCS_WINDOWS
  # Resources that embed the *.html and *.png files in the executable.
  win/resource.rc
  )
APPEND_PLATFORM_SOURCES(EXAMPLE_RESOURCES_SRCS)

# On macOS and Windows include the shared resources.
set(EXAMPLE_RESOURCES_SRCS
  ${EXAMPLE_RESOURCES_SRCS}
  ${SHARED_RESOURCES_SRCS}
  )

#
# Shared configuration.
#

# Target executable names.
set(EXAMPLE_TARGET "minimal")

#
# Windows configuration.
#

# Add resources to the sources variable for convenience.
set(EXAMPLE_SRCS
  ${EXAMPLE_SRCS}
  ${EXAMPLE_RESOURCES_SRCS}
  )

# Create source groups for Visual Studio.
SET_EXAMPLE_SOURCE_GROUPS("${EXAMPLE_SRCS}")

# Executable target.
add_executable(${EXAMPLE_TARGET} WIN32 ${EXAMPLE_SRCS})
SET_EXAMPLE_EXECUTABLE_TARGET_PROPERTIES(${EXAMPLE_TARGET})
add_dependencies(${EXAMPLE_TARGET} shared)
target_link_libraries(${EXAMPLE_TARGET} shared)
