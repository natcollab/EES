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

include(GNUInstallDirs)
target_include_directories(EES.Trader
  INTERFACE  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
             $<INSTALL_INTERFACE:include/EES>
  )

target_link_libraries(EES.Trader
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
install(TARGETS EES.Trader EXPORT EES_export)

set_target_properties(EES.Trader PROPERTIES EXPORT_NAME Trader)
install(EXPORT EES_export
  NAMESPACE EES::
  FILE EESTargets.cmake
  DESTINATION lib/cmake/EES
  )
