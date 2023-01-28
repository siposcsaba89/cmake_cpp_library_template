cmake_minimum_required(VERSION 3.23)
set_property(GLOBAL PROPERTY USE_FOLDERS ON)
# change this
project(library VERSION 0.0.1)

if (${CMAKE_SOURCE_DIR} STREQUAL ${CMAKE_BINARY_DIR})
    message(FATAL_ERROR "CMake source dir cannot be equal to CMAKE_BINARY_DIR! Please create a separate folder for generating build files!")
endif()

option(BUILD_EXAMPLES "Build polydist examples" ON)


set(PUB_HDRS
    include/library/library.h
)
    
set(SRCS
    src/library.cpp
)
source_group(TREE ${CMAKE_CURRENT_SOURCE_DIR} FILES ${SRCS} ${PUB_HDRS})


add_library(${PROJECT_NAME} ${PUB_HDRS} ${SRCS})
add_library(${PROJECT_NAME}::${PROJECT_NAME} ALIAS ${PROJECT_NAME})

target_include_directories(${PROJECT_NAME} PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/src>
    $<BUILD_INTERFACE:${CMAKE_CURRENT_BINARY_DIR}/gen/${PROJECT_NAME}>
    $<INSTALL_INTERFACE:include/${PROJECT_NAME}>
)

target_compile_options(${PROJECT_NAME} PRIVATE $<$<CXX_COMPILER_ID:MSVC>:/MP>)

set_target_properties(${PROJECT_NAME} PROPERTIES
    CXX_STANDARD 17
    CXX_STANDARD_REQUIRED TRUE
    FOLDER libraries
    DEBUG_POSTFIX _d
    RELWITHDEBINFO_POSTFIX _rd
    POSITION_INDEPENDENT_CODE ON
    CXX_VISIBILITY_PRESET hidden
    VISIBILITY_INLINES_HIDDEN ON
)

include(CMakePackageConfigHelpers)
write_basic_package_version_file(
    "${CMAKE_CURRENT_BINARY_DIR}/gen/${PROJECT_NAME}-config-version.cmake"
    VERSION ${CMAKE_PROJECT_VERSION}
    COMPATIBILITY SameMajorVersion
)
include(GenerateExportHeader)
generate_export_header(${PROJECT_NAME} EXPORT_FILE_NAME 
        ${CMAKE_CURRENT_BINARY_DIR}/gen/${PROJECT_NAME}/${PROJECT_NAME}/${PROJECT_NAME}_export.h)

configure_file(cmake/config.cmake.in ${PROJECT_NAME}-config.cmake @ONLY)

include(GNUInstallDirs)

install(TARGETS ${PROJECT_NAME} EXPORT ${PROJECT_NAME}-targets  
    ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR} COMPONENT ${PROJECT_NAME}_Development
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR} COMPONENT ${PROJECT_NAME}_RunTime NAMELINK_COMPONENT ${PROJECT_NAME}_Development
    RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR} COMPONENT ${PROJECT_NAME}_RunTime)

install(
    FILES 
        ${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}-config.cmake 
        ${CMAKE_CURRENT_BINARY_DIR}/gen/${PROJECT_NAME}-config-version.cmake
    DESTINATION 
        share/${PROJECT_NAME}
)
    
install(EXPORT ${PROJECT_NAME}-targets NAMESPACE ${PROJECT_NAME}:: DESTINATION share/${PROJECT_NAME})

install(
    DIRECTORY
        include/
    DESTINATION
        include/${PROJECT_NAME}
)

install(
    FILES
        ${CMAKE_CURRENT_BINARY_DIR}/gen/${PROJECT_NAME}/${PROJECT_NAME}/${PROJECT_NAME}_export.h
    DESTINATION
        include/${PROJECT_NAME}/${PROJECT_NAME}
)

if (BUILD_EXAMPLES)
    add_subdirectory(examples)
endif()

include(CTest)

if (BUILD_TESTING AND NOT BUILD_SHARED_LIBS)
    enable_testing()
    add_subdirectory(tests)
endif()
