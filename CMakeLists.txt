cmake_minimum_required(VERSION 3.16)
project(main)

set(CMAKE_VERBOSE_MAKEFILE)
SET(CMAKE_CXX_COMPILER clang++)
set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)
set(PREBUILT_MODULE_PATH ${CMAKE_BINARY_DIR}/modules)

function(add_module name)
    file(MAKE_DIRECTORY ${PREBUILT_MODULE_PATH})
    add_custom_target(${name}.pcm
            COMMAND
                ${CMAKE_CXX_COMPILER}
                -std=c++20
                -stdlib=libc++
                -fimplicit-modules
                -fimplicit-module-maps
                -c
                ${CMAKE_CURRENT_SOURCE_DIR}/${ARGN}
                -Xclang -emit-module-interface
                -o ${PREBUILT_MODULE_PATH}/${name}.pcm

            )
endfunction()

add_compile_options(-std=c++20)
add_compile_options(-stdlib=libc++)
add_compile_options(-fimplicit-modules)
add_compile_options(-fimplicit-module-maps)
add_compile_options(-fprebuilt-module-path=${PREBUILT_MODULE_PATH})

add_module(helloworld helloworld.cpp)
add_executable(helloworld-driver
        main.cpp
        helloworld.cpp
        )
add_dependencies(helloworld-driver helloworld.pcm)
