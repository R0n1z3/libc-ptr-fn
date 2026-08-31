# libc-ptr-fn

Exploring function-pointer access to libc symbols in C++20.

## Requirements

- CMake 3.20+
- A C++20 compiler (built with g++ 15.2)
- glibc with `dlfcn.h` (linked via `CMAKE_DL_LIBS`)

## Build

```bash
cmake -S . -B build
cmake --build build -j
```

## Run

```bash
./build/libc-ptr-fn
```

## Layout

| Path | Contents |
| --- | --- |
| `src/` | Sources; entry point is `src/main.cpp` |
| `include/` | Public headers |

## Options

| Option | Default | Effect |
| --- | --- | --- |
| `CMAKE_BUILD_TYPE` | `Debug` | Standard CMake build type |
| `LIBCPTR_WARNINGS_AS_ERRORS` | `OFF` | Add `-Werror` to the warning set |

## License

MIT — see [LICENSE](LICENSE).
