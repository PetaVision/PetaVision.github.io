---
layout: post
title:  "Zero-padding checkpoint directory names"
author: Pete Schultz
categories: checkpoints
---
Previously, if the checkpoint interval was 40 steps, the checkpoint
directories would be named  
Checkpoint40  
Checkpoint80  
Checkpoint120  
etc.

It is often preferable to have all the step numbers written with the same
width, padding with zeros as necessary:  
Checkpoint040  
Checkpoint080  
Checkpoint120  
etc.

Accordingly, I added a parameter to HyPerCol, checkpointIndexWidth, to
control this behavior.  For example, setting checkpointIndexWidth to 3
will generate the behavior above.  For time step 1000, the directory will
still be Checkpoint1000: the step number is never truncated.

Setting checkpointIndexWidth=0 or 1 recreates the old behavior: step numbers
are never truncated, and padding zeroes are never added.

If you set checkpointIndexWidth to a negative number, checkpointWrite
will compute the total number of steps from the startTime, stopTime, and dt
parameters, and use the number of digits in the number of steps when creating
the directory name.

The default value of checkpointIndexWidth is -1 (to automatically choose
the width).
