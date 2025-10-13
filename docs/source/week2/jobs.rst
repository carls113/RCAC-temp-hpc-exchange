Submit jobs using the scheduler
===============================
:doc:`week2`

**Previous section:**
:doc:`what_cluster`

In this section, we will talk about what
exactly is a scheduler and how to
interact with it to get your work
done.

First, what is a scheduler? We use a
scheduler called **Slurm** which is the
most common one used by major computing
centers. It is a resource manager and a
workload manager. Essentially, there are
a finite number of compute nodes, many
users of the system and even more jobs
to be run. the scheduler tracks resource
allocation requests and manages the
execution of those jobs. Users package
their work (directly or indirectly) into
self-contained **shell scripts** submitted
to Slurm.

So what is a `resource allocation request`
or a `job`? It's essentially a shell script
that encapsulates everything for the work
to be done. This shell script can be submitted
to the scheduler via the `sbatch` program.
The details of what resources are requested
are commonly inside the script itself, but
can also be passed to the `sbatch` program
manually.

Purdue's community clusters require a couple of
pieces of information to actually submit a job.
The first of which is what account to submit
that job to.

Use the `slist` program to show which Slurm accounts
are available for you to submit to:

.. code-block::

   $ slist
            			Current Negishi Accounts
   ==============================================================================
                  |              CPU Partition              |    AI Partition
   Accounts       |   Total     Queue      Run      Free    |  GPU Hours Balance
   ============== | ========= ========= ========= ========= | ===================
   lab_queue      |       128        32        64        32 |                 0.0

.. important::

   The `lab_queue` here is just a placeholder, in reality,
   this should be something similar to your PI's username.
   in the following examples, please substitute `lab_queue`
   with an account found in the output of `slist`.

.. admonition:: Empty slist

   If your `slist` output is empty, that means that you
   aren't associated with any groups that have resources
   allocated to their accounts on that cluster.

Once you know what account you want to submit with,
you need to know what part of the cluster you want
to submit that job to. To show the different partitions
available on the cluster, run the `showpartitions`
program:

.. code-block::

   $ showpartitions
   Partition statistics for cluster negishi at Thu Jul 17 16:12:58 EDT 2025
   Partition       #Nodes     #CPU_cores  Cores_pending   Job_Nodes MaxJobTime Cores Mem/Node
   Name  State   Total  Idle  Total   Idle Resorc  Other   Min   Max  Day-hr:mn /node     (GB)
   cpu      up     446     0  57088   2078      0  15973     1 infin   infinite   128     257
   highmem  up       6     0    768    236      0   2114     1 infin   infinite   128    1031
   gpu      up       5     3    160    132      0      0     1 infin   infinite    32     515

To see what the different node types mean, use
the `sfeatures` program:

.. code-block::

   $ sfeatures
   NODELIST    CPUS  MEMORY   AVAIL_FEATURES   GRES
   a[000-449]  128   257400   A,a              (null)
   b[000-005]  128   1031600  B,b              (null)
   g[000-004]  32    515500   G,g,MI210,mi210  gpu:3

Use `AVAIL_FEATURES` as tags as a constraint
on clusters with more distinct hardware
types.

.. hint::

   Use the `-C` or `\-\-constraint` option with `sbatch` to
   target one of the feature tags.

Lastly, something you may want to specify is the
*Quality of Service* (QoS) for the job. This would
include the `standby` QoS for jobs that you want
to use idle resources and not the high-priority
resources in your lab account.

For more information see the page here:
`Scheduler Modernization <https://www.rcac.purdue.edu/news/7245>`_

Essentially, you need to specify an account to submit
to and a part of the cluster you want to submit to.
Sometimes, you may want to specify a QoS for your
job as well.

Please use a command-line text editor to
create this shell script, named `example.sh`:

.. code-block::

   #!/bin/bash
   #SBATCH -A lab_queue -p cpu -q standby
   #SBATCH -c 8 -t 00:01:00

   module load conda
   python example.py

.. admonition:: Numpy again

   If you ran into the *no module named numpy*
   error earlier, you will need to activate
   your conda environment inside your job as well.

   .. code-block::

      #!/bin/bash
      #SBATCH -A lab_queue -p cpu -q standby
      #SBATCH -c 8 -t 00:01:00

      module load conda
      conda activate example
      python example.py

Once that is written, submit it to the
scheduler with the `sbatch` program:

.. code-block::

   $ ls
   example.py  example.sh  ...

   $ sbatch example.sh
   Submitted batch job 19804935

You have now submitted your first supercomputing
resource allocation request. This job ID number
is helpful to note down as it can be used elsewhere.

.. hint::

   The output of your job will, by default, be saved in
   files with this ID (e.g. `slurm-1980435.out`).

