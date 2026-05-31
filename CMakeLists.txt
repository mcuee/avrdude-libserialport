#
# CMakeLists.txt
# Copyright (C) 2021 Marius Greuel
#

cmake_minimum_required(VERSION 3.12)
project(libserialport)

# Global internal building definitions
add_compile_definitions(
    _CRT_SECURE_NO_WARNINGS=1
    SP_PRIV=
    LIBSERIALPORT_MSBUILD=1
)

add_compile_definitions(LOG_APPNAME="avrdude")

add_library(libserialport STATIC
    src/serialport.c
    src/timing.c
    src/windows.c
)

target_include_directories(libserialport
    PUBLIC
    include
)

# CRITICAL: This strips __declspec(dllimport) from downstream targets linking to this static lib
target_compile_definitions(libserialport PUBLIC SP_API=)

# MSVC specific compiler options for a clean build
if(MSVC)
    target_compile_options(libserialport PRIVATE /wd4996) # warning C4996: 'xxx': was declared deprecated
    target_compile_options(libserialport PRIVATE /wd5105) # warning C5105: macro expansion producing 'xxx' has undefined behavior
endif()
