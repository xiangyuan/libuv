# Copyright (c) 2013 Plenluno All rights reserved.

cmake_minimum_required(VERSION 2.8)

project(uv)

# variables --------------------------------------------------------------------

## uv-include
set(uv-include
    include
    include/uv-private
    src
)

## uv-src
set(uv-src
    src/fs-poll.c
    src/inet.c
    src/uv-common.c
)

if(UNIX)
    set(uv-src
        ${uv-src}
        src/unix/async.c
        src/unix/core.c
        src/unix/dl.c
        src/unix/eio/eio.c
        src/unix/error.c
        src/unix/ev/ev.c
        src/unix/fs.c
        src/unix/loop.c
        src/unix/loop-watcher.c
        src/unix/pipe.c
        src/unix/poll.c
        src/unix/process.c
        src/unix/signal.c
        src/unix/stream.c
        src/unix/tcp.c
        src/unix/thread.c
        src/unix/timer.c
        src/unix/tty.c
        src/unix/udp.c
        src/unix/uv-eio.c
    )
else(UNIX)
    set(uv-src
        ${uv-src}
        src/win/async.c
        src/win/core.c
        src/win/dl.c
        src/win/error.c
        src/win/fs.c
        src/win/fs-event.c
        src/win/getaddrinfo.c
        src/win/handle.c
        src/win/loop-watcher.c
        src/win/pipe.c
        src/win/thread.c
        src/win/poll.c
        src/win/process.c
        src/win/process-stdio.c
        src/win/req.c
        src/win/signal.c
        src/win/stream.c
        src/win/tcp.c
        src/win/tty.c
        src/win/threadpool.c
        src/win/timer.c
        src/win/udp.c
        src/win/util.c
        src/win/winapi.c
        src/win/winsock.c
    )
endif(UNIX)

if(APPLE)
    set(uv-src
        ${uv-src}
        src/unix/darwin.c
        src/unix/fsevents.c
        src/unix/kqueue.c
    )
endif(APPLE)

if(${CMAKE_SYSTEM} MATCHES "Linux")
    set(uv-src
        ${uv-src}
        src/unix/linux/inotify.c
        src/unix/linux/linux-core.c
        src/unix/linux/syscalls.c
    )
endif(${CMAKE_SYSTEM} MATCHES "Linux")

## uv-deps
if(UNIX)
    set(uv-deps
        m
    )
else(UNIX)
    set(uv-deps
        iphlpapi
        psapi
        ws2_32
    )
endif(UNIX)

if(${CMAKE_SYSTEM} MATCHES "Linux")
    set(uv-deps
        ${uv-deps}
        dl
        rt
    )
endif(${CMAKE_SYSTEM} MATCHES "Linux")

## uv-cflags
if(UNIX)
    set(uv-cflags
        --std=gnu89
        -pedantic
        -Wall
        -Wextra
        -Wno-unused-parameter
        -D_FILE_OFFSET_BITS=64
        -D_GNU_SOURCE
        -D_LARGEFILE_SOURCE
        -DEIO_STACKSIZE=262144
    )
else(UNIX)
    set(uv-cflags
        /D_GNU_SOURCE
        /D_WIN32_WINNT=0x0600
        /DEIO_STACKSIZE=262144
    )
endif(UNIX)

if(APPLE)
    set(uv-cflags
        ${uv-cflags}
        -D_DARWIN_USE_64_BIT_INODE=1
        -DEV_CONFIG_H='"config_darwin.h"'
        -DEIO_CONFIG_H='"config_darwin.h"'
    )
endif(APPLE)

if(${CMAKE_SYSTEM} MATCHES "Linux")
    set(uv-cflags
        ${uv-cflags}
        -DEV_CONFIG_H='"config_linux.h"'
        -DEIO_CONFIG_H='"config_linux.h"'
    )
endif(${CMAKE_SYSTEM} MATCHES "Linux")

string(REPLACE ";" " " uv-cflags "${uv-cflags}")

# build ------------------------------------------------------------------------

include_directories(
    ${uv-include}
)

add_library(uv STATIC
    ${uv-src}
)

target_link_libraries(uv
    ${uv-deps}
)

set_target_properties(uv PROPERTIES
    COMPILE_FLAGS "${uv-cflags}"
)
