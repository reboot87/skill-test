cmake_minimum_required(VERSION 3.16)
project(hello_world LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

add_executable(hello src/main.cpp)

include(CTest)
if(BUILD_TESTING)
    add_executable(hello_test tests/test_hello.cpp)
    add_test(NAME hello_test COMMAND hello_test)
endif()