The job that we submitted requested 8 cores for 1
minute from your lab's account, to the CPU part of
the cluster, using the `standby` QoS.

Following is a list of common Slurm resource
parameters that you may want to specify in your
shell script:

.. list-table:: Common Slurm resource parameter reference
   :widths: 10 40 50
   :header-rows: 1

   * - Shortcut
     - Long form option
     - Meaning
   * - `-A`
     - `\-\-account`
     - Account (default: `lab_queue`)
   * - `-p`
     - `\-\-partition
     - Partition (default: `cpu`)
   * - `-q`
     - `\-\-qos
     - Quality of Service (default: none)
   * - `-J`
     - `\-\-job-name`
     - Job name (default: <script name>)
   * - `-t`
     - `\-\-time`
     - Walltime limit
   * - `-N`
     - `\-\-nodes`
     - Number of nodes (default: 1)
   * - `-n`
     - `\-\-ntasks`, `\-\-ntasks-per-node`
     - Nunber of Slurm tasks (default: 1)
   * - `-c`
     - `\-\-cpus-per-task`
     - Cores per task (default: 1)
   * - `\-\-mem`
     - `\-\-mem-per-cpu`
     - Memory (default: ~2GB per core)
   * - `-G`
     - `\-\-gpus`, `\-\-gpus-per-node`
     - Number of GPUs (default: 0)
   * - `-o`
     - `\-\-output`
     - File path for application output

You can also use the command `man sbatch` to
learn more about different parameters

You can use the `squeue` program to list currently scheduled
(pending and running) jobs. By default it will show all jobs
from all users on the cluster, which leads to a lot of
output.

Quiz: What option do we need to limit the output to a
specific account? Specific user? Only our own jobs?

.. admonition:: Answer
   :collapsible: closed

   Specific account: `squeue -A ACCOUNT_NAME`

   Specific user: `squeue -u USERNAME`

   Only our own jobs: `squeue \-\- me`

To learn more about the parameters of a single job, you can
use the `jobinfo` program. To use `jobinfo`, the command
would be `jobinfo JOB_ID`, where the `JOB_ID` is replaced
with the job ID mentioned above (which you can also check
with the `squeue` program).

.. code-block::

   $ jobinfo 19804944
   Name : example.sh
   User : username
   Account : lab_queue
   Partition : cpu
   Nodes : a305

There are also `jobenv`, `jobcmd`, and `jobscript`
programs that tell you more information about the
job as it was submitted.

.. important::

   These four commands: `jobinfo`, `jobenv`, `jobcmd`,
   and `jobscript` are all RCAC-specific. It is not
   guaranteed that other HPC centers will have these
   programs implemented.

To cancel a job, use the `scancel` program. It used by
running `scancel JOB_ID`, where `JOB_ID` is replaced
with the job ID mentioned before.

.. code-block::

   $ scancel 19804944

Quiz: Using the `man` program, what option do we need
to cancel all our own jobs?

.. admonition:: Answer
   :collapsible: closed

   To cancel all our own jobs: `scancel \-\-me`

.. important::

   Cancelling an application this way isn't very
   "nice", in that it immediately stops everything
   and can cause problems if in the middle of file
   operations.

To get an interactive job (or essentially a shell
on a compute node), use the `sinteractive` program
(which is RCAC specific). You will need to specify
the same parameters as with `sbatch` (e.g. account,
partition, QoS, cores, nodes, time).

.. code-block::

   username@login03.negishi:[~] $ sinteractive -A lab_queue -p cpu -q standby -c 4 -t 00:10:00
   salloc: Pending job allocation 19809515
   salloc: job 19809515 queued and waiting for resources
   salloc: job 19809515 has been allocated resources
   salloc: Granted job allocation 19809515
   salloc: Waiting for resource configuration
   salloc: Nodes a195 are ready for job
   username@a195.negishi:[~] $

Notice that before the `sinteractive` program was run,
we were on `login03.negishi` and after it was run, we
are now on `a195.negishi`, this is a good way to tell
if you are running on a compute node, or on a login
node.

To get out of the interactive slurm job, simply
run the `exit` program and you'll be returned to
the login node you were on previously.

**Good citizenship**

Last, but not least, there are four main points to touch
on about good citizenship on HPC resources:

#. Do not request for excessive resources knowingly
   (don't ask for a large memory node if it's not needed)
#. Do not abuse file systems
   (heavy I/O for /depot space, use /scratch instead)
#. Do not submit lots of tiny jobs, instead use the pilot-job pattern
   with a workflow tool
#. Do not submit jobs and camp
   (don't submit a GPU job from the Gateway for 24 hours so it's
   ready for you in the afternoon and then forget about it)

Next section\:
:doc:`../week3/week3`
