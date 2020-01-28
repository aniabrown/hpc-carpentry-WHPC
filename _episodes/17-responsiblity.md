---
title: "Using shared resources responsibly"
teaching: 15
exercises: 5
questions:
- "How can I be a responsible user?"
- "How can I protect my data?"
- "How can I best get large amounts of data off an HPC system?"
objectives:
- "Learn how to be a considerate shared system citizen."
- "Understand how to protect your critical data."
- "Appreciate the challenges with transferring large amounts of data off HPC systems."
- "Understand how to convert many files to a single archive file using tar."
keypoints:
- "Clusters are designed to handle large number of users"
- "The shared resources to be careful with are memory and cpus on the login nodes, and I/O"
- "Your data on the system is your responsibility."
- "Plan and test large data transfers."
- "It is often best to convert many files to a single archive file before transferring."
- "Don't run stuff on the login node."
- "Really don't run stuff on the login node."
- "When in doubt, talk to the cluster support team first"
---

One of the major differences between using remote HPC resources and your own system 
(e.g. your laptop) is that they are a shared resource. How many users the resource is
shared between at any one time varies from system to system but it is unlikely you
will ever be the only user logged into or using such a system.

We have already mentioned one of the consequences of this shared nature of the resources:
the scheduling system where you submit your jobs, but there are other things you need 
to consider in order to be a considerate HPC citizen, to protect your critical data
and to transfer data 

> ## It's pretty hard to break a cluster
>
> While it is important to keep in mind the following guidelines for using the cluster responsibly,
> it is also possible to be too scared of breaking something -- remember, these systems exist to be used!
> An HPC cluster is a relatively robust system that is specifically designed to be able to handle multiple users 
> working on it at once -- for instance, there are systems preventing you from deleting files created
> by other users, and if you accidentally submit too many jobs the scheduler will notice and hold some of them
> for other users to get through. If you're ever in doubt about something you want to try, 
> get in touch with the support team for the 
> cluster you're using -- their email should be prominent in the cluster documentation
{: .callout}

## Be kind to the login nodes

The login node is often very busy managing lots of users logged in, creating and editing files
and compiling software! It doesn’t have any extra space to run computational work.

Don’t run jobs on the login node (though quick tests are generally fine). A “quick test” is
generally anything that uses less than 1 minute of time. If you
use too much resource then other users on the login node will start to be affected - their
login sessions will start to run slowly and may even freeze or hang. 

> ## Login nodes are a shared resource
>
> Remember, the login node is shared with all other users and your actions could cause
> issues for other people. Think carefully about the potential implications of issuing
> commands that may use large amounts of resource.
>
{: .callout}

You can always use the commands `top` and `ps ux` to list the processes you are running on a login
node and the amount of CPU and memory they are using. The `kill` command can be used along
with the *PID* to terminate any processes that are using large amounts of resource.

> ## Login Node Etiquette
> 
> Which of these commands would probably be okay to run on the login node?
> - python physics_sim.py
> - make
> - create_directories.sh
> - molecular_dynamics_2
> - tar -xzf R-3.3.0.tar.gz
> 
{: .challenge}

If you experience performance issues with a login node you should report it to the system
staff (usually via the helpdesk) for them to investigate. You can use the `top` command
to see which users are using which resources.

## Test before scaling

Can you see what's wrong with this submission script?

```
#!/bin/bash

# job configuration
#PBS -N long_python_job
#PBS -l select=1:ncpus=36
#PBS -l walltime=24:00:00

# Change to the directory that the job was submitted from
# (remember this should be on the /work filesystem)
cd $PBS_O_WORKDIR

python my_script.py
```
{: .language-bash}

Remember that you are generally charged for usage on shared systems. A simple mistake in a 
job script can end up costing a large amount of resource budget. Imagine a job script with 
a mistake that makes it sit doing nothing for 24 hours on 1000 cores or one where you have
requested 2000 cores by mistake and only use 100 of them! This problem can be compounded 
when people write scripts that automate job submission (for example, using job arrays).  When this happens it hurts both you
(as you waste lots of charged resource) and other users (who are blocked from accessing the
idle compute nodes).

On very busy resources you may wait many days in a queue for your job to fail within 10 seconds
of starting due to a trivial typo in the job script. This is extremely frustrating! Most
systems provide dedicated resources for testing that have short wait times to help you 
avoid this issue.

> ## Test job submission scripts that use large amounts of resource
> Before submitting a very large or very long job submit a short truncated test to ensure that
> the job starts as expected
>
> Before submitting a large collection of jobs, submit one as a test first to make sure everything works
> as expected.
{: .callout}

## Have a backup plan

Although many HPC systems keep backups, it does not always cover all the file systems available
and may only be for disaster recovery purposes (*i.e.* for restoring the whole file system if lost
rather than an individual file or directory you have deleted by mistake). Your data on the
system is primarily your responsibility and you should ensure you have secure copies of data
that are critical to your work.

Version control systems (such as Git) often have free, cloud-based offerings (e.g. Github, Gitlab)
that are generally used for storing source code. Even if you are not writing your own 
programs, these can be very useful for storing job scripts, analysis scripts and small
input files. 

For larger amounts of data, you should make sure you have a robust system in place for taking
copies of critical data off the HPC system wherever possible to backed-up storage. Tools such
as `rsync` can be very useful for this.

Your access to the shared HPC system will generally be time-limited so you should ensure you have a
plan for transferring your data off the system before your access finishes. The time required to
transfer large amounts of data should not be underestimated and you should ensure you have planned
for this early enough (ideally, before you even start using the system for your research).

In all these cases, the helpdesk of the system you are using should be able to provide useful
guidance on your options for data transfer for the volumes of data you will be using.

> ## Your data is your responsibility
> Make sure you understand what the backup policy is on the file systems on the system you are
> using and what implications this has for your work if you lose your data on the system. Plan
> your backups of critical data and how you will transfer data off the system throughout the
> project.
{: .callout}

## Transferring data

As mentioned above, many users run into the challenge of transferring large amounts of data 
onto or off HPC systems at some point.

If you have related data that consists of a large number of small files it
is strongly recommended to pack the files into a larger *archive* file for long term storage and
transfer. A single large file makes more efficient use of the file system and is easier to move,
copy and transfer because significantly fewer meta-data operations are required. Archive files can
be created using tools like `tar` and `zip`. We have already met `tar` when we talked about data
transfer earlier. 

If you are transferring large (>10GB) amounts of data it is always useful to run some tests that you can use 
to extrapolate how long it will take to transfer your data. 

## Getting help

If you believe you may have unusual needs, such as needing to transfer very large amounts of data to
the cluster, talk to the support team for the cluster you are using -- you should be able to find their
contact details in the cluster documentation. They are there to help, and in general will be much happier
answering a query ahead of time than needing to fix something after the fact!

After checking the cluster documentation, the support team is also your first port of call for any general advice on 
how to use the system. Depending on the cluster, the preferred method of contact may be direct email to the support
team, or there may be a forum that is public for all users to view. 

Finally, there will often be further training available for the particular cluster you are using. Many clusters will 
have a mailing list with updates on these events as well as important notices for users such as maintenance shut downs. 

{% include links.md %}
