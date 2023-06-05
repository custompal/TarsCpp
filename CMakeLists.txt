cmake_minimum_required(VERSION 3.2)

project(tars-cpp)

#-------------------------------------------------------------------

if("${TARS_CPP_COMMON}" STREQUAL "")

    set(TARS_CPP_COMMON "1")

    include(${PROJECT_SOURCE_DIR}/../../cmake/common.cmake)

    include("${PROJECT_SOURCE_DIR}/../../cmake/3rdparty.cmake")

    include("${PROJECT_SOURCE_DIR}/cmake/BuildTarsCpp.cmake")

    include("${PROJECT_SOURCE_DIR}/cmake/ParseAsm.cmake")

endif()

option(TARS_MYSQL "option for mysql" ON)
option(TARS_SSL "option for ssl" ON)
option(TARS_HTTP2 "option for http2" OFF)
option(TARS_PROTOBUF "option for protocol" OFF)
option(TARS_GPERF "option for gperf" OFF)
option(ENABLE_CONSOLELOG_COLOR "option for console log color" OFF)

set(ONLY_LIB "ON" CACHE STRING "enable example" FORCE)

#-------------------------------------------------------------
message("----------------------------------------------------")
message(STATUS "CMAKE_SOURCE_DIR:          ${CMAKE_SOURCE_DIR}")
message(STATUS "CMAKE_BINARY_DIR:          ${CMAKE_BINARY_DIR}")
message(STATUS "PROJECT_SOURCE_DIR:        ${PROJECT_SOURCE_DIR}")
message(STATUS "CMAKE_BUILD_TYPE:          ${CMAKE_BUILD_TYPE}")
message(STATUS "PLATFORM:                  ${PLATFORM}")
message(STATUS "CMAKE_INSTALL_PREFIX:      ${CMAKE_INSTALL_PREFIX}")
message(STATUS "BIN:                       ${CMAKE_RUNTIME_OUTPUT_DIRECTORY}")
message(STATUS "TARS2CPP:                  ${TARS2CPP}")
#message(STATUS "TARS_OPENTRACKING:        ${TARS_OPENTRACKING}")
message(STATUS "ONLY_LIB:                  ${ONLY_LIB}")

#-------------------------------------------------------------
#tarscpp依赖
update_cached_list(TARSCPP_COMPILE_DEFINITIONS ${LIST_COMPILE_DEFINITIONS})
update_cached_list(TARSCPP_LINK_LIBRARIES ${LIST_LINK_LIBRARIES})

if (ENABLE_CONSOLELOG_COLOR)
    update_cached_list(TARSCPP_COMPILE_DEFINITIONS ENABLE_CONSOLELOG_COLOR)
endif ()

# set(major "")
# set(minor "")
# set(patch "")
# get_version(major minor patch)

# set(TARS_VERSION ${major}.${minor}.${patch})
set(TARS_VERSION "3.0.14")
update_cached_list(TARSCPP_COMPILE_DEFINITIONS TARS_VERSION="${TARS_VERSION}")
message("TARS_VERSION:              ${TARS_VERSION}")

if (TARS_MYSQL)
    include_directories(SYSTEM ${MYSQL_INC_DIR})
    update_cached_list(TARSCPP_COMPILE_DEFINITIONS TARS_MYSQL=1)
    update_cached_list(TARSCPP_LINK_LIBRARIES ${LIB_MYSQL})
endif ()
if (TARS_SSL)
    include_directories(SYSTEM ${SSL_INC_DIR})
    update_cached_list(TARSCPP_COMPILE_DEFINITIONS TARS_SSL=1)
    update_cached_list(TARSCPP_LINK_LIBRARIES ${LIB_OPENSSL})
endif ()
if (TARS_HTTP2)
    include_directories(SYSTEM ${NGHTTP2_INC_DIR})
    update_cached_list(TARSCPP_COMPILE_DEFINITIONS TARS_HTTP2=1)
    update_cached_list(TARSCPP_LINK_LIBRARIES ${LIB_HTTP2})
endif ()
if (TARS_PROTOBUF)
    include_directories(SYSTEM ${PROTOBUF_INC_DIR})
    update_cached_list(TARSCPP_COMPILE_DEFINITIONS TARS_PROTOBUF=1)
    update_cached_list(TARSCPP_LINK_LIBRARIES ${LIB_PROTOBUF})
endif ()
if (TARS_GPERF)
    include_directories(SYSTEM ${GPERF_INC_DIR})
    update_cached_list(TARSCPP_COMPILE_DEFINITIONS TARS_GPERF=1)
    update_cached_list(TARSCPP_LINK_LIBRARIES ${LIB_TCMALLOC_PROFILER})
endif ()

#-------------------------------------------------------------------

set(CLEAR_INCLUDE "clear-install.cmake")
FILE(WRITE ${CLEAR_INCLUDE} "EXECUTE_PROCESS(COMMAND ${CMAKE_COMMAND} -E remove_directory ${CMAKE_INSTALL_PREFIX}/include)\n")
install(SCRIPT ${CLEAR_INCLUDE})

#-------------------------------------------------------------------

#include_directories(${PROJECT_SOURCE_DIR}/util/include)

#include_directories(${PROJECT_SOURCE_DIR}/servant)
#include_directories(${PROJECT_SOURCE_DIR}/servant/protocol)

IF(WIN32)
#include_directories(${PROJECT_SOURCE_DIR}/util/src/epoll_windows)
ENDIF()

add_subdirectory(util)
add_subdirectory(tools)
add_subdirectory(servant)

IF (NOT ${ONLY_LIB})
add_subdirectory(examples)
add_subdirectory(unit-test)
ENDIF()

#工程分组
if (USE_SOLUTION_FOLDERS)
    set(TARSCPP_PROJECT tarsutil tarsservant)
    list(APPEND TARSCPP_PROJECT tars2android)
    list(APPEND TARSCPP_PROJECT tars2c)
    list(APPEND TARSCPP_PROJECT tars2case)
    list(APPEND TARSCPP_PROJECT tars2cpp)
    list(APPEND TARSCPP_PROJECT tars2cs)
    list(APPEND TARSCPP_PROJECT tars2node)
    list(APPEND TARSCPP_PROJECT tars2oc)
    list(APPEND TARSCPP_PROJECT tars2php)
    list(APPEND TARSCPP_PROJECT tars2python)
    list(APPEND TARSCPP_PROJECT tarsmerge)
    list(APPEND TARSCPP_PROJECT tarsparse)
    list(APPEND TARSCPP_PROJECT TUP-TARS)
    list(APPEND TARSCPP_PROJECT FRAMEWORK-PROTOCOL)
    list(APPEND TARSCPP_PROJECT COPY-SERVENT-TARS)
    foreach (PROJECT ${TARSCPP_PROJECT})
        set_property(TARGET ${PROJECT} PROPERTY FOLDER "tarscpp")
    endforeach ()
endif ()
