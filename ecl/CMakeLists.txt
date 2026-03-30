# SPDX-License-Identifier: GPL-2.0-or-later

set(ECL_MD5 "92c91aabe58bf15d360493904ed86850")
set(ECL_URL "https://ecl.common-lisp.dev/static/files/release/ecl-${PROJECT_VERSION}.tgz")

ExternalProject_Add(ecl-native
  DEPENDS gmplib
  BUILD_IN_SOURCE 1
  URL "${ECL_URL}"
  DOWNLOAD_DIR ${CMAKE_SOURCE_DIR}/downloads
  DOWNLOAD_EXTRACT_TIMESTAMP true
  URL_MD5 ${ECL_MD5}
  CONFIGURE_COMMAND ./configure --prefix=${CMAKE_BINARY_DIR}/ecl-native-root --enable-gmp=included
  BUILD_COMMAND make
  INSTALL_COMMAND make install
)

ExternalProject_Add(ecl-bytecode
  DEPENDS gmplib ecl-native
  BUILD_IN_SOURCE 1
  URL "${ECL_URL}"
  DOWNLOAD_DIR ${CMAKE_SOURCE_DIR}/downloads
  DOWNLOAD_EXTRACT_TIMESTAMP true
  URL_MD5 ${ECL_MD5}
  PATCH_COMMAND  rm -rf "${CMAKE_CURRENT_BINARY_DIR}/ecl-bytecode-prefix/src/ecl-bytecode/src/gmp"
        COMMAND  cp -a "${CMAKE_BINARY_DIR}/gmplib/gmplib-prefix/src/gmplib" "${CMAKE_CURRENT_BINARY_DIR}/ecl-bytecode-prefix/src/ecl-bytecode/src/gmp"
  CONFIGURE_COMMAND ECL_TO_RUN=${CMAKE_BINARY_DIR}/ecl-native-root/bin/ecl ./configure --host=x86_64-w64-mingw32 --prefix=${CMAKE_BINARY_DIR}/ecl-cross-root --with-cross-config=${CMAKE_BINARY_DIR}/ecl/ecl-bytecode-prefix/src/ecl-bytecode/src/util/x86_64-w64-mingw32.cross_config --enable-gmp=included --with-cmp=no --with-bytecmp=yes "${ASDF_OPTION}"
  BUILD_COMMAND make
  INSTALL_COMMAND make install
          COMMAND ${CMAKE_COMMAND} -E copy "${MINGW_LIBGCC}" "${CMAKE_BINARY_DIR}/ecl-cross-root"
          COMMAND ${CMAKE_COMMAND} -E copy "${MINGW_LIBWINPTHREAD}" "${CMAKE_BINARY_DIR}/ecl-cross-root"
)
install(DIRECTORY ${CMAKE_BINARY_DIR}/ecl-cross-root/ DESTINATION "ecl-${ECL_VERSION}")
