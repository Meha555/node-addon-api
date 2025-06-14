cmake_minimum_required(VERSION 3.15)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)
set(BUILD_TESTING OFF)

project(node-addon-api
    LANGUAGES CXX C
)

if(WIN32)
    set(CMAKE_WINDOWS_EXPORT_ALL_SYMBOLS ON)
endif()

find_package(unofficial-node-api-headers CONFIG REQUIRED)

add_library(node-addon-api INTERFACE napi.h napi-inl.h)
target_link_directories(node-addon-api INTERFACE unofficial::node-api-headers::node-api-headers)
target_include_directories(node-addon-api INTERFACE
    ${CMAKE_CURRENT_SOURCE_DIR}
    ${node-api-headers_INCLUDE_DIR}
)

# Just for cmake can generate compile_commands.json for LSP
add_library(phony OBJECT nothing.c)
target_link_libraries(phony PUBLIC node-addon-api)

include(CTest)
if(BUILD_TESTING)
    enable_testing()
    add_subdirectory(test)
endif()