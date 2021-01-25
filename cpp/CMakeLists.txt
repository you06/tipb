cmake_minimum_required (VERSION 2.8)

set (CMAKE_CXX_STANDARD 17)

set(PROTO_DIR "${CMAKE_CURRENT_SOURCE_DIR}/../proto")
set(OUTPUT_DIR "${CMAKE_CURRENT_SOURCE_DIR}/tipb")

file(GLOB PROTO_FILES "${PROTO_DIR}/*.proto")
foreach(PROTO_FILE ${PROTO_FILES})
    get_filename_component (FILENAME ${PROTO_FILE} NAME_WLE)
    set(PROTO_SRC "${OUTPUT_DIR}/${FILENAME}.pb.cc")
    set(PROTO_HDR "${OUTPUT_DIR}/${FILENAME}.pb.h")

    list(APPEND OUTPUT_SOURCES ${PROTO_SRC} ${PROTO_HDR})
endforeach()

add_custom_command(
    OUTPUT ${OUTPUT_SOURCES}
    COMMAND cd ${CMAKE_CURRENT_SOURCE_DIR}/.. &&
            PROTOC=${_PROTOBUF_PROTOC} ./generate-cpp.sh &&
            cd -
    DEPENDS ${PROTO_FILE}
)

add_library(tipb ${OUTPUT_SOURCES})
target_link_libraries(tipb ${_GRPC_GRPCPP_UNSECURE} ${_PROTOBUF_LIBPROTOBUF})
target_include_directories(tipb PUBLIC ${CMAKE_CURRENT_SOURCE_DIR})
