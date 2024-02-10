if (NOT TARGET gtest)
    find_package(GTest REQUIRED CONFIG)
endif()

set(TESTS
    library_test
)

include(GoogleTest)
foreach(test ${TESTS})
    add_executable(${test} ${test}.cpp)
    target_include_directories(${test} PRIVATE ${CMAKE_CURRENT_SOURCE_DIR}/../src)
    target_link_libraries(${test} GTest::gtest GTest::gtest_main library::library)
    set_target_properties(${test} PROPERTIES
        FOLDER tests
        CXX_STANDARD 11
        CXX_STANDARD_REQUIRED TRUE
        DEBUG_POSTFIX _d
        RELWITHDEBINFO_POSTFIX _rd
    )
    gtest_discover_tests(${test})
endforeach(test ${TESTS})
