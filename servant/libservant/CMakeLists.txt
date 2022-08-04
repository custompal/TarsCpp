# if(TARS_OPENTRACKING)
# include_directories(${PROJECT_SOURCE_DIR} ${OPENTRACKING_INC})
# else()
#include_directories(${PROJECT_SOURCE_DIR})
# endif()

set(LIBRARY_OUTPUT_PATH ${PROJECT_BINARY_DIR}/lib)

aux_source_directory(. DIR_SRCS)

add_library(tarsservant ${DIR_SRCS})

add_dependencies(tarsservant COPY-SERVENT-TARS)

#重新定义__FILE__
define_filename_macro(tarsservant)

target_compile_options(tarsservant
    PRIVATE ${COMPILE_OPTIONS_DEFAULT}
)
target_link_libraries(tarsservant
    PUBLIC tarsutil
)
target_include_directories(tarsservant
    PUBLIC "$<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/..>"
)

install(TARGETS tarsservant
        LIBRARY DESTINATION lib
        ARCHIVE DESTINATION lib)
