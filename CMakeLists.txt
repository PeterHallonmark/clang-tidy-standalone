cmake_minimum_required(VERSION 3.13.4)
project(clang-tidy-standalone)

find_package(LLVM 20.1.8 EXACT REQUIRED CONFIG)
find_package(Clang 20.1.8 EXACT REQUIRED CONFIG)

message(STATUS "Using LLVM ${LLVM_PACKAGE_VERSION}")
message(STATUS "Using Clang ${CLANG_PACKAGE_VERSION}")

# Make all LLVM/Clang headers available
include_directories(
    ${LLVM_INCLUDE_DIRS}
    ${CLANG_INCLUDE_DIRS}
    ${CMAKE_BINARY_DIR}
)

# Generate clang-tidy config header (required by core)
configure_file(
    clang-tidy-config.h.cmake
    ${CMAKE_BINARY_DIR}/clang-tidy-config.h
)

if(NOT CMAKE_BUILD_TYPE)
    set(CMAKE_BUILD_TYPE Debug CACHE STRING "Build type" FORCE)
endif()

add_executable(clang-tidy-standalone
    tool/ClangTidyMain.cpp
    tool/ClangTidyToolMain.cpp
)

# Link only the core clang-tidy libraries
target_link_libraries(clang-tidy-standalone
    PRIVATE
        clangTidy
        clangAST
        clangASTMatchers
        clangBasic
        clangTooling
)
