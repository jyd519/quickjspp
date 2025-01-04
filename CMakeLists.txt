cmake_minimum_required(VERSION 3.28)
project(quickjspp LANGUAGES CXX C)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_INTERPROCEDURAL_OPTIMIZATION TRUE)

file(STRINGS quickjs/VERSION version)

if(CMAKE_COMPILER_IS_GNUCC)
    add_compile_options(-Wall -Wno-unused-parameter)
endif()

add_link_options(-static-libgcc -static-libstdc++ -static -s)

add_subdirectory(quickjs)

add_library(quickjspp INTERFACE)
target_link_libraries(quickjspp INTERFACE quickjs ${CMAKE_REQUIRED_LIBRARIES})
target_compile_features(quickjspp INTERFACE cxx_std_17)
target_include_directories(quickjspp INTERFACE .)
set_target_properties(quickjspp PROPERTIES PUBLIC_HEADER quickjspp.hpp)

add_executable(qjs1 qjs.cpp)
target_link_libraries(qjs1 quickjspp)

add_executable(qjs qjs2.cpp )
target_sources(qjs PRIVATE quickjs/repl.c)
target_link_libraries(qjs quickjspp)
target_include_directories(qjs PRIVATE quickjs)
target_compile_definitions(qjs PRIVATE CONFIG_VERSION="${version}")

# cmake -DBUILD_TESTING=OFF to disable tests
include(CTest)
if(BUILD_TESTING)
    add_subdirectory(test)
endif()

install(TARGETS quickjspp qjs)
