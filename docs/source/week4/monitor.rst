Resource utilization and monitoring
===================================
:doc:`week4`

**Previous section:**
:doc:`history`

Now that we can submit multinode jobs,
we should check to make sure they can
utilize the resources efficiently.
There is no special way to infer usage
other than to test directly.

You can use the `jobinfo` program with
the ID to see which node your job is on
and `ssh` directly there.

.. code-block::

   $ sbatch example.sh --exclusive
   Submitted btach job 19823415

   $ squeue --me
   JOBID     USER       ACCOUNT  NAME        NODES  CPUS  TIME_LIMIT  ST  TIME
   19823415  username lab_queue  example.sh  1         8       10:00   R  0:05

.. note::

   The use of `\-\-exclusive` (or similar)
   is important here to not get polluted
   by others' jobs on the node.

Once you've done `jobinfo` to determine
which node your job has landed on, you
can `ssh` directly to the node. This is
something you can do only if you have a
Slurm job on the node. Once on the node,
use a tool like `htop` to inspect CPU
and memory activity (press `q` to quit).

.. code-block::

   username@login03.negishi:[~] $ ssh a200

   username@a200.negishi:[~] $ htop -u username
   <full screen app>

.. note::

   When using `ssh` within a cluster's
   subnet, there's no need to specify
   the subnet, or the username.

However, this isn't real telemetry. We
want to collect and store the data. To
do this, use the `monitor` utility
(which is RCAC specific) to gather data
on CPU and GPU metrics.

.. code-block::

   $ module load monitor
   $ monitor cpu memory
   DATE TIME HOSTNAME monitor.cpu.memory 15.7
   DATE TIME HOSTNAME monitor.cpu.memory 15.7
   DATE TIME HOSTNAME monitor.cpu.memory 15.7
   DATE TIME HOSTNAME monitor.cpu.memory 15.7
   ...

.. note::

   Use `\-\-help` or `man monitor` to check
   for usage details. You can also check our
   user guides for more recommendations.

Now let's do this for an actual Slurm job.
Edit your `example.sh` submission script
to look like this::

   #!/bin/bash
   #SBATCH -A lab_queue -p cpu -q standby
   #SBATCH -c 128 -t 00:10:00

   module load conda
   module load monitor

   mkdir -p ${SCRATCH}/example
   cd ${SCRATCH}/example

   cp ~/example.sh ~/example.py ./

   monitor cpu percent --csv > cpu_per.csv &
   monitor cpu memory --csv > cpu_mem.csv &
   python example.py > results.out

   cd ..
   htar -cvf example.tar example/

Be sure to ask for all the resources on
the node so you don't collect data on your
neighbor's job! Start each monitoring task
before starting your application.

Quiz: Why do we need to use the `\&` on each
of these commands in the script?

.. admonition:: Answer
   :collapsible: closed

   If we didn't, the node would be stuck
   on the monitor command until the walltime
   ran out.

Next section\:
:doc:`workloads`

