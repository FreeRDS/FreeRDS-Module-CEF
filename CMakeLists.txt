cmake_minimum_required(VERSION 2.8)

set(MODULE_NAME "freerds-cef")
set(MODULE_PREFIX "FREERDS_CEF")

project(${MODULE_NAME} C)

include(GNUInstallDirs)
include(FindPackageHandleStandardArgs)

find_package(WinPR CONFIG REQUIRED)
find_package(FreeRDS CONFIG REQUIRED)

include_directories(${WinPR_INCLUDE_DIR})
include_directories(${FreeRDS_INCLUDE_DIR})

find_path(CEF_INCLUDE_DIR
	NAMES cef_version.h
	PATHS /opt/cef/include)

find_library(CEF_DEBUG_LIBRARY
	NAMES cef
	PATHS /opt/cef/Debug)

find_library(CEF_RELEASE_LIBRARY
	NAMES cef
	PATHS /opt/cef/Release)

if(CMAKE_BUILD_TYPE MATCHES "Debug")
	set(CEF_LIBRARY ${CEF_DEBUG_LIBRARY})
else()
	set(CEF_LIBRARY ${CEF_RELEASE_LIBRARY})
endif()

find_package_handle_standard_args(CEF DEFAULT_MSG CEF_LIBRARY CEF_INCLUDE_DIR)

if(CEF_FOUND)
	set(CEF_LIBRARIES ${CEF_LIBRARY})
	set(CEF_INCLUDE_DIRS ${CEF_INCLUDE_DIR})
endif()

set(${MODULE_PREFIX}_SRCS
	cef_module.c
	cef_module.h)

add_library(${MODULE_NAME} ${${MODULE_PREFIX}_SRCS})

list(APPEND ${MODULE_PREFIX}_LIBS freerds-module)

if(WinPR_MONOLITHIC_BUILD)
	list(APPEND ${MODULE_PREFIX}_LIBS winpr)
else()
	list(APPEND ${MODULE_PREFIX}_LIBS winpr-crt winpr-synch winpr-thread winpr-environment winpr-utils winpr-utils)
endif()

target_link_libraries(${MODULE_NAME} ${${MODULE_PREFIX}_LIBS})

install(TARGETS ${MODULE_NAME} DESTINATION ${CMAKE_INSTALL_LIBDIR})

if(CEF_FOUND)
	add_subdirectory(service)
endif()

