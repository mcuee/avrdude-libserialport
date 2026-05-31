#
# CMakeLists.txt
# Copyright (C) 2021 Marius Greuel
# Adapted by Xiaofan Chen for libserialport MSVC build
#

cmake_minimum_required(VERSION 3.12)
project(libserialport C)

add_library(libserialport STATIC
    src/serialport.c
    src/timing.c
    src/windows.c
)

target_include_directories(libserialport PUBLIC include)

# Internal private definitions needed strictly to compile the library source files.
# We removed _CRT_SECURE_NO_WARNINGS here because the library header already defines it internally.
target_compile_definitions(libserialport PRIVATE 
    SP_PRIV=
    LOG_APPNAME="avrdude"
)

if(WIN32)
    # Link system libraries required by the Windows backend natively
    target_link_libraries(libserialport PRIVATE setupapi cfgmgr32)

    if(BUILD_SHARED_LIBS)
        target_compile_definitions(libserialport PRIVATE LIBSERIALPORT_MSBUILD=1)
    else()
        # Static Build target definitions
        target_compile_definitions(libserialport PUBLIC LIBSERIALPORT_MSBUILD=1 SP_API=)
    endif()
endif()

# MSVC tuning - Using PUBLIC ensures downstream targets (like avrdude) 
# are also kept clean and warning-free!
if(MSVC)
    target_compile_options(libserialport PUBLIC 
        /wd4005 # Suppress warning C4005: macro redefinition
    )
    target_compile_options(libserialport PRIVATE
        /wd4996 # Suppress warning C4996: deprecated POSIX names
        /wd5105 # Suppress warning C5105: macro expansion undefined behavior
    )
endif()
