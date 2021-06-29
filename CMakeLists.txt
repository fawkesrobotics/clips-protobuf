cmake_minimum_required(VERSION 3.12)

project(
  clips_protobuf
  VERSION 0.9.0
  DESCRIPTION "Read and write protobuf messages from within CLIPS")

include(GNUInstallDirs)

find_package(Protobuf REQUIRED)
find_package(ProtobufComm REQUIRED)
find_package(spdlog REQUIRED)
include(FindPkgConfig)
pkg_check_modules(clipsmm REQUIRED IMPORTED_TARGET clipsmm-1.0)
add_library(clips_protobuf SHARED src/communicator.cpp)
target_link_libraries(
  clips_protobuf protobuf::libprotobuf ProtobufComm::protobuf_comm
  PkgConfig::clipsmm spdlog::spdlog)
target_include_directories(
  clips_protobuf PUBLIC $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>)
target_compile_definitions(
  clips_protobuf PRIVATE SHAREDIR="${CMAKE_INSTALL_FULL_DATADIR}/clips")
set_target_properties(clips_protobuf PROPERTIES SOVERSION
                                                ${PROJECT_VERSION_MAJOR})

install(
  TARGETS clips_protobuf
  EXPORT ClipsProtobufTargets
  LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR})

install(DIRECTORY include/clips_protobuf
        DESTINATION ${CMAKE_INSTALL_INCLUDEDIR})

install(FILES src/protobuf.clp DESTINATION ${CMAKE_INSTALL_FULL_DATADIR}/clips)

include(CMakePackageConfigHelpers)
write_basic_package_version_file(
  ${CMAKE_CURRENT_BINARY_DIR}/ClipsProtobufConfigVersion.cmake
  VERSION ${PROJECT_VERSION}
  COMPATIBILITY SameMajorVersion)

install(
  EXPORT ClipsProtobufTargets
  FILE ClipsProtobufTargets.cmake
  NAMESPACE ClipsProtobuf::
  DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/ClipsProtobuf)

install(FILES cmake/ClipsProtobufConfig.cmake
              ${CMAKE_CURRENT_BINARY_DIR}/ClipsProtobufConfigVersion.cmake
        DESTINATION ${CMAKE_INSTALL_LIBDIR}/cmake/ClipsProtobuf)

set(PKG_CONFIG_REQUIRES "protobuf protobuf_comm spdlog clipsmm-1.0")

configure_file(clips_protobuf.pc.in
               ${CMAKE_CURRENT_BINARY_DIR}/clips_protobuf.pc @ONLY)

install(FILES ${CMAKE_CURRENT_BINARY_DIR}/clips_protobuf.pc
        DESTINATION ${CMAKE_INSTALL_LIBDIR}/pkgconfig)
