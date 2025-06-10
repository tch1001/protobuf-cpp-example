cmake_minimum_required(VERSION 3.28)
project(writing)

set(CMAKE_CXX_STANDARD 20)

option(protobuf_BUILD_TESTS OFF) # otherwise we get error involving absl::scoped_mock_log

add_subdirectory(third_party/protobuf) # CMakeLists.txt from protobuf, so we get access to all protobuf::* targets
add_subdirectory(third_party/abseil-cpp) # CMakeLists.txt from abseil, so we get access to all absl::* targets

set(PROTO_FILE "${CMAKE_CURRENT_SOURCE_DIR}/addressbook.proto")
set(GENERATED_PROTO_SRC "${CMAKE_CURRENT_SOURCE_DIR}/addressbook.pb.cc")
set(GENERATED_PROTO_HDR "${CMAKE_CURRENT_SOURCE_DIR}/addressbook.pb.h")

add_custom_command(
        OUTPUT ${GENERATED_PROTO_SRC} ${GENERATED_PROTO_HDR}
        COMMAND protobuf::protoc
        ARGS --cpp_out=${CMAKE_CURRENT_SOURCE_DIR}
             -I ${CMAKE_CURRENT_SOURCE_DIR}
            ${PROTO_FILE}
        DEPENDS ${PROTO_FILE}
        COMMENT "Generating .cc/.h from .proto"
)
add_custom_target(generate_protos DEPENDS ${GENERATED_PROTO_SRC} ${GENERATED_PROTO_HDR})
add_executable(writing writing.cpp ${GENERATED_PROTO_SRC})
add_executable(reading reading.cpp ${GENERATED_PROTO_SRC})

set(INCLUDE_DIRS
    ${CMAKE_CURRENT_SOURCE_DIR}/protos
    ${protobuf_SOURCE_DIR}/src
)
target_include_directories(writing PRIVATE ${INCLUDE_DIRS})
target_include_directories(reading PRIVATE ${INCLUDE_DIRS})

target_link_libraries(writing PRIVATE protobuf::libprotobuf)
target_link_libraries(reading PRIVATE protobuf::libprotobuf)
