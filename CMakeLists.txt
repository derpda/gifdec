cmake_minimum_required(VERSION 3.5)
project(FlexOCRNative LANGUAGES C VERSION 1.0)
set(CMAKE_C_STANDARD 99)

########## Options                                ##############################
option(GD_BUILD_EXAMPLE "Build example" OFF)

########## Build instructions                     ##############################
add_library(gifdec gifdec.c)
target_include_directories(gifdec PUBLIC
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
  $<INSTALL_INTERFACE:include>)
if(CMAKE_BUILD_TYPE STREQUAL "Release")
  target_compile_options(gifdec PRIVATE -O3)
else()
  target_compile_options(gifdec PRIVATE -O0 -g)
endif()

if(GD_BUILD_EXAMPLE)
    add_executable(example example.c)
    # TODO: Allow SDL, not only SDL2?
    find_package(SDL2 REQUIRED)
    target_link_libraries(example gifdec SDL2::SDL2)
endif()

########## Install instructions                   ##############################
install(TARGETS gifdec EXPORT gifdecTargets DESTINATION lib)
install(FILES gifdec.h DESTINATION include)

include(CMakePackageConfigHelpers)
write_basic_package_version_file(
  gifdecConfigVersion.cmake
  VERSION ${PACKAGE_VERSION}
  COMPATIBILITY SameMajorVersion
)
install(FILES
  "${CMAKE_CURRENT_BINARY_DIR}/gifdecConfigVersion.cmake"
  DESTINATION ${CMAKE_INSTALL_PREFIX}/lib/cmake/gifdec
)
install(EXPORT gifdecTargets
  FILE gifdecConfig.cmake
  NAMESPACE gifdec::
  DESTINATION ${CMAKE_INSTALL_PREFIX}/lib/cmake/gifdec
)
