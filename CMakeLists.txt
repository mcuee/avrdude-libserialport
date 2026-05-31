#
# CMakeLists.txt
# Copyright (C) 2021 Marius Greuel
# Adapted by Xiaofan Chen for libserialport MSVC build

cmake_minimum_required(VERSION 3.12)
project(libserialport)

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

# Crucial: Forces any target linking to 'libserialport' to compile with this macro
target_compile_definitions(libserialport PUBLIC LIBSERIALPORT_STATIC)

if(MSVC)
    target_compile_options(libserialport PRIVATE /wd4996) 
    target_compile_options(libserialport PRIVATE /wd5105) 
endif()
