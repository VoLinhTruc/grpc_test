cmake_minimum_required(VERSION 3.14)
project(grpc_test)

find_package(Protobuf REQUIRED)
find_package(gRPC CONFIG REQUIRED)

# Generate proto files
set(PROTO_FILE ${CMAKE_SOURCE_DIR}/helloworld.proto)
set(PROTO_SRCS ${CMAKE_BINARY_DIR}/helloworld.pb.cc)
set(PROTO_HDRS ${CMAKE_BINARY_DIR}/helloworld.pb.h)
set(GRPC_SRCS ${CMAKE_BINARY_DIR}/helloworld.grpc.pb.cc)
set(GRPC_HDRS ${CMAKE_BINARY_DIR}/helloworld.grpc.pb.h)

add_custom_command(
  OUTPUT ${PROTO_SRCS} ${PROTO_HDRS} ${GRPC_SRCS} ${GRPC_HDRS}
  COMMAND protoc --cpp_out=${CMAKE_BINARY_DIR} --grpc_out=${CMAKE_BINARY_DIR}
          --plugin=protoc-gen-grpc=/usr/local/bin/grpc_cpp_plugin
          -I${CMAKE_SOURCE_DIR} ${PROTO_FILE}
  DEPENDS ${PROTO_FILE}
  WORKING_DIRECTORY ${CMAKE_BINARY_DIR}
)

# Create library from proto files
add_library(proto_lib ${PROTO_SRCS} ${PROTO_HDRS} ${GRPC_SRCS} ${GRPC_HDRS})
target_link_libraries(proto_lib PUBLIC 
  protobuf::libprotobuf
  gRPC::grpc++
)
target_include_directories(proto_lib PUBLIC ${CMAKE_BINARY_DIR})

add_subdirectory(grpc_server)
add_subdirectory(grpc_client)
