cmake_minimum_required(VERSION 3.25)

project(quickjs C)

file(STRINGS VERSION quickjs_version)

add_library(quickjs OBJECT)

set_target_properties(
  quickjs
  PROPERTIES
  C_STANDARD 99
  POSITION_INDEPENDENT_CODE ON
)

target_sources(
  quickjs
  INTERFACE
    quickjs.h
  PRIVATE
    quickjs.c
    libregexp.c
    libunicode.c
    libbf.c
    cutils.c
)

target_include_directories(
  quickjs
  PUBLIC
    .
)

target_compile_definitions(
  quickjs
  PUBLIC
    CONFIG_BIGNUM
    CONFIG_VERSION="${quickjs_version}"
  PRIVATE
    $<$<CONFIG:Debug>:DUMP_LEAKS>
)

add_library(quickjs_shared SHARED)

set_target_properties(
  quickjs_shared
  PROPERTIES
  OUTPUT_NAME quickjs
  WINDOWS_EXPORT_ALL_SYMBOLS ON
)

target_link_libraries(
  quickjs_shared
  PUBLIC
    quickjs
)

if(UNIX)
  target_link_libraries(
    quickjs_shared
    PRIVATE
      m
  )
endif()

add_library(quickjs_static STATIC)

set_target_properties(
  quickjs_static
  PROPERTIES
  OUTPUT_NAME quickjs
  PREFIX lib
)

target_link_libraries(
  quickjs_static
  PUBLIC
    quickjs
)

if(UNIX)
  target_link_libraries(
    quickjs_static
    PRIVATE
      m
  )
endif()

install(TARGETS quickjs_shared quickjs_static)
