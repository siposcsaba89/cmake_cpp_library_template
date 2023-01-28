set(EXAMPLE_NAMES example)

foreach(EXAMPLE ${EXAMPLE_NAMES})
    add_executable(${EXAMPLE} example.cpp)
    target_link_libraries(${EXAMPLE} PRIVATE library::library)
    target_include_directories(${EXAMPLE} PRIVATE ${CMAKE_CURRENT_SOURCE_DIR})
    set_target_properties(${EXAMPLE} PROPERTIES
        FOLDER examples
        CXX_STANDARD 17
        CXX_STANDARD_REQUIRED TRUE
        DEBUG_POSTFIX _d
        RELWITHDEBINFO_POSTFIX _rd
    )
endforeach(EXAMPLE ${EXAMPLE_NAMES})
