cmake_minimum_required(VERSION 2.6)

project(TestSocket)
set(EXECUTABLE_OUTPUT_PATH bin/${CMAKE_BUILD_TYPE})
set(CMAKE_CXX_STANDARD 14) # c++14
add_definitions(-DLINUX)
add_definitions(-DOPENSSL)

# Code coverage setup
IF(CMAKE_BUILD_TYPE MATCHES Coverage)
   INCLUDE(CodeCoverage.cmake)
   SET(CMAKE_CXX_FLAGS "-g -O0 -fprofile-arcs -ftest-coverage")
   SET(CMAKE_C_FLAGS "-g -O0 -fprofile-arcs -ftest-coverage")
ENDIF(CMAKE_BUILD_TYPE MATCHES Coverage)

# Locate GTest
find_package(GTest REQUIRED)
include_directories(${GTEST_INCLUDE_DIRS})

include_directories(../Socket)
include_directories(./simpleini)
include_directories(./)

IF(CMAKE_BUILD_TYPE MATCHES Coverage)

file(GLOB_RECURSE socket_source_files ../Socket/*)

#Output Setup
add_executable(test_socket Tests.cpp test_utils.cpp ${socket_source_files})

#Link setup
target_link_libraries(test_socket ${GTEST_LIBRARIES} pthread ssl crypto)

SETUP_TARGET_FOR_COVERAGE(
           coverage_socket  # Name for custom target.
           test_socket      # Name of the test driver executable that runs the tests.
                            # NOTE! This should always have a ZERO as exit code
                            # otherwise the coverage generation will not complete.
           coverage         # Name of output directory.
           "/home/amzoughi/Test/socket_github.ini"  # Optional fourth parameter is passed as arguments to _testrunner
                                                 # Pass them in list form, e.g.: "-j;2" for -j 2
           )
ELSE(CMAKE_BUILD_TYPE MATCHES Coverage)

link_directories(../lib/${CMAKE_BUILD_TYPE})

#Output Setup
add_executable(test_socket Tests.cpp test_utils.cpp)

#Link setup
target_link_libraries(test_socket socket ${GTEST_LIBRARIES} pthread ssl crypto)

ENDIF(CMAKE_BUILD_TYPE MATCHES Coverage)

