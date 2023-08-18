cmake_minimum_required(VERSION 3.12)

project(TSLogo
        VERSION 0.1
        DESCRIPTION "TSLogo is a terminal logo display program inside of a terminal at its startup."
        LANGUAGES CXX
        )




include(FetchContent)

FetchContent_Declare(ftxui
  GIT_REPOSITORY https://github.com/ArthurSonzogni/ftxui
  GIT_TAG v3.0.0
)

FetchContent_GetProperties(ftxui)
if(NOT ftxui_POPULATED)
  FetchContent_Populate(ftxui)
  add_subdirectory(${ftxui_SOURCE_DIR} ${ftxui_BINARY_DIR} EXCLUDE_FROM_ALL)
endif()

file(GLOB SOURCES "src/*.cpp")
add_executable(TSLogo main.cpp
                      ${SOURCES}
                      )
target_include_directories(TSLogo PRIVATE ${CMAKE_CURRENT_SOURCE_DIR}/include)
target_link_libraries(TSLogo PRIVATE ftxui::screen ftxui::dom ftxui::component)

add_subdirectory(tests)
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

enable_testing()

add_custom_target(runtest
    COMMAND ${CMAKE_CTEST_COMMAND}
    DEPENDS TSLogo # Change this to the actual target name
)

add_custom_target(
    format
    # format all files one by one
    

    COMMAND /usr/bin/clang-format -i ${CMAKE_SOURCE_DIR}/src/*.cpp -style=file
)

if(CMAKE_COMPILER_IS_GNUCXX OR CMAKE_CXX_COMPILER_ID MATCHES "Clang")
    target_compile_options(TSLogo PRIVATE
        -Wall                  # Enable all possible warnings
        -Wextra                # Enable extra warnings
        -Werror                # Treat warnings as errors
        -Wpedantic             # Strict ISO C++ compliance
        -pedantic-errors       # Treat pedantic warnings as errors
    )
endif()
