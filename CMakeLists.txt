cmake_minimum_required(VERSION 3.28)
project(writing)

set(CMAKE_CXX_STANDARD 20)

option(protobuf_BUILD_TESTS OFF) # otherwise we get error involving absl::scoped_mock_log

add_subdirectory(third_party/protobuf) # CMakeLists.txt from protobuf, so we get access to all protobuf::* targets
add_subdirectory(third_party/abseil-cpp) # CMakeLists.txt from abseil, so we get access to all absl::* targets

file(GLOB PROTO_FILES "${CMAKE_CURRENT_SOURCE_DIR}/protos/*.proto")
set(GENERATED_PROTO_SRCS)
set(GENERATED_PROTO_HDRS)
foreach(PROTO_FILE ${PROTO_FILES})
    get_filename_component(PROTO_FILENAME_WE ${PROTO_FILE} NAME_WE)
    set(GENERATED_SRC "${CMAKE_CURRENT_SOURCE_DIR}/protos/${PROTO_FILENAME_WE}.pb.cc")
    set(GENERATED_HDR "${CMAKE_CURRENT_SOURCE_DIR}/protos/${PROTO_FILENAME_WE}.pb.h")

    add_custom_command(
            OUTPUT ${GENERATED_SRC} ${GENERATED_HDR}
            COMMAND protobuf::protoc
            ARGS --cpp_out=${CMAKE_CURRENT_SOURCE_DIR}/protos
            -I ${CMAKE_CURRENT_SOURCE_DIR}/protos
            ${PROTO_FILE}
            DEPENDS ${PROTO_FILE}
            COMMENT "Generating ${PROTO_FILE} .cc/.h from .proto"
    )
    list(APPEND GENERATED_PROTO_SRCS ${GENERATED_SRC})
    list(APPEND GENERATED_PROTO_HDRS ${GENERATED_HDR})
endforeach()

add_custom_target(generate_protos DEPENDS ${GENERATED_PROTO_SRCS} ${GENERATED_PROTO_HDRS})

set(INCLUDE_DIRS
        ${CMAKE_CURRENT_SOURCE_DIR}
        ${protobuf_SOURCE_DIR}/src
)
file(GLOB SRC_FILES "${CMAKE_CURRENT_SOURCE_DIR}/src/*.cpp")
foreach(SRC_FILE ${SRC_FILES})
    get_filename_component(EXEC_NAME ${SRC_FILE} NAME_WE)
    add_executable(${EXEC_NAME} ${SRC_FILE} ${GENERATED_PROTO_SRCS})
    target_include_directories(${EXEC_NAME} PRIVATE ${INCLUDE_DIRS})
    target_link_libraries(${EXEC_NAME} PRIVATE protobuf::libprotobuf)
endforeach()
