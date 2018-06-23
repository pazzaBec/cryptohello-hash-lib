cmake_minimum_required(VERSION 2.8)
set(CMAKE_MODULE_PATH ${CMAKE_CURRENT_SOURCE_DIR} ${CMAKE_MODULE_PATH})
#include(Cryptohello)
message(STATUS "Target architectures: ${CMAKE_TARGET_ARCHITECTURES}")

project(cryptohello_hash C ASM)

#add for macos
if(APPLE)
add_definitions("-DSYS_OS_MAC")
ENDIF()

# Add a CMake parameter for choosing a desired Python version
set(CRYPTOHELLO_PYTHON_VERSION "" CACHE STRING "Python version to use for compiling cryptohello_hash library")

# Set a default build configuration if none is specified. 'Release' produces the fastest binaries
if(NOT CMAKE_BUILD_TYPE AND NOT CMAKE_CONFIGURATION_TYPES)
  message(STATUS "Setting build type to 'Release' as none was specified.")
  set(CMAKE_BUILD_TYPE Release CACHE STRING "Choose the type of build." FORCE)
  set_property(CACHE CMAKE_BUILD_TYPE PROPERTY STRINGS "Debug" "Release"
    "MinSizeRel" "RelWithDebInfo")
endif()
string(TOUPPER "${CMAKE_BUILD_TYPE}" U_CMAKE_BUILD_TYPE)

# Try to autodetect Python (can be overridden manually if needed)
set(Python_ADDITIONAL_VERSIONS 3.4 3.5 3.6)
find_package(PythonLibs ${CRYPTOHELLO_PYTHON_VERSION} REQUIRED)

# Include path for Python header files
include_directories(${PYTHON_INCLUDE_DIR})
# include all subdirs at source directory
#include_directories(${PROJECT_SOURCE_DIR}/.)

#find OpenSSL and include
find_package(OpenSSL)
if(NOT OPENSSL_FOUND)
  message(FATAL_ERROR "OpenSSL not found!")
endif()
include_directories(${OPENSSL_INCLUDE_DIR})
set(IMPORTLIB ${OPENSSL_CRYPTO_LIBRARY} ${OPENSSL_SSL_LIBRARY})

set(CRYPTO_HEADERS
    aes128.h
    blake2_impl.h
    blake2s.h
    blake2s256.h
    camellia128.h
    common.h
    crc32.h
    des.h
    gost.h
    haval5_256.h
    hmac_md5.h
    jtr_crc32.h
    jtr_gost.h
    jtr_sph_haval.h
    jtr_sph_skein.h
    jtr_sph_types.h  
    keccak1600.h
    my_rand48_r.h   
    my_time.h
    oneWayFunction.h
    PoW.h
    rc4.h
    ripemd160.h
    sha1.h
    sha3_256.h
    sha256.h
    sha512.h
    skein512_256.h
    whirlpool.h
)

set(CRYPTO_SOURCES
    aes128.c
    blake2s.c
    blake2s256.c
    camellia128.c
    common.c
    crc32.c
    des.c
    gost.c
    keccak1600.c
    haval5_256.c
    hmac_md5.c
    jtr_crc32.c
    jtr_gost.c
    jtr_haval.c
    jtr_skein.c
    my_time.c
    oneWayFunction.c
    PoW.c
    rc4.c
    ripemd160.c
    sha1.c
    sha3_256.c
    sha256.c
    sha512.c
    skein512_256.c
    whirlpool.c
)

include_directories(${PROJECT_SOURCE_DIR}/compat)

set(COMPAT_HEADERS
    compat/cpuminer-config.h
    compat/inttypes.h
    compat/stdbool.h
    compat/unistd.h
    compat/winansi.h
)

# Create the binding library
add_library(cryptohello_hash SHARED
   #${PYBIND11_HEADERS}
   ${CRYPTO_HEADERS}
   ${CRYPTO_SOURCES}
   ${COMPAT_HEADERS}
   compat.h
   miner.h
   cryptohello_hash.h
   cryptohello_hash.c
   sysinfos.c
   cryptohellohashmodule.c
  # ... extra files go here ...
)