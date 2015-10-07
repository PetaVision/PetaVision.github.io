---
layout: post
title:  "Handling Command Line Arguments"
author: Pete Schultz
categories: parameters
---
I just added changes that centralize the processing of the command line
arguments, and simplify changing the arguments internally from what was passed
on the command line.

If your program simply calls buildandrun, no changes should be necessary.

However, if you are using other interfaces to PetaVision than buildandrun,
please read on.

The principal difference is in a new class PV_Arguments, that stores
and manages the information from the command line in a form that is
easy to retrieve and modify.  PV_Init class has a PV_Arguments member
variable called arguments, which is retrieved with the getArguments method.

For example, to get the params file from a PV_Init object called initObj, call
    `char const * paramsFile = initObj->getArguments()->getParamsFile();`
If you want to change the paramsFile (as several system tests that run
more than one HyPerCol do),
    `initObj->getArguments()->setParamsFile(newParamsFile);`
The command line arguments can be changed in a similar fashion.  
You can reset to the original argc and argv with
    `initObj->getArguments()->resetState();`
or reset to completely new argc and argv with
    `initObj->getArguments()->resetState(new_argc, new_argv, allowUnrecognizedArguments);`

The part of HyPerCol::initialize that throws an error if there are any
unrecognized parameters has been moved into the PV_Arguments initialization.
The PV_Init and PV_Arguments constructors take as arguments, in addition
to argc and argv, a boolean argument allowUnrecognizedArguments.  The above
error checking only takes place if that flag is set to true.
buildandrun() sets that flag to false, which matches the earlier behavior.

Since all the argc and argv information needed to do a run is now in
the PV_Init member variable returned by getArguments(), several functions
have simpler interfaces.
* The HyPerCol constructor no longer takes argc and argv, only name and initObj.
* The Communicator constructor only takes a PV_Arguments input, instead of argc and argv.
* rebuildandrun, buildandrun1paramset, and build no longer take argc and argv, since they already take initObj.
