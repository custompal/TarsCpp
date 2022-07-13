cmake_minimum_required(VERSION 2.8)

project(Demo-DemoServer)

option(TARS_MYSQL "option for mysql" ON)
option(TARS_SSL "option for ssl" OFF)
option(TARS_HTTP2 "option for http2" OFF)

if(WIN32)
    include (c:\\tars\\cpp\\makefile\\tars-tools.cmake)
else()
    include (/usr/local/tars/cpp/makefile/tars-tools.cmake)
endif()

####you can: cd build; cmake .. -DTARS_WEB_HOST={your web host}
set(TARS_WEB_HOST "" CACHE STRING "set web host")
IF (TARS_WEB_HOST STREQUAL "")
	set(TARS_WEB_HOST "http://tars.test.com")
ENDIF ()

if(WIN32)
    include_directories(c:/tars/cpp/thirdparty/include)
    link_directories(c:/tars/cpp/thirdparty/lib)
else()
    include_directories(/usr/local/tars/cpp/thirdparty/include)
    link_directories(/usr/local/tars/cpp/thirdparty/lib)
endif()

#include_directories(/home/tarsprotol/App/OtherServer)

add_subdirectory(src)

#target_link_libraries(mysqlclient ssl crypto nghttp2-static)
