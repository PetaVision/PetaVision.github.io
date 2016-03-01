---
layout: post
title:  "CMake Overhaul"
author: Jeff Bowles
categories: cmake
---

Greetings,

I recently pushed up a commit that overhauls the Petavision CMake build system. The initial motivation is to make things easier to port to Trinity.

The biggest change: adding new system tests. The boilerplate was removed and replaced with a single function call, pv_add_test()

This is what your CMakeLists.txt will be for most system tests. For more examples, check out cmake/PVAddTest.cmake which shows how to specify alternate params files, add special flags, run MPI-only tests, change the min and max number of MPI copies, and so on.

Some other changes:

* The compiler is automatically detected
* If CUDA and cuDNN are installed, Cuda is enabled
* If a compiler with OpenMP support is detected, OpenMP support enabled by default
* If MPI is detected, MPI support is enabled by default
* If you’re using clang, you can turn on address sanitization.

Any of these settings can be overridden. The goal is to get new folks up and running a bit faster.

File-globbing. This isn’t a good practice and it’s been removed from pv-core. If you add or remove files, you’ll need to add or remove them in that directory’s CMakeLists.txt file. PVSystemTests still use file globbing for now.

For those who don’t want to open up PVAddTest.cmake, here’s the header:

```
 pv_add_test([ONLY_MPI | NO_MPI] [MAX_MPI_COPIES] [FLAGS] [BASE_NAME])

 All arguments are optional

 Add a test case for the OpenPV library. This creates the binary and adds
 test tests. Multiple test cases are added by default.
 --------------------------------------------------------------------------------
 Example CMakeLists.txt files for a PVSystemTest:

 Most files will have a single line:

   pv_add_test()

   This will add a test with a base name based on the test's directory name. The params file
   will be named input/directory_name.params

   This test will use 1,2, and 4 MPI copies

 No params files:

   pv_add_test(NO_PARAMS)

 Alternate param files:

   pv_add_test(PARAMS test_kernel test_kernel_normalizepost test_kernel_normalizepost_shrunken)

 No params files, no MPI:

   pv_add_test(NO_PARAMS NO_MPI)

 Append special flags to the command line

   pv_add_test(FLAGS "-c checkpoints/Checkpoint06 --testall")

 Specify the number of MPI copies, only test MPI, and special flags

   pv_add_test(MIN_MPI_COPIES 4 MPI_ONLY FLAGS "-batchwidth 4")

 --------------------------------------------------------------------------------
 Options:

 MPI_ONLY and NO_MPI are mutually exclusive. Both arguments are options.
 By default, one non-MPI test case is created, and two MPI test cases are created, for
 2 and 4 copies

 MIN_MPI_COPIES - minimum number of MPI copies. Default is 2

 MAX_MPI_COPIES - maximum number of MPI copies. Test cases are incremented by powers of two,
  First case is 2, next is 4, and so on, until MAX_MPI_COPIES is reached. Default is 4

 FLAGS - by default, the flags are "-t ${PV_SYSTEM_TEST_THREADS}". Setting FLAGS appends
  to this

 BASE_NAME - by default, this is the last component of the path ${CMAKE_CURRENT_SOURCE_DIR}.
  Set BASE_NAME to override

 PARAMS - by default, this is set to BASE_NAME and a params file of name input/BASE_NAME.params
   is searched for. If multiple params files are specified, then a separate set of tests
   will be run for each of the params files

 NO_PARAMS - if set, then no params files will be used for this test. Setting PARAMS and
   NO_PARAMS will result in an error
```


