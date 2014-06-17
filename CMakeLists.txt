cmake_minimum_required(VERSION 2.8)

project(kms-jsoncpp)

find_package(KurentoHelpers REQUIRED)

include (KurentoGitHelpers)

get_git_version (PROJECT_VERSION 0.0.2-dev)
install_git_hook (pre-commit ${CMAKE_CURRENT_SOURCE_DIR}/hooks/pre-commit.hook)

find_package(PkgConfig)

find_package(Boost REQUIRED)
pkg_check_modules(JSONCPP REQUIRED jsoncpp>=0.6.0)

string(REPLACE "." ";" VERSION_LIST ${PROJECT_VERSION})
list(GET VERSION_LIST 0 PROJECT_VERSION_MAJOR)
list (LENGTH VERSION_LIST _len)
if (${_len} GREATER 1)
  list(GET VERSION_LIST 1 PROJECT_VERSION_MINOR)
endif ()
if (${_len} GREATER 2)
  list(GET VERSION_LIST 2 PROJECT_VERSION_PATCH)
endif ()

include(GNUInstallDirs)

set(CUSTOM_PREFIX "kurento")
set(INCLUDE_PREFIX "${CMAKE_INSTALL_INCLUDEDIR}/${CUSTOM_PREFIX}/jsonrpc")

if(NOT DEFINED SYSCONF_INSTALL_DIR)
  set(SYSCONF_INSTALL_DIR etc CACHE PATH
      "Install directory for system-wide configuration files")
endif(NOT DEFINED SYSCONF_INSTALL_DIR)

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -g -Wall -Werror -std=c++0x")

set(prefix ${CMAKE_INSTALL_PREFIX})
set(exec_prefix "\${prefix}")
set(libdir "\${exec_prefix}/${CMAKE_INSTALL_LIBDIR}")
set(includedir "\${prefix}/${CMAKE_INSTALL_INCLUDEDIR}/${CUSTOM_PREFIX}")

configure_file(libjsonrpc.pc.in ${CMAKE_CURRENT_BINARY_DIR}/libjsonrpc.pc @ONLY)

install(FILES
  ${CMAKE_CURRENT_BINARY_DIR}/libjsonrpc.pc
  DESTINATION ${CMAKE_INSTALL_LIBDIR}/pkgconfig
  )

add_subdirectory (src)