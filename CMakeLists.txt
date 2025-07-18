cmake_minimum_required(VERSION 3.15 FATAL_ERROR)
project(capstats C CXX)
include(cmake/CommonCMakeConfig.cmake)
include(cmake/RequireCXXStd.cmake)

file(STRINGS "${CMAKE_CURRENT_SOURCE_DIR}/VERSION" VERSION LIMIT_COUNT 1)
configure_file(${CMAKE_CURRENT_SOURCE_DIR}/version.cc.in
               ${CMAKE_CURRENT_BINARY_DIR}/version.cc)

########################################################################
## Dependency Configuration

find_package(PCAP REQUIRED)

include_directories(BEFORE
                    ${PCAP_INCLUDE_DIR}
)

########################################################################
## System Introspection

if (${CMAKE_SYSTEM_NAME} MATCHES "Linux")
    set(HAVE_LINUX true)
endif ()

configure_file(${CMAKE_CURRENT_SOURCE_DIR}/config.h.in
               ${CMAKE_CURRENT_BINARY_DIR}/config.h)
include_directories(BEFORE ${CMAKE_CURRENT_BINARY_DIR})

########################################################################
## Build Summary

if (CMAKE_BUILD_TYPE)
    string(TOUPPER ${CMAKE_BUILD_TYPE} BuildType)
endif ()

message(
    "\n=================|  capstats Build Summary  |==================="
    "\n"
    "\nInstall prefix:    ${CMAKE_INSTALL_PREFIX}"
    "\nDebug mode:        ${ENABLE_DEBUG}"
    "\n"
    "\nCXX:               ${CMAKE_CXX_COMPILER}"
    "\nCXXFLAGS:          ${CMAKE_CXX_FLAGS} ${CMAKE_CXX_FLAGS_${BuildType}}"
    "\nCPP:               ${CMAKE_CXX_COMPILER}"
    "\n"
    "\n================================================================\n"
)

########################################################################
## capstats target

add_executable(capstats
               capstats.cc
               ${CMAKE_CURRENT_BINARY_DIR}/version.cc)

target_link_libraries(capstats ${PCAP_LIBRARY})

install(TARGETS capstats DESTINATION bin)

include(UserChangedWarning)
