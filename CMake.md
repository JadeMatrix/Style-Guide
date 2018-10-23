# CMake Style Guide

[ToC]


## Usage

* use modern, target-based CMake files
    * create `INTERFACE` library targets for header-only libraries
    * wrap any dependencies that do not export targets (or do not have CMake scripts) as packages with component targets
        * if debug versions (or other configurations) are available, set them as the `IMPORTED_LOCATION_<CONFIG>` property on the imported target
    * avoid global settings where possible
        * never use `INCLUDE_DIRECTORIES()` or `LINK_LIBRARIES()` (these are deprecated as of CMake 3.0)
* export targets when configuring libraries/SDKs
* always mark target dependencies with visibility (e.g. `PUBLIC`/`PRIVATE`/`INTERFACE`)
    * mark dependencies of executable targets as `PRIVATE` if possible
* perform any source generation at build-time (rather than configure-time) using `ADD_CUSTOM_COMMAND` and `ADD_CUSTOM_TARGET`


## Formatting

* prefer all-caps symbols where possible (function names/calls, variable names, etc.)
    * not possible for `CMAKE_BUILD_TYPE` values, which are typically either `Release` or `Debug`
    * variables containing package or component names may have the name left as-is for ease of use — e.g. `fooBar_VERSION` for a package named `fooBar`
* place parentheses for function calls (and similar syntax) abutted to their symbol, with full space padding; for example `IF( "${VAR}" STREQUAL "foo" )`
* indent 4 spaces
* lines max 80 characters
* double newline between major sections in same file
* use one CMakeLists.txt file per subdirectory where practical
* always enclose variable expansions with quotes unless they are intended to be expanded as a list


## Examples

```cmake
ADD_EXECUTABLE( units_test "test.cpp" )
TARGET_LINK_LIBRARIES( units_test PRIVATE JadeMatrix::units::units )
```