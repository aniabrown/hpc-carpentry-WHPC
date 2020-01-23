---
title: "Scheduling multiple similar jobs"
teaching: 15
exercises: 10
questions:
- "How do we launch many similar jobs?"
- "How can we monitor job arrays?" 
objectives:
- "Understand how to launch similar jobs using a job array."
keypoints:
- "Job arrays allow you to launch many jobs that differ by only a single parameter, using just one submission script."
---

We just saw an example of how parallelising a job to run over too many processes may eventually result in negative returns due to the time taken for all those processes to communicate with each other. However, sometimes we are in the lucky situation where the work we need to do can be separated into truly independent tasks, with no communication required between them. The classic example of this is a parameter search, where we run the same algorithm on a range of different inputs. In that case we can make use of a feature of the job scheduler known as a job array.

## Job Arrays

Job arrays are a way to cleanly submit many similar jobs while only having to define and launch one submission script. 

Here is an example submission script: 

```
#!/bin/bash

# job configuration
#PBS -N job_array_example
#PBS -l select=1:ncpus=1
#PBS -l walltime=00:00:30
#PBS -J 1-4

# Change to the directory that the job was submitted from
# (remember this should be on the /work filesystem)
cd $PBS_O_WORKDIR

echo "Running job ${PBS_ARRAY_INDEX} of job array"

sleep 10

```
{: .language-bash}

This is very similar to scripts we've seen before, with two changes. The configuration parameter ```#PBS -J 1-4``` tells the scheduler to launch four jobs, assigning each an index which will range between one and four. We can also access this index that has been assigned to each job using the PBS variable ```PBS_ARRAY_INDEX```. 

We can launch the job array as we would launch a single job

```
qsub -A tc007 -q R1247997 example-job.sh
```
{: .language-bash}


The status of the job array can be viewed summarised in a single line using ```qstat -u $USER```

```{.output}

indy2-login0: 
                                                            Req'd  Req'd   Elap
Job ID          Username Queue    Jobname    SessID NDS TSK Memory Time  S Time
--------------- -------- -------- ---------- ------ --- --- ------ ----- - -----
1266791[].indy2 ania     workq    job_array_    --    1   1    --  00:00 B   -- 
```

And we can also look at the individual jobs using ```qstat -t jobId[]```. Eg ```qstat -t 1266784[]```

```{.output}
Job id            Name             User              Time Use S Queue
----------------  ---------------- ----------------  -------- - -----
1266791[].indy2-l job_array_examp  ania                     0 B workq           
1266791[1].indy2- job_array_examp  ania              00:00:00 R workq           
1266791[2].indy2- job_array_examp  ania              00:00:00 R workq           
1266791[3].indy2- job_array_examp  ania              00:00:00 R workq           
1266791[4].indy2- job_array_examp  ania              00:00:00 R workq 
```

Remember, for all purposes other than ease of launching, these are completely separate jobs. They may get scheduled to run at different times, and each will generate its own output and error files:

```
ls
```
{: .language-bash}

```{.output}
example_job.pbs               job_array_example.e1266784.3  job_array_example.o1266784.2
job_array_example.e1266784.1  job_array_example.e1266784.4  job_array_example.o1266784.3
job_array_example.e1266784.2  job_array_example.o1266784.1  job_array_example.o1266784.4
```

This submission script will currently launch exactly the same job 5 times -- this is not very useful! 

> ## Responsible use
>Warning: Job arrays give you a lot of power -- use it wisely! All the jobs in a job array will go through the queuing system >so if you submit a job array that is too large eventually your jobs will get held to allow other users through. However, >it's best not to submit thousands of jobs at once, and always remember to test on small numbers of jobs first!
{: .callout}

{% include links.md %}
