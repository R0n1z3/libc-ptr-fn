cmake_minimum_required(VERSION 3.20)

project(libc-ptr-fn
    VERSION 0.1.0
    DESCRIPTION "Function-pointer access to libc symbols"
    LANGUAGES CXX)

set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)
set(CMAKE_EXPORT_COMPILE_COMMANDS ON)

if(NOT CMAKE_BUILD_TYPE AND NOT CMAKE_CONFIGURATION_TYPES)
    set(CMAKE_BUILD_TYPE Debug CACHE STRING "Build type" FORCE)
endif()

option(LIBCPTR_WARNINGS_AS_ERRORS "Treat compiler warnings as errors" OFF)

add_executable(libc-ptr-fn src/main.cpp)

target_include_directories(libc-ptr-fn PRIVATE
    ${CMAKE_CURRENT_SOURCE_DIR}/include)

target_compile_options(libc-ptr-fn PRIVATE
    -Wall -Wextra -Wpedantic -Wshadow -Wconversion -Wnon-virtual-dtor)
if(LIBCPTR_WARNINGS_AS_ERRORS)
    target_compile_options(libc-ptr-fn PRIVATE -Werror)
endif()

# Runtime symbol lookup (dlopen/dlsym) lives in libdl on older glibc and is
# folded into libc on newer ones; CMAKE_DL_LIBS resolves to whichever applies.
target_link_libraries(libc-ptr-fn PRIVATE ${CMAKE_DL_LIBS})

# Export dynamic symbols so the executable's own symbols are visible to dlsym.
set_target_properties(libc-ptr-fn PROPERTIES ENABLE_EXPORTS ON)
