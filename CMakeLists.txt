# libcouchbase
# Copyright (C) 2013 Couchbase, Inc
# All rights reserved.
#
#   Licensed under the Apache License, Version 2.0 (the "License");
#   you may not use this file except in compliance with the License.
#   You may obtain a copy of the License at
#
#       http://www.apache.org/licenses/LICENSE-2.0
#
#   Unless required by applicable law or agreed to in writing, software
#   distributed under the License is distributed on an "AS IS" BASIS,
#   WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
#   See the License for the specific language governing permissions and
#   limitations under the License.

CMAKE_MINIMUM_REQUIRED(VERSION 2.8)
# Currently this file is only used for Windows

SET(SOURCE_ROOT ${CMAKE_SOURCE_DIR})
INCLUDE_DIRECTORIES(BEFORE ${SOURCE_ROOT}/include
                           ${SOURCE_ROOT}/win32
                           ${SOURCE_ROOT}/contrib
                           ${SOURCE_ROOT}/plugins/io/win32
                           ${SOURCE_ROOT}/src
                           ${SOURCE_ROOT})

ADD_DEFINITIONS(-D_CRT_SECURE_NO_WARNINGS)
SET(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} /W3 /EHsc /FD")

ADD_LIBRARY(couchbase SHARED
            src/arithmetic.c src/base64.c src/behavior.c src/cookie.c
            src/error.c src/event.c src/flush.c src/get.c
            src/handler.c src/instance.c src/iofactory.c src/packet.c
            src/remove.c src/ringbuffer.c src/hashset.c src/server.c
            src/stats.c src/store.c src/strerror.c src/synchandler.c
            src/timeout.c src/timings.c src/touch.c src/utilities.c
            src/wait.c src/gethrtime.c plugins/io/win32/plugin-win32.c
            src/isasl.c src/compat.c contrib/http_parser/http_parser.c
            src/http.c src/observe.c src/timer.c src/verbosity.c
            src/sanitycheck.c src/url_encoding.c src/config_cache.c
            contrib/libvbucket/crc32.c contrib/libvbucket/cJSON.c
            contrib/libvbucket/vbucket.c contrib/libvbucket/ketama.c
            contrib/libvbucket/rfc1321/md5c.c)

SET_TARGET_PROPERTIES(couchbase PROPERTIES
                      COMPILE_FLAGS -DLIBCOUCHBASE_INTERNAL=1)
TARGET_LINK_LIBRARIES(couchbase ws2_32.lib)

ADD_EXECUTABLE(cbc tools/cbc.cc tools/cbc-util.cc
                   tools/commandlineparser.cc
                   tools/configuration.cc win32/getopt.c)
TARGET_LINK_LIBRARIES(cbc couchbase)

ADD_EXECUTABLE(cbc-pillowfight
                               tools/commandlineparser.cc
                               tools/cbc-pillowfight.cc
                               src/gethrtime.c win32/getopt.c)
TARGET_LINK_LIBRARIES(cbc-pillowfight couchbase)

INSTALL(TARGETS couchbase
        RUNTIME DESTINATION bin
        LIBRARY DESTINATION lib
        ARCHIVE DESTINATION lib)

INSTALL(TARGETS cbc cbc-pillowfight RUNTIME DESTINATION bin)
INSTALL(FILES include/libcouchbase/behavior.h
              include/libcouchbase/callbacks.h
              include/libcouchbase/compat.h
              win32/libcouchbase/configuration.h
              include/libcouchbase/couchbase.h
              include/libcouchbase/error.h
              include/libcouchbase/timings.h
              include/libcouchbase/types.h
              include/libcouchbase/http.h
              include/libcouchbase/arguments.h
              include/libcouchbase/sanitycheck.h
              include/libcouchbase/visibility.h
        DESTINATION include/libcouchbase)
