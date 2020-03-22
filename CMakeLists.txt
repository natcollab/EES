#[===============================================================================[.rst
# EES/CMakeLists.txt
#
# EES library.
# ^^^^^^^^^^^^
#
# Imported targets in global scope:
#
#   Trader
#   Quote
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
add_library(Trader INTERFACE)

include(GNUInstallDirs)
target_include_directories(Trader
  INTERFACE  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
             $<INSTALL_INTERFACE:include/EES>
  )

target_link_libraries(Trader
  INTERFACE  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/lib/libEESTraderApi.so>
             $<INSTALL_INTERFACE:${CMAKE_INSTALL_LIBDIR}/lib/EES/libEESTraderApi.so>
  )

# Target: Quote
add_library(Quote INTERFACE)

target_include_directories(Quote
  INTERFACE  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
             $<INSTALL_INTERFACE:include/EES>
  )

target_link_libraries(Quote
  INTERFACE  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/lib/libEESQuoteApi.so>
             $<INSTALL_INTERFACE:lib/EES/libEESQuoteApi.so>
  )

#=================================================================================

# Install : Trader
install(DIRECTORY include/
  DESTINATION include/EES
  FILES_MATCHING PATTERN "EesTrader*.h"
  )

install(FILES lib/libEESTraderApi.so
  DESTINATION lib/EES
  )

# Install : Quote
install(DIRECTORY include/
  DESTINATION include/EES
  FILES_MATCHING PATTERN "EESQuote*.h"
  )

install(FILES lib/libEESQuoteApi.so
  DESTINATION lib/EES
  )

#=================================================================================

# Packaging : Trader
install(TARGETS Trader
  EXPORT Trader_export
  LIBRARY
    DESTINATION ${CMAKE_INSTALL_LIBDIR}/lib/EES
  )

install(EXPORT Trader_export
  DESTINATION lib/cmake/EES
  FILE EESTarget.cmake
  )
