# This software is distributed under the terms of the MIT License.
# Copyright (c) 2024 OpenCyphal.
# Author: Maksim Drachov <maksim.drachov@zubax.com>

cmake_minimum_required(VERSION 3.20.0)

get_filename_component(WORKSPACE_DIR ${CMAKE_CURRENT_LIST_DIR}/.. ABSOLUTE)

# This sets common build options for all targets
# set(ZephyrBuildConfiguration_ROOT ${WORKSPACE_DIR}/cmake/modules)

# Set up all the necessary, additional devicetree overlays
# set(DTC_OVERLAY_FILE ${CMAKE_CURRENT_LIST_DIR}/boards/arm/custom_board/ADC.overlay)

# Include all the necessary Kconfig files
set(CONF_FILE ${CMAKE_CURRENT_LIST_DIR}/prj.conf)
# set(CONF_FILE ${WORKSPACE_DIR}/kconfig/cpp.conf)
# list(APPEND CONF_FILE ${WORKSPACE_DIR}/kconfig/ethernet.conf)
# list(CONF_FILE ${CMAKE_CURRENT_LIST_DIR}/kconfig/ethernet.conf) # Application-specific (unlike others which are shared with the verification builds)

find_package(Zephyr REQUIRED HINTS $ENV{ZEPHYR_BASE})

# set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} ${WORKSPACE_DIR}/cmake/modules)

project(opencyphal_zephyr VERSION 0.1.0)

# Set up the application target
# This should contain all the sources (*.cpp and *.c)
file(
    GLOB
    app_sources
    src/*.cpp
    src/*.c # TODO: remove this, we will use C++ only
    platform/*.cpp
    platform/ethernet/*.cpp
    src/registry/*.cpp
    src/cyphal_node/*.cpp
)
target_sources(app PRIVATE ${app_sources})
# This should contain all the include directories (*.hpp and *.h)
target_include_directories(app PRIVATE
    ${CMAKE_CURRENT_SOURCE_DIR}/platform
    ${CMAKE_CURRENT_SOURCE_DIR}/src
    ${CMAKE_CURRENT_SOURCE_DIR}/src/registry
    ${CMAKE_CURRENT_SOURCE_DIR}/src/cyphal_node
)

# Necessary for NodeInfo (OpenCyphal)
# execute_process(
#     COMMAND git rev-parse --short=16 HEAD
#     WORKING_DIRECTORY "${WORKSPACE_DIR}"
#     OUTPUT_VARIABLE vcs_revision_id
#     OUTPUT_STRIP_TRAILING_WHITESPACE
# )
# target_compile_definitions(
#     app PRIVATE -DAPP_SOFTWARE_VERSION=${PROJECT_VERSION_MAJOR}
#                 -DAPP_SOFTWARE_VERSION_MINOR=${PROJECT_VERSION_MINOR}
#                 -DVCS_REVISION_ID=0x${vcs_revision_id}ULL
# )

# Clang-tidy

# Generate code from DSDL using the Nunavut transpiler.
# find_package(nnvg REQUIRED)
# set(dsdl_root_namespace_dirs # List all DSDL root namespaces to transpile here.
#     ${WORKSPACE_DIR}/lib/public_regulated_data_types/uavcan
#     # ${WORKSPACE_DIR}/lib/zubax_dsdl/zubax # Add you custom dsdl here
# )
# set(nunavut_out "${CMAKE_BINARY_DIR}/nunavut_out")
# message(STATUS "Generating DSDL support code into ${nunavut_out}")
# create_dsdl_target(
#     "nunavut_support"
#     c
#     ${nunavut_out}
#     ""
#     OFF
#     little
#     "only"
# )
# foreach(ns_dir ${dsdl_root_namespace_dirs})
#     get_filename_component(ns ${ns_dir} NAME)
#     message(STATUS "DSDL namespace ${ns} at ${ns_dir}")
#     create_dsdl_target(
#         "dsdl_${ns}" # CMake target name
#         c # Target language to transpile into
#         ${nunavut_out} # Destination directory (add it to the includes)
#         ${ns_dir} # Source directory
#         OFF # Disable variable array capacity override
#         little # Endianness of the target platform (alternatives: "big", "any")
#         "never" # Support files are generated once in the nunavut-support target (above)
#         ${dsdl_root_namespace_dirs} # Look-up DSDL namespaces
#     )
#     add_dependencies("dsdl_${ns}" nunavut_support)
# endforeach()
# add_definitions(-DNUNAVUT_ASSERT=assert)

# zephyr_get_compile_options_for_lang(C options)
# zephyr_get_compile_definitions_for_lang(C definitions)

# Dirty hack to remove the zephyr_stdint.h include from the compiler options
# set(string_to_remove_from_options
#     "$<SEMICOLON>$<TARGET_PROPERTY:compiler,imacros>$<SEMICOLON>$<WORKSPACE_DIR>/zephyr/include/zephyr/toolchain/zephyr_stdint.h$<SEMICOLON>"
# )
# # Replace in string_to_remove_from_options $<WORKSPACE_DIR> with the actual workspace directory
# string(REPLACE "$<WORKSPACE_DIR>" "${WORKSPACE_DIR}" string_to_remove_from_options "${string_to_remove_from_options}")
# # Now use string_to_remove_from_options on ${options}
# string(REPLACE "${string_to_remove_from_options}" "" modified_options "${options}")

# Cyphal
# target_include_directories(app SYSTEM PRIVATE ${nunavut_out})
# cavl
# target_include_directories(app SYSTEM PRIVATE ${WORKSPACE_DIR}/lib/cavl/c++)
# fixed point library
# target_include_directories(app SYSTEM PRIVATE ${WORKSPACE_DIR}/lib/fixed_point)
# libcanard library
# add_library(canard STATIC ${WORKSPACE_DIR}/lib/libcanard/libcanard/canard.c)
# target_include_directories(canard PUBLIC ${WORKSPACE_DIR}/lib/libcanard/libcanard)
# target_compile_options(canard PRIVATE ${modified_options})
# target_compile_definitions(canard PRIVATE ${definitions})
# Add dependencies to the application
# target_link_libraries(app PUBLIC canard)
# add_dependencies(app dsdl_uavcan)
# target_include_directories(app SYSTEM PUBLIC ${nunavut_out}) # TODO: this is a hack, use PUBLIC INTERFACE instead
