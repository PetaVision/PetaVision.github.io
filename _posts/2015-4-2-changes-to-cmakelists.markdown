---
layout: post
title:  "Changes to CMakeLists files"
date:   2015-4-2 23:01:55
author: Pete Schultz
categories: jekyll update
---

Hello everyone.  I just committed a rather large update that touches every CMakeLists.txt file in trunk or PVSystemTests.  Until that update, cmake defined the variables *OPEN_MP_THREADS*, *OPEN_CL_GPU*, *CUDA_GPU*, and *CUDNN*; and according to their values, set compiler options to define the preprocessor directives *PV_USE_OPENMP_THREADS*, *PV_OPENCL*, *PV_CUDA*, and *PV_CUDNN*.

What the new code does is define CMake variables whose names are the same as the corresponding preprocessor directives, and then uses those values to generate the file src/include/cMakeHeader.h, which contains #define or #undef statements.  The file pv\_arch.h #includes the cMakeHeader.h file.  The advantage is that these preprocessor directives do not have to be managed by makefiles generated by cmake; instead one only has to ensure that the source files include cMakeHeader.h at some level.  Since it’s already included by pv\_arch.h, and pretty much all of PetaVision includes pv\_arch.h as it is, having cMakeHeader.h included shouldn’t be a problem.

However, you’ll have to rerun cmake in order to generate cMakeHeader.h.  After you update and before you compile, you’ll need to do the following:

In any sandbox CMakeLists.txt file you’re using, change the variables *OPEN_MP_THREADS*, *OPEN_CL_GPU*, *CUDA_GPU*, *CUDNN* (if they appear) to *PV_USE_OPENMP_THREADS*, *PV_OPENCL*, *PV_CUDA*, and *PV_CUDNN*, respectively.  Then anywhere that there is an option *-DPV_USE_OPENMP_THREADS*, *-DPV_OPENCL*, etc., remove it.

If you use a workspace-wide CMakeLists.txt file copied from trunk/docs/cmake/CMakeLists.txt, you can either
(1) Note the local changes you made to the workspace-wide CMakeListst.txt file; copy the new trunk/docs/cmake/CMakeLists.txt to the workspace-wide CMakeLists.txt file, and then redo the changes that you had in the old version; or
(2) Change the workspace-wide CMakeLists.txt file the same way you changed the sandbox, above.

Run cmake or ccmake (cmake28/ccmake28 on the NMC clusters).  There is code in the trunk’s and PVSystemTest’s CMakeLists.txt that should detect an old cache variable, assign the old variable’s value to the new variable, and delete the old value.  However, make sure that the new PV\_\* variables are receiving the correct values.  If you’re using ccmake, it may take a couple of iterations of configuration before you have the chance to generate.  After cmake/ccmake completes, look at trunk/src/include/cMakeHeader.h to make sure it has the correct values.

Finally, run the make command.

Please let me know if you run into any difficulties with this.

