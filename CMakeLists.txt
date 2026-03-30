# CMake file for crosscompiling ECL for Windows
# SPDX-License-Identifier: GPL-2.0-or-later
# Copyright (C) by Wolfgang Dautermann
# License GPLv2+: GNU GPL version 2 or later <http://gnu.org/licenses/gpl.html>
# This is free software: you are free to change and redistribute it.
# There is NO WARRANTY, to the extent permitted by law.

cmake_policy(SET CMP0135 NEW)


set(CMAKE_SYSTEM_NAME Windows)

option(WITH_ASDF "Include ASDF." NO)
if(WITH_ASDF)
    set(ASDF_OPTION "--with-asdf=yes")
else()
    set(ASDF_OPTION "--with-asdf=no")
endif()

message (STATUS "ASDF: ${ASDF_OPTION}")
set(HOST x86_64-w64-mingw32)
set(INSTALLERSUFFIX "win64")

set(CMAKE_C_COMPILER   ${HOST}-gcc)
set(CMAKE_RC_COMPILER  ${HOST}-windres)

cmake_minimum_required(VERSION 3.16)
project(ecl
    LANGUAGES C
    DESCRIPTION "ECL (Embeddable Common-Lisp) is an interpreter of the Common-Lisp language."
    HOMEPAGE_URL "https://ecl.common-lisp.dev/"
    VERSION 26.3.27)

# What is the build system?
execute_process(COMMAND gcc -dumpmachine OUTPUT_VARIABLE BUILDHOST OUTPUT_STRIP_TRAILING_WHITESPACE)
message(STATUS "Build system: ${BUILDHOST}")

# Output the host system, for which we are compiling
message(STATUS "Host system: ${HOST}")

# Where are libgcc and libwinpthread?
execute_process(COMMAND "${CMAKE_C_COMPILER}" -print-file-name=libgcc_s_seh-1.dll OUTPUT_VARIABLE MINGW_LIBGCC    OUTPUT_STRIP_TRAILING_WHITESPACE)
execute_process(COMMAND "${CMAKE_C_COMPILER}" -print-file-name=libwinpthread-1.dll OUTPUT_VARIABLE MINGW_LIBWINPTHREAD OUTPUT_STRIP_TRAILING_WHITESPACE)
message(STATUS "Found libgcc at ${MINGW_LIBGCC}")
message(STATUS "Found libwinpthread at ${MINGW_LIBWINPTHREAD}")

include(ExternalProject)

add_subdirectory("gmplib/")
add_subdirectory("ecl/")
# Needed for non-bytecode build:
# add_subdirectory("winlibs/")

set(CPACK_GENERATOR "NSIS;ZIP")
set(CPACK_PACKAGE_VENDOR "ECL Team")
set(CPACK_RESOURCE_FILE_LICENSE "${CMAKE_SOURCE_DIR}/LICENSE")
set(CPACK_NSIS_ENABLE_UNINSTALL_BEFORE_INSTALL ON)
set(CPACK_NSIS_URL_INFO_ABOUT "https://ecl.common-lisp.dev/")
set(CPACK_NSIS_MENU_LINKS "Embeddable Common-Lisp" "bin/ecl.exe" "About ECL" "https://ecl.common-lisp.dev/" "ECL Manual"  "https://ecl.common-lisp.dev/static/files/manual/current-manual/" "ECL License" "https://gitlab.com/embeddable-common-lisp/ecl/-/raw/develop/LICENSE")
set(CPACK_PACKAGE_FILE_NAME "ecl-${PROJECT_VERSION}-${INSTALLERSUFFIX}")
set(CPACK_NSIS_COMPRESSOR "/SOLID lzma")

include(CPack)
