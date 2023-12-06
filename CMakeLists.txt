cmake_minimum_required(VERSION 3.13)
project(grpc-demo)

set(CMAKE_CXX_STANDARD 14)

include(FetchContent)
FetchContent_Declare(
  grpc
  GIT_REPOSITORY https://github.com/grpc/grpc.git
  GIT_TAG v1.54.0
)
set(FETCHCONTENT_QUIET OFF)
FetchContent_MakeAvailable(grpc)

add_subdirectory(helloworld)
add_subdirectory(route_guide)
