set(LIBRARY_OUTPUT_PATH ${PROJECT_BINARY_DIR}/lib)

set(DIR_INCLUDES ${CMAKE_CURRENT_SOURCE_DIR}/../include)
IF(WIN32)
list(APPEND DIR_INCLUDES ${CMAKE_CURRENT_SOURCE_DIR}/epoll_windows)
aux_source_directory(epoll_windows/src. DIR_SRCS)
ENDIF()

aux_source_directory(. DIR_SRCS)

parse_asm(JUMP_SRC MAKE_SRC)

message("----------------------------------------------------")
message("CMAKE_SYSTEM_NAME:         ${CMAKE_SYSTEM_NAME}")
message("CMAKE_SYSTEM_PROCESSOR:    ${CMAKE_SYSTEM_PROCESSOR}")
message("CMAKE_CXX_COMPILER_ID:     ${CMAKE_CXX_COMPILER_ID}")
message("ABI_STR:                   ${ABI_STR}")
message("BF_STR:                    ${BF_STR}")
message("CPU_STR:                   ${CPU_STR}")
message("JUMP_SRC:                  ${JUMP_SRC}")
message("MAKE_SRC:                  ${MAKE_SRC}")
message("CMAKE_C_SIZEOF_DATA_PTR:   ${CMAKE_C_SIZEOF_DATA_PTR}")
message("----------------------------------------------------")

list(APPEND DIR_SRCS ${JUMP_SRC})
list(APPEND DIR_SRCS ${MAKE_SRC})

add_library(tarsutil ${DIR_SRCS})
add_dependencies(tarsutil 3rdparty)

#重新定义__FILE__
define_filename_macro(tarsutil)

set(COMPILE_DEFINITIONS ${TARSCPP_COMPILE_DEFINITIONS})
set(LINK_LIBRARIES ${TARSCPP_LINK_LIBRARIES})
target_compile_definitions(tarsutil
    PUBLIC ${COMPILE_DEFINITIONS}
)
target_compile_options(tarsutil
    PRIVATE ${DEFAULT_COMPILE_OPTIONS}
)
target_link_libraries(tarsutil
    PUBLIC ${LINK_LIBRARIES}
)
target_include_directories(tarsutil
    PUBLIC "$<BUILD_INTERFACE:${DIR_INCLUDES}>"
)

install(TARGETS tarsutil
        LIBRARY DESTINATION lib
        ARCHIVE DESTINATION lib)

IF(WIN32)
install(DIRECTORY epoll_windows/sys
        DESTINATION include)
ENDIF()        
