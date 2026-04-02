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

option(NODE_ADDON_API_DISABLE_DEPRECATED "Disable deprecated API" ON)
option(NODE_ADDON_API_REQUIRE_BASIC_FINALIZERS "Require basic finalizers" OFF)
option(NODE_ADDON_API_CPP_EXCEPTIONS "Enable C++ exceptions" ON)
if (NODE_ADDON_API_CPP_EXCEPTIONS)
    option(NODE_ADDON_API_CPP_EXCEPTIONS_ALL "Enable C++ exceptions for all" OFF)
endif()
option(NODE_ADDON_API_ENABLE_MAYBE "Enable Maybe" OFF)
option(NODE_API_SWALLOW_UNTHROWABLE_EXCEPTIONS "Swallow unthrowable exceptions" ON)

find_package(unofficial-node-api-headers CONFIG REQUIRED)

add_library(node-addon-api INTERFACE napi.h napi-inl.h)
target_link_directories(node-addon-api INTERFACE unofficial::node-api-headers::node-api-headers)
target_include_directories(node-addon-api INTERFACE
    ${CMAKE_CURRENT_SOURCE_DIR}
)
target_compile_definitions(node-addon-api INTERFACE
    $<$<BOOL:${NODE_ADDON_API_DISABLE_DEPRECATED}>:NODE_ADDON_API_DISABLE_DEPRECATED>
    $<$<BOOL:${NODE_ADDON_API_CPP_EXCEPTIONS}>:NAPI_CPP_EXCEPTIONS>
    $<$<BOOL:${NODE_ADDON_API_CPP_EXCEPTIONS_ALL}>:NODE_ADDON_API_CPP_EXCEPTIONS_ALL>
    $<$<BOOL:${NODE_ADDON_API_REQUIRE_BASIC_FINALIZERS}>:NODE_ADDON_API_REQUIRE_BASIC_FINALIZERS>
    $<$<BOOL:${NODE_ADDON_API_ENABLE_MAYBE}>:NODE_ADDON_API_ENABLE_MAYBE>
    $<$<BOOL:${NODE_API_SWALLOW_UNTHROWABLE_EXCEPTIONS}>:NODE_API_SWALLOW_UNTHROWABLE_EXCEPTIONS>
)

# Just for cmake can generate compile_commands.json for LSP
add_library(phony OBJECT nothing.c)
target_link_libraries(phony PUBLIC node-addon-api)

include(CTest)
if(BUILD_TESTING)
    enable_testing()
    add_subdirectory(test)
endif()