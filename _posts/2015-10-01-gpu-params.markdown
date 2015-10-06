---
layout: post
title:  "GPU parameters are no longer ignored if compiling without GPUs"
author: Pete Schultz
categories: parameters
---
If PetaVision was compiled without GPU acceleration, it is now an error to
set receiveGpu (in connections) or updateGpu (in layers) to true.
Previously, the values in the params file would be read and discarded; and
in the files that got saved in checkpoints and the output path, those
parameters would be set to false.

The rationale for this change is to help catch situations where the user meant
to use GPU acceleration, but failed to turn PV_USE_CUDA on.  By throwing an
error immediately, we avoid running for a long time before realizing that
the GPUs are not being used.
