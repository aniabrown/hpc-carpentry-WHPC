---
title: "Using resources effectively"
teaching: 15
exercises: 10
questions:
- "How do we monitor our jobs?"
- "How can I get my jobs scheduled more easily?" 
objectives:
- "Understand how to look up job statistics and profile code."
- "Understand job size implications."
keypoints:
- "The smaller your job, the faster it will schedule."
---

We now know virtually everything we need to know about getting stuff on a cluster. We can log on,
submit different types of jobs, use preinstalled software, and install and use software of our own.
What we need to do now is use the systems effectively.

## Estimating required resources using the scheduler

Although we covered requesting resources from the scheduler earlier, how do we know how much and
what type of resources we will need in the first place?

Answer: we don't. Not until we've tried it ourselves at least once. We'll need to benchmark our job
and experiment with it before we know how much it needs in the way of resources.

A good rule of thumb is to ask the scheduler for more time and memory (perhaps 20% more) than you expect your job to need after benchmarking. This ensures that minor fluctuations in run time or memory use will not result in your job being canceled by the scheduler. Keep in mind that the more resources you ask for, the longer your job will wait in the scheduler to run. 

## Benchmarking example

As an example, let's try benchmarking a very simple parallel program. This program calculates the integral under a function using the trapezoidal rule on N trapeziums. 

This work can be done in parallel by splitting the trapeziums equally among all processors that the job is running on. At the end, all processes will need to perform some global communication to combine their local sums.

With that in mind, let's grab the code. It is available at https://github.com/aniabrown/ARC_parallel_programming_exercises. 

We can clone the code from GitHub onto Cirrus after first loading the git module:

```
$ module load git
$ git clone https://github.com/aniabrown/ARC_parallel_programming_exercises
```
{: .language-bash}

```{.output}
Cloning into 'ARC_parallel_programming_exercises'...
remote: Enumerating objects: 30, done.
remote: Counting objects: 100% (30/30), done.
remote: Compressing objects: 100% (21/21), done.
remote: Total 30 (delta 8), reused 19 (delta 6), pack-reused 0
Unpacking objects: 100% (30/30), done.
```

The example is in the integral directory:
```
$ cd ARC_parallel_programming_exercises/integral
$ ls
```
{: .language-bash}

```{.output}
integral.c  integral.inp  integral_mpi.c  makefile
```

There are two different versions of the code: a serial version designed to run on one process and an MPI version designed to run on multiple processes. 

This code is written in C, which needs to be compiled into a machine readable executable before we can run it. To compile the serial version we can use the command:

```
$ make integral_serial
```
{: .language-bash}

Just this once, we will see what the code does by running on the login node -- we know that the program is quick enough to do this. 


```{.output}
Calculating integral from -1.000000 to 1.000000 using 1000000 trapeziums.
Using serial version.
Results:
 average process time is 0.024334 s with standard deviation 0.152275 over 100 trials
 value of integral = 3.141593
```

This code is fast enough than we would usually not bother running it on more than one core 
(the problem size is very small) but we can still use it to get a feel for how to choose 
how many cores to request. The main things to keep in mind are that eventually adding more
cores will likely not make the program run much faster (it may even run slower) and that the
more cores you ask for the longer your job will wait in the scheduler. 

> ## Running across multiple processes
> To compile the parallel version of the code, we will need to load the MPI module: 
>
>```
>module load mpt
>make integral_mpi
> ```
> {: .bash}
> There is a submission script, mpi_job.pbs, in the integral folder:
>```
>#!/bin/bash
>
># job configuration
>#PBS -N integral
>#PBS -l select=1:ncpus=2
>#PBS -l walltime=00:00:30
>
># Change to the directory that the job was submitted from
># (remember this should be on the /work filesystem)
>cd $PBS_O_WORKDIR
>
>module load mpt
>module load intel-compilers-17
>
>mpiexec_mpt -ppn 2 -n 2 ./integral_mpi
>```
> Edit this to run on job counts between 1 and 72 processes and record the average calculation time reported
> in each case. How does the program scale? Is there an ideal process count, taking into account queuing time? 
> 
> Note, the mpiexec_mpt command is used to launch the program on multiple processes. It takes the number of 
> processes per node as the first argument and the total number of processes in the job as the second argument.
> E.g. for a job running 72 processes across two nodes you would use -ppn 36 -n 72. You will also need to edit
> the #PBS -l select statement to match. 
> {: .bash}
{: .challenge}

** note -- if we run the serial version on a job with many processes, we will still only run on one process!]


Hint: try making a guess, eg 8 procs, then doubling, then halving 

{% include links.md %}
