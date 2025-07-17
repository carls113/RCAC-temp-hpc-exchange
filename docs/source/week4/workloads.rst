Managing workloads and jobs
===========================
:doc:`week4`

**Previous section:**
:doc:`monitor`

In this last section, we will discuss
how to manage many-task workflows. Because,
we often want to run lots of things,
especially the same thing on many inputs.

There's two main paradigms that we can
adhere to when talking about workload
management:

#. Submit lots of separate jobs
#. Submit one job with many tasks inside (pilot job)

Both approaches can be handled many
different ways. We will only go over
a few in this workshop.

One approach is to build a job flow
script that just submits lots of jobs
into different directories. Be sure to
modify our job script to instead rely on
the `SLURM_JOB_NAME` environment variable
for the directory. For this, we need two
files: The submitter and the worker.

The submitter we will call `submit.sh`
and should look like this::

   #!/bin/bash
   
   for step in {01..30}
   do
      sbatch example.sh -J example-${step}
   done

For the worker, we will edit the
`example.sh` submission script we
had earlier to use the new paradigm::

   #!/bin/bash
   #SBATCH -A lab_queue -p cpu -q standby
   #SBATCH -c 128 -t 00:10:00

   module load conda
   module load monitor

   mkdir -p ${SCRATCH}/${SLURM_JOB_NAME}
   cd ${SCRATCH/${SLURM_JOB_NAME}

   cp ~/example.sh ~/example.py ./

   monitor cpu percent --csv > cpu_per.csv &
   monitor cpu memory --csv > cpu_mem.csv &
   python example.py > results.out

   cd ..
   htar -cvf ${SLURM_JOB_NAME}.tar ${SLURM_JOB_NAME}/

We've only modified the areas of the
script that were affected by the
working directory name that we
created to hold job outputs. Whatever
we give for the job name will now
result in that directory being created
and used in Scratch. See the *file pattern*
section of the *manual page* for details.

We can also use something called "Slurm
job arrays" to automate our workflow.
This way, we can submit a single job instead
of manually submitting many copies.
The job is duplicated and runs `N` times
wiht only `SLURM_ARRAY_TASK_ID` environment
variable different.

Create a new submission script called
`array.sh` and copy in::

   #!/bin/bash
   #SBATCH -A lab_queue -p cpu -q standby
   #SBATCH -c 128 -t 00:10:00
   #SBATCH -a 1-30

   module load conda
   module load monitor

   site=example-${SLURM_ARRAY_TASK_ID}
   mkdir -p ${SCRATCH}/${site}
   cd ${SCRATCH/${site}

   cp ~/array.sh ~/example.py ./

   monitor cpu percent --csv > cpu_per.csv &
   monitor cpu memory --csv > cpu_mem.csv &
   python example.py > results.out

   cd ..
   htar -cvf ${site}.tar ${site}/

This will submit an array of 30 jobs
to do the same task many times and
save the output in different directories.

These examples are only the beginning,
the mechanics of a job array can get
a lot more sophisticated from here. 

However, there are limitations on
what you can do by submitting many
jobs at the same time. For one, it
clogs our database if you submit too
many jobs. For this reason, we ask that
you prefer use the pilot job paradigm.
This is where you request one job and
run many tasks inside that job.

There are many tools out there for
computing many tasks with the pilot
job paradigm. Two examples are:
HTCondor and HyperShell. They both
achieve the task of computing many
things using a single Slurm job
and each have different features.

The challenges of *high-throughput*,
**many-task** computing like this
are becoming more common. If you have
more questions, you can always submit a
ticket to `our ticketing system`_

.. _our ticketing system: rcac-help@purdue.edu

.. toctree::
   :hidden:
   
   ../congratulations

Next section\:
:doc:`../congratulations`
