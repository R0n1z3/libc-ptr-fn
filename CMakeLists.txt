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

add_executable(retlibc src/main.cpp)

target_include_directories(retlibc PRIVATE
    ${CMAKE_CURRENT_SOURCE_DIR}/include)

target_compile_options(retlibc PRIVATE
    -Wall -Wextra -Wpedantic -Wshadow -Wconversion -Wnon-virtual-dtor
    -fno-stack-protector -m32)
target_link_options(retlibc PRIVATE -m32)   # -m32 is needed at link time too
if(LIBCPTR_WARNINGS_AS_ERRORS)
    target_compile_options(retlibc PRIVATE -Werror)
endif()

# Runtime symbol lookup (dlopen/dlsym) lives in libdl on older glibc and is
# folded into libc on newer ones; CMAKE_DL_LIBS resolves to whichever applies.
target_link_libraries(retlibc PRIVATE ${CMAKE_DL_LIBS})

# Export dynamic symbols so the executable's own symbols are visible to dlsym.
set_target_properties(retlibc PROPERTIES ENABLE_EXPORTS ON)

# Attacker-side tool: builds the "badfile" payload for the return-to-libc lab.
# Deliberately plain -- no -fno-stack-protector, no dl/exports; those belong to
# the vulnerable target above, not to the exploit that attacks it.
add_executable(exploit src/exploit.cpp)
target_compile_options(exploit PRIVATE -Wall -Wextra)
