---
layout: post
title:  "Batching Across MPI"
author: Sheng Lundquist
categories: jekyll update
---

Hi all,

I've just implemented being able to split batches into different MPI processes. Here's everything you need to know about it.

In the simplest case, to run a parameter file with 20 batches, split into 4 different MPI processes (5 batches per process), simply run

{% highlight bash %}
mpirun -np 5 Release/myExecutable -p input/myParams.params -batchwidth 5
{% endhighlight %}

By default, this will put your output path wherever this command was ran as *output_batchwidth_00*, where the suffix numbers specify the MPI batch rank. This is because each MPI batch rank will write to it's own output path. How do you specify different output paths per each MPI batch rank? To do so, simply use the new batch sweep method (modeled after parameter sweeps) as such:

{% highlight c %}
HyPerCol "column" = {
   ...
   outputPath = "placeholder"; //This output path value doesn't matter, as batch
                               //sweep method will overwrite this value
}

BatchSweep "column":outputPath = {
    "my/output/path/for/batch/rank0/";
    "my/output/path/for/batch/rank1/";
    "my/output/path/for/batch/rank2/";
    "my/output/path/for/batch/rank3/";
    "my/output/path/for/batch/rank4/";
};
{% endhighlight %}

Note that this can be done with any parameter within reason (as in if you're batch sweeping the nbatch field in HyPerCol, you deserve segfaults). Note that Movie and MoviePvp's batchMethod of *byImage* and *byMovie* automatically determines the global batch index, so there is no need to BatchSweep these methods. Currently, you cannot do both *BatchSweeps* and *ParameterSweeps* at the same time. For a detailed parameter file showcasing this feature, please see *PVSystemTests/BatchSweepTest/*.

There are a few other updates involved with this update. Namely, there exists a new *PV_Init* object that takes care of calling *MPI_Init* and *MPI_Finalize*. If your code is simply calling *buildandrun*, this process is automatically done for you. However, if your main function is a little more complicated (such as running 2 parameter files one after another), you will need to call the *PV_Init* constructor. Please see *PVSystemTest/CheckpointSystemTest/src/main.cpp* for an example of how to do this.

Finally, one additional change I added is the ability to specify more process than you actually need. For example, consider this run.

{% highlight bash %}
mpirun -np 10 Release/myExecutable -p input/myParams.params -rows 2 -columns 2 -batchwidth 2
{% endhighlight %}

As you can see, the run specifies that it needs 8 MPI processes for the run, but there exists 10 MPI processes launched through mpirun. While the old code would have thrown an error, the new code will simply make the extra processes bypass the *buildandrun* mechanism.

Please let me know if you have any trouble with the new MPI batching.

Happy scaling,
Sheng



