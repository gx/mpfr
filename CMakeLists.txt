# Copyright 2019 Free Software Foundation, Inc.
#
#  This file is part of the GNU MP Library.
#
#  The GNU MP Library is free software; you can redistribute it and/or modify
#  it under the terms of either:
#
#    * the GNU Lesser General Public License as published by the Free
#      Software Foundation; either version 3 of the License, or (at your
#      option) any later version.
#
#  or
#
#    * the GNU General Public License as published by the Free Software
#      Foundation; either version 2 of the License, or (at your option) any
#      later version.
#
#  or both in parallel, as here.
#
#  The GNU MP Library is distributed in the hope that it will be useful, but
#  WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY
#  or FITNESS FOR A PARTICULAR PURPOSE.  See the GNU General Public License
#  for more details.
#
#  You should have received copies of the GNU General Public License and the
#  GNU Lesser General Public License along with the GNU MP Library.  If not,
#  see https://www.gnu.org/licenses/.


cmake_minimum_required(VERSION 3.12.2)

project(MPFR
  VERSION 4.0.2
  DESCRIPTION "GNU portable C library for arbitrary-precision binary floating-point computation with correct rounding"
  HOMEPAGE_URL "https://www.mpfr.org/"
  LANGUAGES C)

set(MPFR_LT_CURRENT 6)
set(MPFR_LT_AGE 2)
set(MPFR_LT_REVISION 0)

set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin)
set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)
set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib)

# Use solution folders for Visual Studio
set_property(GLOBAL PROPERTY USE_FOLDERS ON)

# Test endian type.
include(TestBigEndian)
test_big_endian(big_endian)
if(big_endian)
  set(HAVE_DOUBLE_IEEE_BIG_ENDIAN 1)
  set(HAVE_BIG_ENDIAN 1)
else()
  set(HAVE_DOUBLE_IEEE_LITTLE_ENDIAN 1)
  set(HAVE_LITTLE_ENDIAN 1)
endif()

include(CheckIncludeFile)
check_include_file(inttypes.h HAVE_INTTYPES_H)
check_include_file(stdarg.h HAVE_STDARG)
check_include_file(stdint.h HAVE_STDINT_H)
check_include_file(locale.h HAVE_LOCALE_H)

include(CheckTypeSize)
check_type_size("long long" LONG_LONG)

include(CheckFunctionExists)
check_function_exists(clock_gettime HAVE_CLOCK_GETTIME)
check_function_exists(sigaction HAVE_SIGACTION)
check_function_exists(signal HAVE_SIGNAL)

include(CheckSymbolExists)
check_symbol_exists(va_copy stdarg.h HAVE_VA_COPY)
check_symbol_exists(__va_copy stdarg.h HAVE___VA_COPY)

include(CheckCSourceRuns)
include(CheckCSourceCompiles)

set(CMAKE_REQUIRED_INCLUDES ${CMAKE_SOURCE_DIR}/src)
check_c_source_runs(
  "#define MPFR_USE_THREAD_SAFE 1\n#define MPFR_USE_C11_THREAD_SAFE 1\n#include \"mpfr-thread.h\"\nMPFR_THREAD_ATTR int x = 17;\nint main() { return x != 17; }\n"
  resultVar)
if(resultVar)
  set(MPFR_USE_THREAD_SAFE 1)
  set(MPFR_USE_C11_THREAD_SAFE 1)
endif()
if(NOT MPFR_USE_C11_THREAD_SAFE)
  check_c_source_runs(
    "#define MPFR_USE_THREAD_SAFE 1\n#include \"mpfr-thread.h\"\nMPFR_THREAD_ATTR int x = 17;\nint main() { return x != 17; }\n"
    resultVar)
  if(resultVar)
    set(MPFR_USE_THREAD_SAFE 1)
  endif()
endif()
check_c_source_compiles("#include \"mpfr-intmax.h\"\n intmax_t x = INTMAX_MAX; (void) x;\nint main() {}\n" resultVar)
if(resultVar)
  set(MPFR_HAVE_INTMAX_MAX 1)
endif()
unset(CMAKE_REQUIRED_INCLUDES)

configure_file(src/config.h.in config.h)
configure_file(src/mparam_h.in mparam.h)
include_directories(
  ${CMAKE_CURRENT_SOURCE_DIR}/src
  ${CMAKE_CURRENT_BINARY_DIR})

# MPFR requires GMP version 5.0.0 or later.
find_package(GMP 5.0.0 REQUIRED)

if(MSVC)
  add_compile_options(/wd4018 /wd4146 /wd4244 /wd4267 /wd4996)
endif()

add_compile_definitions(HAVE_CONFIG_H)

add_subdirectory(src)
#add_subdirectory(tune)
add_subdirectory(examples)
add_subdirectory(tools/bench)

include(CTest)
enable_testing()
if(BUILD_TESTING)
  add_subdirectory(tests)
endif()
