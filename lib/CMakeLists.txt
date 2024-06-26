# Copyright 2011,2012,2016,2018,2019 Free Software Foundation, Inc.
#
# This file was generated by gr_modtool, a tool from the GNU Radio framework
# This file is a part of gr-RigExpert
#
# SPDX-License-Identifier: GPL-3.0-or-later
#

########################################################################
# Setup library
########################################################################
include(GrPlatform) #define LIB_SUFFIX

list(APPEND RigExpert_sources
    fobos_sdr_impl.cc ../fobos/fobos.c
)




set(RigExpert_sources "${RigExpert_sources}" PARENT_SCOPE)
if(NOT RigExpert_sources)
    MESSAGE(STATUS "No C++ sources... skipping lib/")
    return()
endif(NOT RigExpert_sources)


find_package(PkgConfig)

if(PKG_CONFIG_FOUND)
    pkg_check_modules(LIBUSB libusb-1.0 IMPORTED_TARGET)
    if(LIBUSB_LINK_LIBRARIES)
        set(LIBUSB_LIBRARIES "${LIBUSB_LINK_LIBRARIES}")
    endif()
endif()

message(>>> "OS: " ${CMAKE_SYSTEM_NAME})
message(>>> "LIBUSB_LIBRARIES: " ${LIBUSB_LIBRARIES})
message(>>> "LIBUSB_INCLUDE_DIRS: " ${LIBUSB_INCLUDE_DIRS})

add_library(gnuradio-RigExpert SHARED ${RigExpert_sources})
target_link_libraries(gnuradio-RigExpert ${LIBUSB_LIBRARIES})
target_link_libraries(gnuradio-RigExpert gnuradio::gnuradio-runtime)
target_include_directories(gnuradio-RigExpert
    PUBLIC $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/..>
    PUBLIC $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/../include>
    PUBLIC ${LIBUSB_INCLUDE_DIRS}
    PUBLIC $<INSTALL_INTERFACE:include>
  )
  
  
set_target_properties(gnuradio-RigExpert PROPERTIES DEFINE_SYMBOL "gnuradio_RigExpert_EXPORTS")

if(APPLE)
    set_target_properties(gnuradio-RigExpert PROPERTIES
        INSTALL_NAME_DIR "${CMAKE_INSTALL_PREFIX}/lib"
    )
endif(APPLE)

########################################################################
# Install built library files
########################################################################
include(GrMiscUtils)
GR_LIBRARY_FOO(gnuradio-RigExpert)

########################################################################
# Print summary
########################################################################
message(STATUS "Using install prefix: ${CMAKE_INSTALL_PREFIX}")
message(STATUS "Building for version: ${VERSION} / ${LIBVER}")

########################################################################
# Build and register unit test
########################################################################
include(GrTest)

# If your unit tests require special include paths, add them here
#include_directories()
# List all files that contain Boost.UTF unit tests here
list(APPEND test_RigExpert_sources
)
# Anything we need to link to for the unit tests go here
list(APPEND GR_TEST_TARGET_DEPS gnuradio-RigExpert)

if(NOT test_RigExpert_sources)
    MESSAGE(STATUS "No C++ unit tests... skipping")
    return()
endif(NOT test_RigExpert_sources)

foreach(qa_file ${test_RigExpert_sources})
    GR_ADD_CPP_TEST("RigExpert_${qa_file}"
        ${CMAKE_CURRENT_SOURCE_DIR}/${qa_file}
    )
endforeach(qa_file)
