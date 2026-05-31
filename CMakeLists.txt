#
# CMakeLists.txt
# Copyright (C) 2021 Marius Greuel
# Adapted by Xiaofan Chen for libserialport MSVC build
#

cmake_minimum_required(VERSION 3.12)
project(libserialport C)

add_library(libserialport
    src/serialport.c
    src/timing.c
    src/windows.c
)

target_include_directories(libserialport PUBLIC include)

# Internal private definitions needed strictly to compile the library source files
target_compile_definitions(libserialport PRIVATE 
    _CRT_SECURE_NO_WARNINGS=1
    SP_PRIV=
    LOG_APPNAME="avrdude"
)

# Windows specific target handling (fixes standalone vs sub-project builds)
if(WIN32)
    # Automatically links setupapi and cfgmgr32 dependencies natively
    target_link_libraries(libserialport PRIVATE setupapi cfgmgr32)

    if(BUILD_SHARED_LIBS)
        # Shared Build: exports symbols during build, drops them for consumers
        target_compile_definitions(libserialport PRIVATE LIBSERIALPORT_MSBUILD=1)
    else()
        # Static Build: Forces the header to strip __declspec(dllimport) everywhere!
        # This fixes BOTH your standalone GitHub Action workflow AND the parent avrdude SWIG build.
        target_compile_definitions(libserialport PUBLIC LIBSERIALPORT_MSBUILD=1 SP_API=)
    endif()
endif()

# MSVC specific compiler warning suppression
if(MSVC)
    target_compile_options(libserialport PRIVATE /wd4996 /wd5105)
endif()
