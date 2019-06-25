cmake_minimum_required(VERSION 2.8)

project(miximus-emscripten)

set(CMAKE_VERBOSE_MAKEFILE on)
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/build.emscripten)

set(CMAKE_CXX_FLAGS "-fno-rtti -O3 --llvm-lto 1 --std=c++11 -s ALLOW_MEMORY_GROWTH=1 -s DISABLE_EXCEPTION_CATCHING=0")

set(CMAKE_PREFIX_PATH ${CMAKE_CURRENT_SOURCE_DIR}/installroot)

set(PKG_CONFIG_USE_CMAKE_PREFIX_PATH on)

set(USE_ASM OFF)

set(GMP_LIBRARY ${CMAKE_CURRENT_SOURCE_DIR}/installroot/lib/libgmp.a)
set(GMP_INCLUDE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/installroot/include)
set(GMP_LIBRARIES ${CMAKE_CURRENT_SOURCE_DIR}/installroot/lib/libgmp.a)

include_directories(SYSTEM ${CMAKE_CURRENT_SOURCE_DIR}/installroot/include)

add_subdirectory(ethsnarks-miximus/circuit)


# Bleh.. have to double-escape stuff and prefix with "SHELL:..." otherwise it combines multiple options into a single `-s` flag

target_link_options(miximus_cli PUBLIC "SHELL:-s EXPORTED_FUNCTIONS=[\\\"_miximus_prove_json\\\",\\\"_miximus_verify\\\",\\\"_miximus_tree_depth\\\"]")

target_link_options(miximus_cli PUBLIC "SHELL:-s EXTRA_EXPORTED_RUNTIME_METHODS=[\\\"ccall\\\",\\\"cwrap\\\"]")

target_link_options(miximus_cli PUBLIC "SHELL:-s FORCE_FILESYSTEM=1")

target_link_options(miximus_cli PUBLIC "SHELL:-s DEFAULT_LIBRARY_FUNCS_TO_INCLUDE=[\\\"$Browser\\\"]")