#
# CMakeLists.txt
# Copyright (C) 2021 Marius Greuel
# Adapted by Xiaofan Chen for libserialport MSVC build
#

cmake_minimum_required(VERSION 3.12)
project(libserialport)

# Global internal building definitions
# We completely remove SP_API= and LIBSERIALPORT_MSBUILD here
add_compile_definitions(
    _CRT_SECURE_NO_WARNINGS=1
    SP_PRIV=
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

# This is the secret sauce: 
# It sets SP_API to blank for libserialport when compiling itself,
# AND tells any downstream target (like swig_avrdude) to also treat it as blank!
target_compile_definitions(libserialport INTERFACE SP_API=)
target_compile_definitions(libserialport PRIVATE SP_API=)

# MSVC specific compiler options for a clean build
if(MSVC)
    target_compile_options(libserialport PRIVATE /wd4996) # warning C4996: 'xxx': was declared deprecated
    target_compile_options(libserialport PRIVATE /wd5105) # warning C5105: macro expansion producing 'xxx' has undefined behavior
endif()
