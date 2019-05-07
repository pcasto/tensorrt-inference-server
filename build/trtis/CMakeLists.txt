# Copyright (c) 2019, NVIDIA CORPORATION. All rights reserved.
#
# Redistribution and use in source and binary forms, with or without
# modification, are permitted provided that the following conditions
# are met:
#  * Redistributions of source code must retain the above copyright
#    notice, this list of conditions and the following disclaimer.
#  * Redistributions in binary form must reproduce the above copyright
#    notice, this list of conditions and the following disclaimer in the
#    documentation and/or other materials provided with the distribution.
#  * Neither the name of NVIDIA CORPORATION nor the names of its
#    contributors may be used to endorse or promote products derived
#    from this software without specific prior written permission.
#
# THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS ``AS IS'' AND ANY
# EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE
# IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR
# PURPOSE ARE DISCLAIMED.  IN NO EVENT SHALL THE COPYRIGHT OWNER OR
# CONTRIBUTORS BE LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL,
# EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED TO,
# PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS OF USE, DATA, OR
# PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY
# OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT
# (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE
# OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.

cmake_minimum_required (VERSION 3.5)
project (trtis)

if(NOT CMAKE_BUILD_TYPE)
  set(CMAKE_BUILD_TYPE Release)
endif()

set(CMAKE_CXX_FLAGS "-Wall -Wextra -Wno-unused-parameter -Werror")
set(CMAKE_CXX_FLAGS_DEBUG "-g")
set(CMAKE_CXX_FLAGS_RELEASE "-O3")
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_POSITION_INDEPENDENT_CODE ON)

if(${TRTIS_ENABLE_METRICS})
  add_definitions(-DTRTIS_ENABLE_METRICS=1)
endif() # TRTIS_ENABLE_METRICS

if(${TRTIS_ENABLE_TENSORFLOW})
  add_definitions(-DTRTIS_ENABLE_TENSORFLOW=1)
endif() # TRTIS_ENABLE_TENSORFLOW

if(${TRTIS_ENABLE_TENSORRT})
  add_definitions(-DTRTIS_ENABLE_TENSORRT=1)
endif() # TRTIS_ENABLE_TENSORRT

if(${TRTIS_ENABLE_CAFFE2})
  add_definitions(-DTRTIS_ENABLE_CAFFE2=1)
endif() # TRTIS_ENABLE_CAFFE2

if(${TRTIS_ENABLE_CUSTOM})
  add_definitions(-DTRTIS_ENABLE_CUSTOM=1)
endif() # TRTIS_ENABLE_CUSTOM

# For more recent cmakes this should not be necessary
set(CUDA_TOOLKIT_INCLUDE_DIRECTORIES "/usr/local/cuda/include")

include_directories("${PROJECT_SOURCE_DIR}/../..")
include_directories("${PROJECT_BINARY_DIR}")

#
# libevent
#
find_package(Libevent CONFIG REQUIRED)
message(STATUS "Using libevent ${Libevent_VERSION}")
include_directories(${LIBEVENT_INCLUDE_DIRS})

#
# Protobuf
#
set(protobuf_MODULE_COMPATIBLE TRUE)
find_package(Protobuf CONFIG REQUIRED)
message(STATUS "Using protobuf ${Protobuf_VERSION}")
include_directories(${Protobuf_INCLUDE_DIRS})

#
# GRPC
#
find_package(gRPC CONFIG REQUIRED)
message(STATUS "Using gRPC ${gRPC_VERSION}")
include_directories($<TARGET_PROPERTY:gRPC::grpc,INTERFACE_INCLUDE_DIRECTORIES>)

add_subdirectory(../../src/core src/core)
add_subdirectory(../../src/nvrpc src/nvrpc)
add_subdirectory(../../src/backends/caffe2 src/backends/caffe2)
add_subdirectory(../../src/backends/custom src/backends/custom)
add_subdirectory(../../src/backends/ensemble src/backends/ensemble)
add_subdirectory(../../src/backends/tensorflow src/backends/tensorflow)
add_subdirectory(../../src/backends/tensorrt src/backends/tensorrt)
add_subdirectory(../../src/servers src/servers)