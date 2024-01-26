cmake_minimum_required(VERSION 3.10)

project(opentelemetry-protobuf-interface)

add_subdirectory(3rdparty/opentelemetry)

add_executable(test main.cpp)

target_link_libraries(test PRIVATE opentelemetry-proto-cpp)

target_compile_features(test PRIVATE cxx_std_17)
