cmake_minimum_required(VERSION 3.9.4 FATAL_ERROR)


# Project
# ######################################################################
project(Ex_1-n-1 VERSION 0.0.1 LANGUAGES CXX)

set(CMAKE_PREFIX_PATH $ENV{FAIRMQ_DIR} ${CMAKE_PREFIX_PATH})
find_package(FairMQ)
if(FairMQ_FOUND)
  find_package(FairLogger ${FairMQ_FairLogger_VERSION})
  find_package(Boost ${FairMQ_Boost_VERSION} COMPONENTS ${FairMQ_Boost_COMPONENTS})
endif()


string(TOLOWER ${PROJECT_NAME} PROJECT_NAME_LOWER)
string(TOUPPER ${PROJECT_NAME} PROJECT_NAME_UPPER)

# Set a default build type
if(NOT CMAKE_BUILD_TYPE)
  set(CMAKE_BUILD_TYPE RelWithDebInfo)
endif()


# Handle C++ standard level
set(CMAKE_CXX_STANDARD_REQUIRED ON)
if(NOT CMAKE_CXX_STANDARD)
  set(CMAKE_CXX_STANDARD 11)
elseif(${CMAKE_CXX_STANDARD} LESS 11)
  message(FATAL_ERROR "A minimum CMAKE_CXX_STANDARD of 11 is required.")
elseif(${CMAKE_CXX_STANDARD} GREATER 11)
  message(WARNING "A CMAKE_CXX_STANDARD of ${CMAKE_CXX_STANDARD} (greater than 11) is not tested. Use on your own risk.")
endif()
set(CMAKE_CXX_EXTENSIONS OFF)

# Set -fPIC as default for all library types
set(CMAKE_POSITION_INDEPENDENT_CODE ON)

# Generate compile_commands.json file
# (https://clang.llvm.org/docs/JSONCompilationDatabase.html)
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

# Define CMAKE_INSTALL_*DIR family of variables
include(GNUInstallDirs)

# Define install dirs
set(PROJECT_INSTALL_BINDIR ${CMAKE_INSTALL_BINDIR})
set(PROJECT_INSTALL_LIBDIR ${CMAKE_INSTALL_LIBDIR})
set(PROJECT_INSTALL_INCDIR ${CMAKE_INSTALL_INCLUDEDIR}/${PROJECT_NAME_LOWER}) 
set(PROJECT_INSTALL_DATADIR ${CMAKE_INSTALL_DATADIR}/${PROJECT_NAME_LOWER})
set(PROJECT_INSTALL_CMAKEMODDIR ${PROJECT_INSTALL_DATADIR}/cmake)

# https://cmake.org/Wiki/CMake_RPATH_handling
set(CMAKE_INSTALL_RPATH_USE_LINK_PATH TRUE)
list(FIND CMAKE_PLATFORM_IMPLICIT_LINK_DIRECTORIES "${CMAKE_INSTALL_PREFIX}/${PROJECT_INSTALL_LIBDIR}" isSystemDir)
if("${isSystemDir}" STREQUAL "-1")
  if(CMAKE_SYSTEM_NAME STREQUAL "Linux")
    set(CMAKE_EXE_LINKER_FLAGS ${CMAKE_EXE_LINKER_FLAGS} "-Wl,--enable-new-dtags")
    set(CMAKE_SHARED_LINKER_FLAGS ${CMAKE_SHARED_LINKER_FLAGS} "-Wl,--enable-new-dtags")
    set(CMAKE_INSTALL_RPATH "$ORIGIN/../${PROJECT_INSTALL_LIBDIR}")
  elseif(CMAKE_SYSTEM_NAME STREQUAL "Darwin")
    set(CMAKE_INSTALL_RPATH "@loader_path/../${PROJECT_INSTALL_LIBDIR}")
  else()
    set(CMAKE_INSTALL_RPATH "${CMAKE_INSTALL_PREFIX}/${PROJECT_INSTALL_LIBDIR}")
  endif()
endif()

# Define export set, only one for now
set(PROJECT_EXPORT_SET ${PROJECT_NAME}Targets)

# Override CMake defaults
set(CMAKE_CXX_FLAGS_NIGHTLY "-O2 -g -Wshadow -Wall -Wextra")
set(CMAKE_CXX_FLAGS_PROFILE "-g3 -fno-inline -ftest-coverage -fprofile-arcs -Wshadow -Wall -Wextra -Wunused-variable")

add_subdirectory(1-n-1)