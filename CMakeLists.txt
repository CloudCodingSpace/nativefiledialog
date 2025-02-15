cmake_minimum_required(VERSION 3.5.0)
project(nfd)

# Collect common source files
file(GLOB CSRCS "${CMAKE_CURRENT_SOURCE_DIR}/src/*.c")
file(GLOB CXXSRCS "${CMAKE_CURRENT_SOURCE_DIR}/src/*.cpp")

# Platform-specific source files and dependencies
if(WIN32)
    list(APPEND CSRCS "${CMAKE_CURRENT_SOURCE_DIR}/src/nfd_win.cpp")
    target_link_libraries(nfd PRIVATE comdlg32)
elseif(APPLE)
    list(APPEND CSRCS "${CMAKE_CURRENT_SOURCE_DIR}/src/nfd_cocoa.m")
    find_library(COCOA_LIB Cocoa REQUIRED)
    find_library(FOUNDATION_LIB Foundation REQUIRED)
    list(APPEND EXTRA_LIBS ${COCOA_LIB} ${FOUNDATION_LIB})
elseif(UNIX AND NOT APPLE)
    list(APPEND CSRCS "${CMAKE_CURRENT_SOURCE_DIR}/src/nfd_gtk.c")
    find_package(PkgConfig REQUIRED)
    pkg_check_modules(GTK3 REQUIRED gtk+-3.0)
    include_directories(${GTK3_INCLUDE_DIRS})
    link_directories(${GTK3_LIBRARY_DIRS})
    add_definitions(${GTK3_CFLAGS_OTHER})
    list(APPEND EXTRA_LIBS ${GTK3_LIBRARIES})
endif()

# Define the library target
add_library(nfd ${CSRCS} ${CXXSRCS})

# Set include directories
target_include_directories(nfd PUBLIC "${CMAKE_CURRENT_SOURCE_DIR}/src/include")

# Link system libraries
target_link_libraries(nfd PRIVATE ${EXTRA_LIBS})
