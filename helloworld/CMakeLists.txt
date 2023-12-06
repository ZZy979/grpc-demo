# proto targets
set(helloworld_proto_srcs
  ${CMAKE_CURRENT_BINARY_DIR}/helloworld.pb.cc
  ${CMAKE_CURRENT_BINARY_DIR}/helloworld.grpc.pb.cc)

add_custom_command(
  OUTPUT ${helloworld_proto_srcs}
  COMMAND protobuf::protoc
    -I${CMAKE_SOURCE_DIR}/protos
    --cpp_out=${CMAKE_CURRENT_BINARY_DIR}
    --grpc_out=${CMAKE_CURRENT_BINARY_DIR}
    --plugin=protoc-gen-grpc=$<TARGET_FILE:grpc_cpp_plugin>
    helloworld.proto)

add_library(helloworld_proto ${helloworld_proto_srcs})
target_link_libraries(helloworld_proto
  PUBLIC protobuf::libprotobuf
  PRIVATE grpc++ grpc++_reflection)
target_include_directories(helloworld_proto PUBLIC ${CMAKE_CURRENT_BINARY_DIR})

# client/server targets
foreach(target greeter_client greeter_server)
  add_executable(${target} ${target}.cc)
  target_link_libraries(${target} helloworld_proto
    absl::flags absl::flags_parse grpc++ grpc++_reflection)
endforeach()
