---
title: "Scheduling multiple similar jobs"
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

We just saw an example of how parallelising a job to run over too many processes may eventually result in negative returns due to the time taken for all those processes to communicate with each other. However, sometimes we are in the lucky situation where the work we need to do can be separated into truly independent tasks, with no communication required between them. The classic example of this is a parameter search, where we run the same algorithm on a range of different inputs. In that case we can make use of a feature of the job scheduler known as a job array.

## Job Arrays

Job arrays are a way to cleanly submit many similar jobs while only having to define and launch one submission script. 

Here is an example submission script. 

Note the ****. This is the range of ids of the jobs that will be launched. 

We can launch the submission script once using *** as usual. 

We can see the 

This submission script will currently launch exactly the same job 5 times -- this is not very useful! 

Warning: Job arrays give you a lot of power -- use it wisely! All the jobs in a job array will go through the queuing system so if you submit a job array that is too large eventually your jobs will get held to allow other users through. However, it's best not to submit thousands of jobs at once, and always remember to test on small numbers of jobs first!

Warning: No communication is a slight lie -- you might need to do some small amount of processing afterwards. 


{% include links.md %}
