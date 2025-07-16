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
a finite number of compute jobs, many
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

Please use a command-line text editor to
create this shell script, named `example.sh`:

.. code-block::

   #!/bin/bash
   #SBATCH -A standby -c 8 -t 00:01:00

   module load conda
   python example.py

Once that is written, submit it to the
scheduler with the `sbatch` program:

.. code-block::

   $ ls
   example.py  example.sh  ...

   $ sbatch example.sh
   Submitted batch job 19804935

You have now submited your first supercomputing
resource allocation request. This job ID number
is helpful to note down as it can be used elsewhere.

.. hint::
   
   The output of your job will, by default, be saved in
   files with this ID (e.g. `slurm-1980435.out`).

The job that we submitted requested 8 cores for 1
minute from the `standby` account.

Use the `slist` program to show which Slurm accounts
are available for you to submit to:

.. code-block::

   $ slist
                        Current Number of Cores                     Node
   Account          Total    Queue     Run    Free   Max Walltime   Type
   ============== ================================= ============== ======
   debug              256        1       0     256       00:30:00      A
   gpu                160        0      78      82     1-00:00:00      G
   highmem            768     1152     632     136     1-00:00:00      B
   standby          55424   686860   32474     573       04:00:00      A

On our clusters, we have owner and system accounts.
In the `slist` output above, all the system accounts
are shown, but no owner accounts. Owner accounts
have the highest priority for the resources that
are allocated to it. Jobs can also be submitted to
the `standby` account and will run when idle
resources are availble (up to a limit) and with
equal priority to other users.

The `highmem` and `gpu` accounts are associated
with different node types, B and G, respectively.
These are both available to everyone similar to
`standby`. The `debug` account grants quicker
access for lesser resources within reason.

To see what the different node types mean, use
the `sfeatures` program:

.. code-block::

   $ sfeatures
   NODELIST    CPUS  MEMORY   AVAIL_FEATURES   GRES
   a[000-449]  128   257400   A,a              (null)
   b[000-005]  128   1031600  B,b              (null)
   c[000-015]  256   515500   C,c              (null)
   g[000-004]  32    515500   G,g,MI210,mi210  gpu:3

Use `AVAIL_FEATURES` as tags as a constraint
on clusters with more distinct hardeware
types.

.. hint::
 
   Use the `-C` or `\-\-constraint` option with `sbatch` to
   target one of the feature tags.

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
     - Account (default: standby)
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
   Account : standby
   Partition : a
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

   To cancel all our own jobs: `scancel --me`

.. important::

   Cancelling an application this way isn't very
   "nice", in that it immediately stops everything
   and can cause problems if in the middle of file
   operations.

To get an interactive job (or essentially a shell
on a compute node), use the `sinteractive` program
(which is RCAC specific). You will need to specify
the same parameters as with `sbatch` (e.g. account,
cores, nodes, time).

.. code-block::

   username@login03.negishi:[~] $ sinteractive -A standby -c 4 -t 00:10:00
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

**Good citezenship**

Last, but not least, there are four main points to touch
on about good citizenship on HPC resources:

#. Do not request for excessive resources knowingly
   (don't ask for a large memory node if it's not needed)
#. Do not abuse file systems
   (heavy I/O for /depot space, use /scratch instead)
#. Do not submit lots of timy jobs, instead use the pilot-job pattern
   with a workflow tool
#. Do not submit jobs and camp
   (don't submit a GPU job from the Gateway for 24 hours so it's
   ready for you in the afternoon and then forget about it)

Next section\:
:doc:`../week3/week3`

