# CMakeLists.txt
#
# Wireshark - Network traffic analyzer
# By Gerald Combs <gerald@wireshark.org>
# Copyright 1998 Gerald Combs
#
# SPDX-License-Identifier: GPL-2.0-or-later
#

# @gyk: This is based on Wireshark's Gryphon protocol example
# (https://github.com/wireshark/wireshark/blob/master/plugins/epan/gryphon/CMakeLists.txt), with
# some changes.

project(rtmpt2-dissector C)

cmake_minimum_required(VERSION 3.7)

set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} ${CMAKE_CURRENT_SOURCE_DIR}/cmake)

include(WiresharkPlugin)
include(UseMakePluginReg)

# We simply don't check APIs.
macro(CHECKAPI)
endmacro()

# These should be updated by make-version.pl, but let's manually set them.
set(PROJECT_MAJOR_VERSION 2)
set(PROJECT_MINOR_VERSION 6)
set(PROJECT_RELEASE_VERSION "${PROJECT_MAJOR_VERSION}.${PROJECT_MINOR_VERSION}")

# Plugin name and version info (major minor micro extra)
set_module_info(rtmpt2 0 0 1 0)

set(DISSECTOR_SRC
	src/packet-rtmpt2.c
)

set(PLUGIN_FILES
	plugin.c
	${DISSECTOR_SRC}
)

set_source_files_properties(
	${PLUGIN_FILES}
	PROPERTIES
	COMPILE_FLAGS "${WERROR_COMMON_FLAGS}"
)

include_directories(${CMAKE_CURRENT_SOURCE_DIR})
include_directories(${CMAKE_BINARY_DIR})

configure_file(${CMAKE_SOURCE_DIR}/cmakeconfig.h.in ${CMAKE_BINARY_DIR}/config.h)

find_path(WIRESHARK_INCLUDE_DIRS epan/column-info.h PATH_SUFFIXES wireshark)
if(WIRESHARK_INCLUDE_DIRS)
  message(STATUS "Found wireshark libs at ${WIRESHARK_INCLUDE_DIRS}")
else()
  message(STATUS "Wireshark libs not found!")
endif()
include_directories(${WIRESHARK_INCLUDE_DIRS})

if(NOT GLIB2_INCLUDE_DIRS)
  find_package(GLIB2)
endif()
include_directories(${GLIB2_INCLUDE_DIRS})

set(LINK_MODE_LIB SHARED)
set(LINK_MODE_MODULE MODULE)

register_plugin_files(plugin.c
	plugin
	${DISSECTOR_SRC}
)

add_library(rtmpt2
  ${LINK_MODE_MODULE}
  ${PLUGIN_FILES}
)

set_target_properties(rtmpt2 PROPERTIES PREFIX "")

target_link_libraries(rtmpt2 wireshark glib-2.0)

install_plugin(rtmpt2 epan)

file(GLOB DISSECTOR_HEADERS RELATIVE "${CMAKE_CURRENT_SOURCE_DIR}" "*.h")
CHECKAPI(
	NAME
	  rtmpt2
	SWITCHES
	  -g abort -g termoutput -build
	SOURCES
	  ${DISSECTOR_SRC}
	  ${DISSECTOR_HEADERS}
)

#
# Editor modelines  -  http://www.wireshark.org/tools/modelines.html
#
# Local variables:
# c-basic-offset: 8
# tab-width: 8
# indent-tabs-mode: t
# End:
#
# vi: set shiftwidth=8 tabstop=8 noexpandtab:
# :indentSize=8:tabSize=8:noTabs=false:
#