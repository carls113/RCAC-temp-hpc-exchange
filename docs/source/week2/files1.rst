File storage and transfer
=========================
:doc:`week2`

**Previous section:**
:doc:`access`

Now that we can get onto the cluster, we
want to get our data and files onto it as
well.

There are four main areas that you may want to
store data/files on:

#. Home
#. Scratch
#. Depot
#. Fortress

We will discuss in more detail what each of these areas are
in the :doc:`../week4/week4` section: :doc:`../week4/files2`.

For now, we will put everything into our home directories,
as that is where we land whenever we log into the
clusters.

There are 6 main ways to get data and files onto and off of
the clusters:

#. Open on Demand
#. scp
#. sftp
#. rsync
#. SMB
#. Globus

**Open on Demand**

In the files tab of the Open on Demand page, there are
upload and download buttons, but they are limited in
what they can do. e.g. there is a file size limit of
100 GB to upload and if your connection is flaky at
all, you're going to have a bad time.

**scp**

`scp` stands for `secure copy protocol` and is the
server version of the `cp` we saw last week (:ref:`cp`)
It too need a source and a destination, but one of
them may be a server.

Copying to a cluster:

.. code-block::

   $ scp ./source_file USERNAME@CLUSTER.rcac.purdue.edu:~/some_dir/cluster_file_name

Copying from a cluster:

.. code-block::

   $ scp USERNAME@CLUSTER.rcac.purdue.edu:~/some_dir/cluster_file_name ./destination_file

When copying from a cluster, the destination file will
go into the directory you are currently in. You can also
specify a path you want the destination file to go to.
This path can be either relative, or absolute.

**sftp**

`sftp` stands for `secure file transfer protocol` is a
reliable way to transfer files between the cluster and
another computer.

Essentially, `sftp` starts a file transfer shell on a
remote computer. Simple use the command `sftp USERNAME@CLUSTER.rcac.purdue.edu`
to start the file transfer session. After logging in,
use the `get` and `put` programs to transfer to and from
the cluster you are connected to:

.. code-block::

   $ sftp USERNAME@CLUSTER.rcac.purdue.edu

      (transfer TO CLUSTER)
   sftp> put sourcefile somedir/destinationfile
   sftp> put -P sourcefile somedir/

      (transfer FROM CLUSTER)
   sftp> get sourcefile somedir/destinationfile
   sftp> get -P sourcefile somedir/

   sftp> exit

When transferring to and from the cluster via `sftp`,
the transferring on the side of your local computer will
be relative to the directory you were in when you initiated
the `sftp` session.

**rsync**

`rsync` is similar to `scp`, but much more fully-featured.
It includes features to auto-resume transfers in case of
disconnection. It has the same format of arguments as `scp`,
but has many more options, check them out with:

.. code-block::

   $ man rsync

**SMB**

`SMB`, also known as `Samba` is a way to connect a
remote drive to your computer to transfer files
back and forth to the clusters in a graphical way.

To learn more about this option, please visit this
site: `SMB drives <https://www.rcac.purdue.edu/knowledge/negishi/storage/transfer/cifs>`_

**Globus**

For transferring large data to the cluster, you will
want to use the `Globus transfer service <https://transfer.rcac.purdue.edu>`_

If you want to transfer files from your local machine
to the cluster, you will need to install the `Globus Connect
Personal` software on your local computer.

From the Globus transfer service, you can select a source
and a destination. It will handle the actual transferring
of the file(s) for you, resuming if there's network
connectivity problems.

Helpful RCAC programs
^^^^^^^^^^^^^^^^^^^^^

The following two programs can be helpful for you as you
navigate using the clusters. As a note, these are RCAC
specific programs, meaning that we implemented these and
other supercomputers may not have them.

**myquota**

`myquota` is run without any arguments and tells you
where you have access to read and write files. It also
tells you what the space quotas are for each of those
spaces and how much you have used already:

.. code-block::

   $ myquota
   Type     Location   Size    Limit    Use   Files   Limit    Use
   ===============================================================
   home     username  809KB   25.0GB  0.00%       -       -      -
   scratch  cluster    36KB  200.0TB  0.00%      0k  2,000k  0.00%
   depot    example  92.0MB    1.0TB     1%       -       -      -

**flost**

RCAC regularly backs up data in home and depot
spaces, so that if something is accidentally deleted
or overwritten, it can be recovered (if it's been
there sufficiently long). We have daily, weekly, and
monthly snapshots for varying amounts of time. If
you lost something in your scratch space, we don't
have backups of those, so you're out of luck.

.. code-block::

   $ flost
   This script will help you try to recover lost home or group directory contents.
   NB: Scratch directories are not backed up and cannot be recovered.

   Currently anchoring the search under: $HOME
   If your lost files were on a different filesystem, exit now with Ctrl-C and
   rerun flost with a suitable '-w WHERE' argument (or see 'flost -h' for help).

   Please enter the date that you lost your files: 2024-10-01

   ...

Next section\:
:doc:`applications`
