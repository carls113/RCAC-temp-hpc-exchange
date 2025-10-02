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

.. admonition:: Numpy yet again

   If you faced the Numpy missing issue
   earlier, we need to have our Conda
   enviornment activated in our submission
   script::

      #!/bin/bash
      #SBATCH -A lab_queue -p cpu -q standby
      #SBATCH -c 128 -t 00:10:00

      module load conda
      conda activate example
      module load monitor

      mkdir -p ${SCRATCH}/${SLURM_JOB_NAME}
      cd ${SCRATCH}/${SLURM_JOB_NAME}

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

We don't have to submit this one, but to
submit it, we would run the `submit.sh`
file as a program::

   $ ./submit.sh
   Submitted batch job 209526
   Submitted batch job 209527
   Submitted batch job 209528
   ...

This will create 30 different scratch folders
named `example-01` to `example-30`, copy all
relevant files there, run the python script
30 times and bundle all the folders up to
send to Fortress.

We can also use something called "Slurm
job arrays" to automate our workflow.
This way, we can submit a single job instead
of manually submitting many copies.
The job is duplicated and runs `N` times
with only `SLURM_ARRAY_TASK_ID` environment
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

.. admonition:: Numpy for the last time

   If you faced the Numpy missing issue
   earlier, we need to have our Conda
   environment activated in our submission
   script::

      #!/bin/bash
      #SBATCH -A lab_queue -p cpu -q standby
      #SBATCH -c 128 -t 00:10:00
      #SBATCH -a 1-30

      module load conda
      conda activate example
      module load monitor

      site=example-${SLURM_ARRAY_TASK_ID}
      mkdir -p ${SCRATCH}/${site}
      cd ${SCRATCH}/${site}

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

One naive example of this would be
the following job script::

   #!/bin/bash
   #SBATCH -A lab_queue -p cpu -q standby
   #SBATCH -c 128 -t 00:10:00

   module load conda
   conda activate example

   site=example-naive
   mkdir -p ${SCRATCH}/${site}
   cd ${SCRATCH}/${site}

   cp ~/array.sh ~/example.py ./

   python example.py > results1.out &
   python example.py > results2.out &
   python example.py > results3.out &

   wait

   cd ..
   htar -cvf ${site}.tar ${site}/

This just manually runs our workflow
multiple times and saves the results
to a different output file each time.
You could extend this to doing multiple
different workflows in tandem, as long
as they fit within the job (RAM and CPUs)
you can do whatever you want with the
resources.

.. note::

   This is NOT real science, we're just doing
   the same thing 3 times. In reality, you
   would want to do this (usually) with different
   inputs, so you can get different tasks done.

There are many tools out there for automating
computing of many tasks with the pilot
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
