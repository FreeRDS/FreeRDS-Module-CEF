# FreeRDP X11 Server Next Generation
# xrdp-ng cmake build script
#
# Copyright 2013 Marc-Andre Moreau <marcandre.moreau@gmail.com>
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

set(MODULE_NAME "freerds-cef-service")
set(MODULE_PREFIX "FREERDS_CEF_SERVICE")

set(${MODULE_PREFIX}_SRCS
	cef_service.c
	cef_service.h)

include_directories(${CEF_INCLUDE_DIRS})

add_executable(${MODULE_NAME} ${${MODULE_PREFIX}_SRCS})

set_target_properties(${MODULE_NAME} PROPERTIES OUTPUT_NAME "freerds-cef" RUNTIME_OUTPUT_DIRECTORY "..")

list(APPEND ${MODULE_PREFIX}_LIBS freerds-module)

list(APPEND ${MODULE_PREFIX}_LIBS ${CEF_LIBRARIES})

if(WinPR_MONOLITHIC_BUILD)
	list(APPEND ${MODULE_PREFIX}_LIBS winpr)
else()
	list(APPEND ${MODULE_PREFIX}_LIBS winpr-crt winpr-synch winpr-thread winpr-environment winpr-utils)
endif()

target_link_libraries(${MODULE_NAME} ${${MODULE_PREFIX}_LIBS})

install(TARGETS ${MODULE_NAME} DESTINATION ${CMAKE_INSTALL_BINDIR})
