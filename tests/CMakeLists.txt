include(FetchContent)
FetchContent_Declare(
    googletest
    GIT_REPOSITORY https://github.com/google/googletest.git
    GIT_TAG release-1.12.1
)

FetchContent_GetProperties(googletest)
if(NOT googletest_POPULATED)
    FetchContent_Populate(googletest)
    add_subdirectory(${googletest_SOURCE_DIR} ${googletest_BINARY_DIR} EXCLUDE_FROM_ALL)
endif()

set(TESTS
    library_test
)

include(GoogleTest)
foreach(test ${TESTS})
    add_executable(${test} ${test}.cpp)
    target_include_directories(${test} PRIVATE ${CMAKE_CURRENT_SOURCE_DIR}/../src)
    target_link_libraries(${test} gtest gtest_main library::library)
    set_target_properties(${test} PROPERTIES
        FOLDER tests
        CXX_STANDARD 11
        CXX_STANDARD_REQUIRED TRUE
        DEBUG_POSTFIX _d
        RELWITHDEBINFO_POSTFIX _rd
    )
    gtest_discover_tests(${test})
endforeach(test ${TESTS})
