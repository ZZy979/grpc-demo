# proto targets
set(route_guide_proto_srcs
  ${CMAKE_CURRENT_BINARY_DIR}/route_guide.pb.cc
  ${CMAKE_CURRENT_BINARY_DIR}/route_guide.grpc.pb.cc)

add_custom_command(
  OUTPUT ${route_guide_proto_srcs}
  COMMAND protobuf::protoc
    -I${CMAKE_SOURCE_DIR}/protos
    --cpp_out=${CMAKE_CURRENT_BINARY_DIR}
    --grpc_out=${CMAKE_CURRENT_BINARY_DIR}
    --plugin=protoc-gen-grpc=$<TARGET_FILE:grpc_cpp_plugin>
    route_guide.proto)

add_library(route_guide_proto ${route_guide_proto_srcs})
target_link_libraries(route_guide_proto
  PUBLIC protobuf::libprotobuf
  PRIVATE grpc++ grpc++_reflection)
target_include_directories(route_guide_proto PUBLIC ${CMAKE_CURRENT_BINARY_DIR})

# route_guide_helper
add_library(route_guide_helper helper.cc)
target_link_libraries(route_guide_helper route_guide_proto grpc++ grpc++_reflection)

# client/server targets
foreach(target route_guide_client route_guide_server)
  add_executable(${target} ${target}.cc)
  target_link_libraries(${target} route_guide_proto route_guide_helper grpc++ grpc++_reflection)
endforeach()
