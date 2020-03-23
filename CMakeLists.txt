#[===============================================================================[.rst
# EES/CMakeLists.txt
#
# EES library.
# ^^^^^^^^^^^^
#
# Imported targets in global scope:
#
#   EES::Trader
#   EES::Quote
#
#]===============================================================================]

# Setup minimum required version of cmake, policy
# This command should be the first command in the project
cmake_minimum_required(VERSION 3.13...3.16)

# Setup Project name and version
# CMakeTest_VERSION and PROJECT_VERSION variables are set
project(EES VERSION 1.0
  DESCRIPTION "Interface to EES library"
  LANGUAGES CXX
  )

#=================================================================================

# Target: Trader
add_library(EES.Trader INTERFACE)
add_library(EES::Trader ALIAS EES.Trader)

set(EES_INCLUDE_INSTALL_DIR include/EES)
set(EES_LIB_INSTALL_DIR lib/EES)
set(EES_CMAKE_INSTALL_DIR lib/cmake/EES)

target_include_directories(EES.Trader
  INTERFACE  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
             $<INSTALL_INTERFACE:${EES_INCLUDE_INSTALL_DIR}>
  )

target_link_libraries(EES.Trader
  INTERFACE  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/lib/libEESTraderApi.so>
             $<INSTALL_INTERFACE:${EES_LIB_INSTALL_DIR}/libEESTraderApi.so>
  )

# Target: Quote
add_library(EES.Quote INTERFACE)
add_library(EES::Quote ALIAS EES.Quote)

target_include_directories(EES.Quote
  INTERFACE  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
             $<INSTALL_INTERFACE:${EES_INCLUDE_INSTALL_DIR}>
  )

target_link_libraries(EES.Quote
  INTERFACE  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/lib/libEESQuoteApi.so>
             $<INSTALL_INTERFACE:${EES_LIB_INSTALL_DIR}/libEESQuoteApi.so>
  )

#=================================================================================

# Install : Trader
install(DIRECTORY include/
  DESTINATION ${EES_INCLUDE_INSTALL_DIR}
  FILES_MATCHING PATTERN "EesTrader*.h"
  )

install(FILES lib/libEESTraderApi.so
  DESTINATION ${EES_LIB_INSTALL_DIR}
  )

# Install : Quote
install(DIRECTORY include/
  DESTINATION ${EES_INCLUDE_INSTALL_DIR}
  FILES_MATCHING PATTERN "EESQuote*.h"
  )

install(FILES lib/libEESQuoteApi.so
  DESTINATION ${EES_LIB_INSTALL_DIR}
  )

#=================================================================================

# Install export targets

install(TARGETS EES.Trader EXPORT EES_export)
set_target_properties(EES.Trader PROPERTIES EXPORT_NAME Trader)

install(TARGETS EES.Quote EXPORT EES_export)
set_target_properties(EES.Quote PROPERTIES EXPORT_NAME Quote)

# TODO: library path in export target file is relative path, which is not correct
install(EXPORT EES_export
  NAMESPACE EES::
  FILE EESTargets.cmake
  DESTINATION ${EES_CMAKE_INSTALL_DIR}
  )

#=================================================================================

# Install package config and version files

include(CMakePackageConfigHelpers)

# Package version file
write_basic_package_version_file(
  ${CMAKE_CURRENT_BINARY_DIR}/EESConfigVersion.cmake
  VERSION ${PROJECT_VERSION}
  COMPATIBILITY SameMajorVersion
  )

# Package config file
configure_package_config_file(
  ${CMAKE_CURRENT_LIST_DIR}/cmake/EESConfig.cmake.in
  ${CMAKE_CURRENT_BINARY_DIR}/EESConfig.cmake
  INSTALL_DESTINATION ${EES_CMAKE_INSTALL_DIR}
  PATH_VARS
    EES_INCLUDE_INSTALL_DIR
    EES_LIB_INSTALL_DIR
  )

# Install the config and version files
install(FILES
  ${CMAKE_CURRENT_BINARY_DIR}/EESConfig.cmake
  ${CMAKE_CURRENT_BINARY_DIR}/EESConfigVersion.cmake
  DESTINATION ${EES_CMAKE_INSTALL_DIR}
  )
