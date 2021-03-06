# ISC License
#
# Copyright (c) 2018 Jakob Dübel
#
# Permission to use, copy, modify, and/or distribute this software for
# any purpose with or without fee is hereby granted, provided that the
# above copyright notice and this permission notice appear in all
# copies.
#
# THE SOFTWARE IS PROVIDED "AS IS" AND THE AUTHOR DISCLAIMS ALL
# WARRANTIES WITH REGARD TO THIS SOFTWARE INCLUDING ALL IMPLIED
# WARRANTIES OF MERCHANTABILITY AND FITNESS. IN NO EVENT SHALL THE
# AUTHOR BE LIABLE FOR ANY SPECIAL, DIRECT, INDIRECT, OR CONSEQUENTIAL
# DAMAGES OR ANY DAMAGES WHATSOEVER RESULTING FROM LOSS OF USE, DATA
# OR PROFITS, WHETHER IN AN ACTION OF CONTRACT, NEGLIGENCE OR OTHER
# TORTIOUS ACTION, ARISING OUT OF OR IN CONNECTION WITH THE USE OR
# PERFORMANCE OF THIS SOFTWARE.

cmake_minimum_required(VERSION 3.4.3)

# For versioning read https://semver.org/
project(mod-midi-merger
    VERSION 0.1.0
    LANGUAGES C)

set(CMAKE_C_STANDARD 99)
set(CMAKE_BUILD_TYPE Release)

set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${CMAKE_SOURCE_DIR}/cmake/Modules/")
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -Wall -Wextra -Wno-unused-parameter")

# Jack dependency
find_package(Jack REQUIRED)
include_directories(${JACK_INCLUDE_DIRS})
set(LIBS ${LIBS} ${JACK_LIBRARIES})

# Pthread dependency
find_package(Threads)
set(LIBS ${LIBS} ${CMAKE_THREAD_LIBS_INIT})

# Position independed code is set automatically
add_library(${PROJECT_NAME} MODULE
            src/midi-merger.h src/midi-merger.c)
SET_TARGET_PROPERTIES(${PROJECT_NAME} PROPERTIES PREFIX "")

add_library(mod-midi-broadcaster MODULE
            src/midi-broadcaster.h src/midi-broadcaster.c)
SET_TARGET_PROPERTIES(mod-midi-broadcaster PROPERTIES PREFIX "")

add_executable(${PROJECT_NAME}-standalone
               src/standalone-midi-merger.c
               src/midi-merger.h src/midi-merger.c)
target_link_libraries(${PROJECT_NAME}-standalone ${LIBS})

add_executable(mod-midi-broadcaster-standalone
               src/standalone-midi-broadcaster.c
               src/midi-broadcaster.h src/midi-broadcaster.c)
target_link_libraries(mod-midi-broadcaster-standalone ${LIBS})

set(CMAKE_INSTALL_PREFIX /usr)
install(TARGETS ${PROJECT_NAME} LIBRARY DESTINATION ${CMAKE_INSTALL_PREFIX}/lib/jack)
install(TARGETS mod-midi-broadcaster LIBRARY DESTINATION ${CMAKE_INSTALL_PREFIX}/lib/jack)
