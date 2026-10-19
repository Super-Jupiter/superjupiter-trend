cmake_minimum_required(VERSION 3.16)
project(SuperJupiterTrend VERSION 0.1.1 LANGUAGES CXX)

set(_superjupiter_trend_top_level OFF)
if(CMAKE_CURRENT_SOURCE_DIR STREQUAL CMAKE_SOURCE_DIR)
  set(_superjupiter_trend_top_level ON)
endif()
option(SUPERJUPITER_TREND_BUILD_TESTS "Build Trend tests" ${_superjupiter_trend_top_level})
option(SUPERJUPITER_TREND_BUILD_EXAMPLES "Build Trend host example" ${_superjupiter_trend_top_level})
option(SUPERJUPITER_TREND_INSTALL "Generate Trend install rules" ${_superjupiter_trend_top_level})

add_library(SuperJupiterTrend INTERFACE)
add_library(SuperJupiter::Trend ALIAS SuperJupiterTrend)
set_target_properties(SuperJupiterTrend PROPERTIES EXPORT_NAME Trend)
target_include_directories(SuperJupiterTrend INTERFACE
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/src>
  $<INSTALL_INTERFACE:include>)
target_compile_features(SuperJupiterTrend INTERFACE cxx_std_11)

if(SUPERJUPITER_TREND_BUILD_TESTS)
  enable_testing()
  add_executable(superjupiter-trend-review tests/review_regressions.cpp)
  target_link_libraries(superjupiter-trend-review PRIVATE SuperJupiter::Trend)
  add_test(NAME superjupiter-trend-review COMMAND superjupiter-trend-review)
  add_executable(superjupiter-trend-tests tests/test.cpp)
  target_link_libraries(superjupiter-trend-tests PRIVATE SuperJupiter::Trend)
  add_test(NAME superjupiter-trend-tests COMMAND superjupiter-trend-tests)
  add_executable(superjupiter-trend-namespace-test tests/namespace_isolation.cpp)
  target_link_libraries(superjupiter-trend-namespace-test PRIVATE SuperJupiter::Trend)
  add_test(NAME superjupiter-trend-namespace-test COMMAND superjupiter-trend-namespace-test)
endif()
if(SUPERJUPITER_TREND_BUILD_EXAMPLES)
  add_executable(superjupiter-trend-replay tools/replay.cpp)
  target_link_libraries(superjupiter-trend-replay PRIVATE SuperJupiter::Trend)
endif()
if(SUPERJUPITER_TREND_INSTALL)
  include(CMakePackageConfigHelpers)
  set(_superjupiter_trend_package_dir lib/cmake/SuperJupiterTrend)
  install(TARGETS SuperJupiterTrend EXPORT SuperJupiterTrendTargets)
  install(FILES src/SuperJupiterTrend.h DESTINATION include)
  install(EXPORT SuperJupiterTrendTargets NAMESPACE SuperJupiter::
    DESTINATION ${_superjupiter_trend_package_dir})
  configure_package_config_file(cmake/SuperJupiterTrendConfig.cmake.in
    ${CMAKE_CURRENT_BINARY_DIR}/SuperJupiterTrendConfig.cmake
    INSTALL_DESTINATION ${_superjupiter_trend_package_dir})
  write_basic_package_version_file(${CMAKE_CURRENT_BINARY_DIR}/SuperJupiterTrendConfigVersion.cmake
    VERSION ${PROJECT_VERSION} COMPATIBILITY SameMinorVersion ARCH_INDEPENDENT)
  install(FILES ${CMAKE_CURRENT_BINARY_DIR}/SuperJupiterTrendConfig.cmake
    ${CMAKE_CURRENT_BINARY_DIR}/SuperJupiterTrendConfigVersion.cmake
    DESTINATION ${_superjupiter_trend_package_dir})
  install(FILES LICENSE DESTINATION share/licenses/SuperJupiterTrend)
endif()
