# Copyright 2017 MongoDB Inc.
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
# http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

# Demonstrates how to use the CMake 'find_package' mechanism to locate build against the libmongocxx
# static library.

cmake_minimum_required(VERSION 3.10 FATAL_ERROR)

if(POLICY CMP0025)
    cmake_policy(SET CMP0025 NEW)
endif()

project(HELLO_WORLD LANGUAGES C CXX)

# Enforce the C++ standard, and disable extensions.
if(NOT DEFINED CMAKE_CXX_STANDARD)
    set(CMAKE_CXX_STANDARD 17)
endif()

set(CMAKE_CXX_EXTENSIONS OFF)

if (NOT CMAKE_CXX_COMPILER_ID STREQUAL "MSVC")
    # set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall -Wextra")
    # set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall -Wextra -Werror")
endif()

include_directories(
    include
)

# set(PROJECT_EXAMPLES
#     src/First.cpp
#     src/Second.cpp
#     src/STLContainerStream.cpp
#     src/ObserverFromScratch.cpp
#     src/Concat.cpp
# )

file(GLOB PROJECT_EXAMPLES "src/*.cpp")

foreach(SRC ${PROJECT_EXAMPLES})
    get_filename_component(EXAMPLE_TARGET ${SRC} NAME_WE)
    add_executable(${EXAMPLE_TARGET} ${SRC})
    target_include_directories(${EXAMPLE_TARGET} PUBLIC ${CMAKE_CURRENT_LIST_DIR})
    # Visual Studio pre 2017 requires boost polyfill.
    if (CMAKE_CXX_COMPILER_ID STREQUAL "MSVC" AND CMAKE_CXX_STANDARD LESS 17)
    # find_package(Boost 1.56.0 REQUIRED)
    # if (CMAKE_VERSION VERSION_LESS 3.15.0)
    #     target_include_directories(hello_example PRIVATE ${Boost_INCLUDE_DIRS})
    # else()
    #     target_link_libraries(hello_example PRIVATE Boost::boost)
    # endif()
    endif()

    target_link_libraries(${EXAMPLE_TARGET}
        pthread
    )
endforeach(SRC)

add_custom_target(run
    COMMAND hello_example
    DEPENDS hello_example
    WORKING_DIRECTORY ${CMAKE_PROJECT_DIR}
)