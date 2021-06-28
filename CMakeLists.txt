cmake_minimum_required(VERSION 3.12)

project(clips_protobuf)

find_package(Protobuf REQUIRED)
find_package(ProtobufComm REQUIRED)
find_package(spdlog REQUIRED)
include(FindPkgConfig)
pkg_check_modules(clipsmm REQUIRED IMPORTED_TARGET clipsmm-1.0)
add_library(clips_protobuf SHARED src/communicator.cpp)
target_link_libraries(
  clips_protobuf protobuf::libprotobuf ProtobufComm::protobuf_comm
  PkgConfig::clipsmm spdlog::spdlog)
target_include_directories(clips_protobuf PUBLIC include)
