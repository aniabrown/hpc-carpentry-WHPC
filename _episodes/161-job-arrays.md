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
- "Job arrays allow you to launch many jobs that are each assigned a different index value, using just one submission script."
---

We just saw an example of how parallelising a job to run over too many processes may eventually result in negative returns due to the time taken for all those processes to communicate with each other. However, sometimes we are in the lucky situation where the work we need to do can be separated into truly independent tasks, with no communication required between them. The classic example of this is a parameter search, where we run the same algorithm on a range of different inputs. In that case we can make use of a feature of the job scheduler known as a job array.

## Job Arrays

Job arrays are a way to cleanly submit many similar jobs while only having to define and launch one submission script. 

Here is an example submission script. Either copy paste it to `example-job.sh` or download it using: 

```
$ wget {{site.url}}{{site.baseurl}}/files/simple_job_array_example.tar.gz
$ tar -xzf simple_job_array_example.tar.gz
$ cd simple_job_array_example
```
{: .language-bash}

```
#!/bin/bash

# job configuration
#PBS -N job_array_example
#PBS -l select=1:ncpus=1
#PBS -l walltime=00:02:00
#PBS -J 1-4

# Change to the directory that the job was submitted from
# (remember this should be on the /work filesystem)
cd $PBS_O_WORKDIR

echo "Running job ${PBS_ARRAY_INDEX} of job array"

sleep 120
```
{: .language-bash}

This is very similar to scripts we've seen before, with two changes. The configuration parameter ```#PBS -J 1-4``` tells the scheduler to launch four jobs, assigning each an index which will range between one and four. We can also access this index that has been assigned to each job using the PBS variable ```PBS_ARRAY_INDEX```. 

We can launch the job array as we would launch a single job

```
$ {{ site.host_prompt }} {{ site.sched_submit }} {{ site.sched_submit_options }} example-job.sh
```
{: .language-bash}


The status of the job array as a whole can be viewed using ```qstat -u $USER```

```{.output}

indy2-login0: 
                                                            Req'd  Req'd   Elap
Job ID          Username Queue    Jobname    SessID NDS TSK Memory Time  S Time
--------------- -------- -------- ---------- ------ --- --- ------ ----- - -----
1266791[].indy2 ania     workq    job_array_    --    1   1    --  00:00 B   -- 
```

And we can also look at the individual jobs using ```qstat -t jobId[]```. Eg ```qstat -t 1266791[]```

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
$ ls
```
{: .language-bash}

```{.output}
example_job.pbs               job_array_example.e1266791.3  job_array_example.o1266791.2
job_array_example.e1266791.1  job_array_example.e1266791.4  job_array_example.o1266791.3
job_array_example.e1266791.2  job_array_example.o1266791.1  job_array_example.o1266791.4
```
Currently these jobs only print out their unique id, which is not very useful. Additionally, the output of these jobs is scattered among all the different output files, which we will need to process somehow. 

## Example with input/output files per job

Let's take a look at a more realistic example. Download and untar the example files using:

```
$ wget {{site.url}}{{site.baseurl}}/files/job_array_example.tar.gz
$ tar -xzf job_array_example.tar.gz
$ cd job_array_example
$ ls
```
{: .language-bash}

```{.output}
input_1.txt  input_3.txt  process_file.py       summarise_outputs.py
input_2.txt  input_4.txt  submit_job_array.pbs
```

There are four input files, each containing a list of numbers. We can see an example using `cat input_1.txt`.

```{.output}
31587
16729
29533
25846
21477
6016
25138
30079
4120
12355
793
26439
31226
18139
4081
9797
32245
6563
15591
7784
``` 

The short python script `process_file.py` takes one of these input files, finds the maximum number in the file and prints that value to an output file that we specify -- very slightly more useful than our first example. 

The script takes two arguments -- the input and output file names. For example:

```
$ module load anaconda/python3
$ python3 process_file.py input_1.txt output_1.txt
```
{: .language-bash}

> ## Process all four input files using a job array
> The example folder contains the same submission script as in the previous example. Can you add a line that 
> runs the `process_file.py` script on each input file? Remember, submitting the job array submission script will
> launch four separate jobs each with their own value of `${PBS_ARRAY_INDEX}`.
> > ## Solution
> > ```
> > #!/bin/bash
> >
> > # job configuration
> > #PBS -N job_array_example
> > #PBS -l select=1:ncpus=1
> > #PBS -l walltime=00:00:30
> > #PBS -J 1-4
> >
> > # Change to the directory that the job was submitted from
> > # (remember this should be on the /work filesystem)
> > cd $PBS_O_WORKDIR
> >
> > echo "Running job ${PBS_ARRAY_INDEX} of job array"
> >
> > module load anaconda/python3
> >
> > python3 process_file.py input_${PBS_ARRAY_INDEX}.txt output_${PBS_ARRAY_INDEX}.txt
> >```
> >{: .language-bash}
> {: .solution}
{: .challenge}

In this example, we create one output file per job in the job array -- this is fairly typical. The last piece of work we need to do is then to process those output files -- in this example there is a python script, `summarise_outputs`, which reads through all the output files and puts them into a summary results table, `summary_file.csv`. 

```
$ module load anaconda/python3
$ python3 summarise_outputs.py
$ cat summary_file.csv
```
{: .language-bash}

```{.output}
job, max
1,32245
2,32244
3,29748
4,30474
```

> ## Responsible use
> Warning: Job arrays give you a lot of power -- use it wisely! All the jobs in a job array will go 
> through the queuing system so if you submit a job array that is too large eventually your jobs will 
> get held to allow other users through. However, it's still best not to submit thousands of jobs at once, 
> and always remember to test on a small numbers of jobs first!
{: .callout}

{% include links.md %}
