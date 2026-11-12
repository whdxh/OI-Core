cmake_minimum_required(VERSION 4.3)
project(OI_Core)

set(CMAKE_CXX_STANDARD 17)

add_executable(OI_Core ./src/main.cpp)
target_include_directories(OI_Core PRIVATE ${CMAKE_SOURCE_DIR}/include)
