include(FetchContent)

FetchContent_Declare(
        opentelemetry-proto
        URL         https://github.com/open-telemetry/opentelemetry-proto/archive/refs/tags/v1.1.0.zip
        URL_HASH    MD5=7715f9794ef9e23a5e5853195a3cf01c
)

FetchContent_MakeAvailable(opentelemetry-proto)

FetchContent_GetProperties(
        opentelemetry-proto
        SOURCE_DIR OpenTelemetryProto_SRC
)

set(PROTO_BASE_PATH ${OpenTelemetryProto_SRC}/opentelemetry/proto)

set(PROTO_FILES
        ${PROTO_BASE_PATH}/metrics/v1/metrics.proto
        ${PROTO_BASE_PATH}/trace/v1/trace.proto
        ${PROTO_BASE_PATH}/collector/metrics/v1/metrics_service.proto
        ${PROTO_BASE_PATH}/collector/trace/v1/trace_service.proto
        ${PROTO_BASE_PATH}/collector/logs/v1/logs_service.proto
        ${PROTO_BASE_PATH}/common/v1/common.proto
        ${PROTO_BASE_PATH}/logs/v1/logs.proto
        ${PROTO_BASE_PATH}/resource/v1/resource.proto
)

set(PROTO_GEN_CPP
        ${CMAKE_CURRENT_BINARY_DIR}/opentelemetry/proto/metrics/v1/metrics.pb.h
        ${CMAKE_CURRENT_BINARY_DIR}/opentelemetry/proto/metrics/v1/metrics.pb.cc
        ${CMAKE_CURRENT_BINARY_DIR}/opentelemetry/proto/trace/v1/trace.pb.h
        ${CMAKE_CURRENT_BINARY_DIR}/opentelemetry/proto/trace/v1/trace.pb.cc
        ${CMAKE_CURRENT_BINARY_DIR}/opentelemetry/proto/collector/metrics/v1/metrics_service.pb.cc
        ${CMAKE_CURRENT_BINARY_DIR}/opentelemetry/proto/collector/metrics/v1/metrics_service.pb.h
        ${CMAKE_CURRENT_BINARY_DIR}/opentelemetry/proto/collector/trace/v1/trace_service.pb.h
        ${CMAKE_CURRENT_BINARY_DIR}/opentelemetry/proto/collector/trace/v1/trace_service.pb.cc
        ${CMAKE_CURRENT_BINARY_DIR}/opentelemetry/proto/collector/logs/v1/logs_service.pb.cc
        ${CMAKE_CURRENT_BINARY_DIR}/opentelemetry/proto/collector/logs/v1/logs_service.pb.h
        ${CMAKE_CURRENT_BINARY_DIR}/opentelemetry/proto/common/v1/common.pb.h
        ${CMAKE_CURRENT_BINARY_DIR}/opentelemetry/proto/common/v1/common.pb.cc
        ${CMAKE_CURRENT_BINARY_DIR}/opentelemetry/proto/logs/v1/logs.pb.cc
        ${CMAKE_CURRENT_BINARY_DIR}/opentelemetry/proto/logs/v1/logs.pb.h
        ${CMAKE_CURRENT_BINARY_DIR}/opentelemetry/proto/resource/v1/resource.pb.cc
        ${CMAKE_CURRENT_BINARY_DIR}/opentelemetry/proto/resource/v1/resource.pb.h
)

find_package(Protobuf REQUIRED)

add_library(opentelemetry-proto-cpp
    ${PROTO_GEN_CPP}
)

target_link_libraries(opentelemetry-proto-cpp  ${Protobuf_LIBRARIES})

target_include_directories(opentelemetry-proto-cpp
        PUBLIC
            ${Protobuf_INCLUDE_DIRS}
            ${CMAKE_CURRENT_BINARY_DIR}
)

add_custom_command(
        OUTPUT ${PROTO_GEN_CPP}
        COMMAND ${CMAKE_COMMAND} -E make_directory ${CMAKE_CURRENT_BINARY_DIR}
        COMMAND ${Protobuf_PROTOC_EXECUTABLE} --experimental_allow_proto3_optional --proto_path=${OpenTelemetryProto_SRC} --cpp_out=${CMAKE_CURRENT_BINARY_DIR} ${PROTO_FILES}
        VERBATIM
)

target_compile_features(opentelemetry-proto-cpp PRIVATE cxx_std_17)
