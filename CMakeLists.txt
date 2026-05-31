#
# CMakeLists.txt
# Copyright (C) 2021 Marius Greuel
# Adapted by Xiaofan Chen for libserialport MSVC build
#

cmake_minimum_required(VERSION 3.12)
project(libserialport C)

# Use target-scoped definitions rather than directory-wide add_compile_definitions
add_library(libserialport STATIC
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

# MSVC specific compiler warning suppression
if(MSVC)
    target_compile_options(libserialport PRIVATE /wd4996 /wd5105)
endif()
