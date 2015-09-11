---
layout: post
title:  "Building with OpenMP under OS X"
author: Pete Schultz
categories: openmp
---
The most recent version of the [Clang](http://clang.llvm.org/) compilers
supports OpenMP; however, as of this writing the version of Clang supplied by
Apple's Xcode does not.  I updated the
[install_osx page](http://petavision.github.io/doxygen/md_docs_doxygen_src_install_osx.html)
with instructions to install the OpenMP-compatible version of Clang; and
updated CMakeLists.txt files throughout the OpenPV repository.

The instructions guide you through installing the most recent version of Clang
and the Intel OpenMP Runtime library.

Then in CMake, set both the `CLANG_OMP` and `PV_USE_OPENMP` variables to ON.

If you are not using MPI, you will also need to set `CMAKE_C_COMPILER`
and `CMAKE_CXX_COMPILER` to the paths to `clang` and `clang++` that you
installed.  To view or change the compiler variables in ccmake, you will need
to press `t` to toggle the advanced mode.  (If you are using MPI, the
compilers used will be mpicc and mpicxx, so this step is not necessary.)
