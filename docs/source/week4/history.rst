Job history
===========
:doc:`week4`

**Previous section:**
:doc:`multinode`

Let's submit a job again with
`sbatch` and check on it with
`squeue`.

.. code-block::

   $ sbatch example.sh
   Submitted batch job 19823415

   $ squeue --me
   JOBID     USER      ACCOUNT  NAME       NODES  CPUS   TIME_LIMIT  ST  TIME
   19823415  username  standby  example.sh     1     8      10:00     R  0:05

   $ squeue --me
   JOBID     USER      ACCOUNT  NAME       NODES  CPUS   TIME_LIMIT  ST  TIME

Notice that the job disappeared
from the output of `squeue` after
completion. How can we check on jobs
after they've disappeared here? If
you know the jo ID, you can always
run `jobinfo`::

   $ jobinfo 19823415
   Name       : example.sh
   User       : username
   Account    : standby
   Partition  : a
   Nodes      : a200
   Cores      : 4
   GPUs       : 0
   State      : COMPLETED
   ExitCode   : 0:0
   ...

But how do we query the full job
history?

We can use the `sacct` program to
search deeper into our job history.
See `man sacct` for details, but
`-X -a` are recommended options::

   $ sacct -u username
   JobID           Jobname  Partition    Account  AllocCPUS      State ExitCode
   ------------ ---------- ---------- ---------- ---------- ---------- --------
   19822708     multinode+          a    standby        256  COMPLETED      0:0
   19822708.ba+      batch               standby        128  COMPLETED      0:0
   19822708.ex+     extern               standby        256  COMPLETED      0:0
   19822708.0     hostname               standby        256  COMPLETED      0:0
   19822977     interacti+          a    standby          1  COMPLETED      0:0
   19822977.in+ interacti+               standby          1  COMPLETED      0:0
   ...

Quiz: How can we limit output to
a specific range in time? Are there
other fields we can display?

.. admonition:: Answer
   :collapsible: closed

   Use the `-S/\-\-starttime` and
   `-E/\-\-endtime` options to control
   the window of time shown. The other
   fields can be found by running
   `sacct \-\-helpformat`.

You can use the `\-\-name` option
or many others like it to filter
for specific jobs. See `man sacct`
for more details.

An example of a job search that is
only for a specific username with a
specific submission account would be::

   $ sacct -aX -u username -A standby

This shows you what jobs you have run
with the `standby` account.

Quiz: How do we control output fields?

.. admonition:: Answer
   :collapsible: closed

   Use the `-o/\-\-format` option
   to control what fields are outputted.

Next section\:
:doc:`monitor`

