cmake_minimum_required(VERSION 3.16)

project(hsc_brkga LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

option(BRKGA_ENABLE_RANGECHECK "Enable BRKGA range checks" OFF)
option(BRKGA_ENABLE_README_WARNINGS "Enable the warning set recommended in README.txt" ON)

find_package(OpenMP REQUIRED COMPONENTS CXX)

add_library(brkga STATIC)
add_library(BRKGA::brkga ALIAS brkga)

target_sources(brkga
    PRIVATE
        src/Population.cpp
    PUBLIC
        FILE_SET HEADERS
        BASE_DIRS
            ${CMAKE_CURRENT_SOURCE_DIR}/include
        FILES
            include/BRKGA.h
            include/MTRand.h
            include/Population.h
)

target_include_directories(brkga
    PUBLIC
        $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
        $<INSTALL_INTERFACE:include>
)

target_link_libraries(brkga PUBLIC OpenMP::OpenMP_CXX)

if(BRKGA_ENABLE_README_WARNINGS AND CMAKE_CXX_COMPILER_ID MATCHES "GNU|Clang|AppleClang")
  target_compile_options(brkga
        PUBLIC
            -Wall
            -Wextra
            -pedantic
            -Woverloaded-virtual
            -Wcast-align
            -Wpointer-arith
    )
endif()

set_target_properties(brkga PROPERTIES
    POSITION_INDEPENDENT_CODE ON
)

if(BRKGA_ENABLE_RANGECHECK)
  target_compile_definitions(brkga PUBLIC RANGECHECK)
endif()
