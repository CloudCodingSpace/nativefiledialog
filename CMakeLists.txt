cmake_minimum_required(VERSION 3.5)
project(nfd)

# Initialize source file lists
set(CSRCS "")
set(CXXSRCS "")

# Platform-specific source files and dependencies
set(EXTRA_LIBS "")  # Initialize EXTRA_LIBS

if(WIN32)
    list(APPEND CXXSRCS "${CMAKE_CURRENT_SOURCE_DIR}/src/nfd_win.cpp")
    list(APPEND CSRCS "${CMAKE_CURRENT_SOURCE_DIR}/src/nfd_common.c")
    list(APPEND EXTRA_LIBS comdlg32)
elseif(APPLE)
    list(APPEND CSRCS "${CMAKE_CURRENT_SOURCE_DIR}/src/nfd_cocoa.m")
    list(APPEND CSRCS "${CMAKE_CURRENT_SOURCE_DIR}/src/nfd_common.c")
    find_library(COCOA_LIB Cocoa REQUIRED)
    find_library(FOUNDATION_LIB Foundation REQUIRED)
    list(APPEND EXTRA_LIBS ${COCOA_LIB} ${FOUNDATION_LIB})
elseif(UNIX AND NOT APPLE)
    list(APPEND CSRCS "${CMAKE_CURRENT_SOURCE_DIR}/src/nfd_gtk.c")
    list(APPEND CSRCS "${CMAKE_CURRENT_SOURCE_DIR}/src/nfd_common.c")
    find_package(PkgConfig REQUIRED)
    pkg_check_modules(GTK3 REQUIRED gtk+-3.0)
    include_directories(${GTK3_INCLUDE_DIRS})
    link_directories(${GTK3_LIBRARY_DIRS})
    add_definitions(${GTK3_CFLAGS_OTHER})
    list(APPEND EXTRA_LIBS ${GTK3_LIBRARIES})
endif()

# Define the library target
add_library(nfd STATIC ${CSRCS} ${CXXSRCS})

# Set include directories
target_include_directories(nfd PUBLIC "${CMAKE_CURRENT_SOURCE_DIR}/src/include")

# Link system libraries
target_link_libraries(nfd PRIVATE ${EXTRA_LIBS})