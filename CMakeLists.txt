cmake_minimum_required(VERSION 3.10)

project(kvstore LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

add_executable(kvnode
    src/main.cpp
)
target_include_directories(kvnode PRIVATE src)

add_executable(kv_workload
    src/client/workload.cpp
)
target_include_directories(kv_workload PRIVATE src)

find_package(Threads REQUIRED)
target_link_libraries(kvnode PRIVATE Threads::Threads)
target_link_libraries(kv_workload PRIVATE Threads::Threads)
