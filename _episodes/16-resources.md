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

As an example, let's try benchmarking a very simple parallel program. This program calculates the temperature at a series of points along a beam over time given an initial temperature distribution, using an equation based on the values of neighbouring points. 

This work can be done in parallel by splitting the points equally among all processors that the job is running on. Every time step, there will need to be some communication among processes to share values at the edges of each region, which will use up some time.  

With that in mind, let's grab the code. It is available at https://github.com/aniabrown/ARC_parallel_programming_exercises. 

We can clone the code from GitHub onto Cirrus after first loading the git module:

```
$ module load git
$ git clone https://github.com/aniabrown/ARC_parallel_programming_exercises
```
{: .language-bash}

The example is in the pde directory:
```
$ cd ARC_parallel_programming_exercises/pde
$ ls
```
{: .language-bash}

[** ls output]

There are two different versions of the code: a serial version designed to run on one process and an MPI version designed to run on multiple processes. 

This code is written in C, which needs to be compiled into a machine readable executable before we can run it. To compile the serial version we can use the command:

```
$ make pde_serial
```
{: .language-bash}

Just this once, we will see what the code does by running on the login node -- we know that the program is quick enough to do this. 

[** output]

[** note -- if we run the serial version on a job with many processes, we will still only run on one process!]


Hint: 

{% include links.md %}
