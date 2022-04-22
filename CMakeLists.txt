# Copyright 2021 The Android Open Source Project
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#      http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

project(make)
cmake_minimum_required(VERSION 3.18.1)

add_executable(make
  # make_SRCS
  src/ar.c
  src/arscan.c
  src/commands.c
  src/default.c
  src/dir.c
  src/expand.c
  src/file.c
  src/function.c
  src/getopt.c
  src/getopt1.c
  src/guile.c
  src/hash.c
  src/implicit.c
  src/job.c
  src/load.c
  src/loadapi.c
  src/main.c
  src/misc.c
  src/output.c
  src/read.c
  src/remake.c
  src/rule.c
  src/signame.c
  src/strcache.c
  src/variable.c
  src/version.c
  src/vpath.c

  # USE_CUSTOMS
  src/remote-stub.c
)

target_compile_definitions(make PRIVATE -DHAVE_CONFIG_H)
target_include_directories(make PRIVATE lib src)

if(UNIX)
  if(CMAKE_SYSTEM_NAME STREQUAL Linux)
    target_include_directories(make PRIVATE soong/linux_glibc)
    target_sources(make PRIVATE lib/getloadavg.c)
    target_link_libraries(make PRIVATE -ldl)
  elseif(APPLE)
    target_include_directories(make PRIVATE soong/darwin)
  endif()
  target_compile_definitions(make PRIVATE
    -DLOCALEDIR=NULL
    -DINCLUDEDIR=0
    -DLIBDIR=0
  )
  target_sources(make PRIVATE
    src/posixos.c
    lib/concat-filename.c
    lib/exitfail.c
    lib/fnmatch.c
    lib/findprog-in.c
    lib/glob.c
    lib/xalloc-die.c
    lib/xconcat-filename.c
  )
endif()

if(WIN32)
  target_include_directories(make PRIVATE
    soong/windows
    src/w32/include
  )
  target_compile_definitions(make PRIVATE -DWINDOWS32)
  target_sources(make PRIVATE
    src/w32/pathstuff.c
    src/w32/w32os.c
    src/w32/compat/posixfcn.c
    src/w32/subproc/misc.c
    src/w32/subproc/sub_proc.c
    src/w32/subproc/w32err.c
    lib/fnmatch.c
    lib/glob.c
    lib/getloadavg.c
  )
endif()

install(TARGETS make)
