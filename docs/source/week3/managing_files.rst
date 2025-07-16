Managing files and directories
==============================
:doc:`week3`

**Previous section:**
:doc:`pipes`

Now that we can create files from the
output of processes, we should learn
some file management techniques. In
this section, we will go over three
main topics surrounding file
management:

#. Archive formats for managing files and data
#. Data compression programs
#. Finding files

To archive a file or directory means to
bundle those files up into a single archive
file.

The `zip` and `tar` programs can create
archive files of whole directories.
They differ in that `tar` compresses the
whole output together instead of
individually compressing files within the
archive.

.. code-block::

   $ tar -cvzf example-data.tar.gz example-data/

In the example above, we supplied four options
to the `tar` program and two arguments. The
options are, in order, compress, verbose, with
gzip, to a file named the following. The first
argument is actually an extension of the `f`
option, which tells `tar` what we want to name 
the archive. The second (and following)
argument(s) tells `tar` what we want to archive.
To undo the archiving, simple swap the `c`
option for the `x` option (which stands for
extract).

.. hint::

   `tar` stands for "Tape ARchive" format,
   because tape archives do better with one
   big file instead of many, smaller files.

The `zip` program is simpler to use, but
often people prefer `tar` over `zip`.

.. code-block::

   $ zip archive-name.zip example-data/
   $ unzip archive-name.zip

.. note::

   As mentioned in earlier sections,
   file name extensions are usually
   meaningless in UNIX systems. However
   it is common to use `.tar.gz` to
   denote gzip-compressed tar archive
   files and `.zip` to denote archive
   files made with the `zip` program.

Compressing a file is to take it and make
it smaller, but unreadable (by humans).
There are three main programs used to
compress individual files and streams
and they all have nearly identical
usage and behavior. The three are:

* `gzip`
* `bzip2`
* `xz`

.. code-block::

   $ gzip *.txt
   $ cat *.gz | gunzip
   ...

Compressed data from these programs can be
stacked safely and decompressed in
concatenated form.

Quiz: What levels of compression are available
for these programs? Fo these levels mean the
same thing across all three?

.. admonition:: Answer
   :collapsible: closed

   `gzip`: options 1-9, 1 being fastest with
   the least compressiong, 9 being slowest with
   most optimal compression.

   `bzip2`: options 1-9, coose the block size
   of the file when compressing, 1 being 100 k
   and 9 being 900 k.

   `xz`: options 0-9, 0-3 being somewhat fast
   presets, 4-6 offering good to very good
   compression while keeping memory requirements
   down, 7-9 have higher compressor and
   decompressor memory requirements and are only
   useful when compressing larger files (> 16 MiB).

When you have a lot of files, it can be useful
to have a program to find files that match a
pattern. To do this, use the `find` program.

.. code-block::

   $ find ~ -type f -name "*.txt" | grep "example-data"
   ~/example-data/paper.txt

In the exmaple, we used some arguments and some
options, but these are special options that the
`find` program calls "primaries" (which is why
they don't have two leading dashes even though
they are longer than one letter). The first
argument that we provided was the folder we
want to search in, in this case our home
directory. The `type` primary narrows down
what kind of listing the command will find.
Putting `f` here narrows the search to just
find regular files. The `name` primary
filters the search to only list the files
and such that follow the pattern given, in
this case, everything that ends in `.txt`.
Finally, we pipe all the output to the `grep`
program that filters output to only lines
containing the given argument (here
`example-data`).

This `find` program is a great way to initialize
a pipeline for these kinds of management tasks.

.. list-table:: Program reference
   :widths: 20 50
   :header-rows: 1

   * - Program
     - Action
   * - `zip/unzip`
     - Create/extract/update archive of files
   * - `tar`
     - Create/extract "tape" archive of files
   * - `gzip/gunzip`
     - Gzip (zlib) compression (most common)
   * - `bzip2/bunzip2`
     - Bzip compression
   * - `xz/unxz`
     - XZ (LZMA) compression
   * - `find`
     - Search for files

Next section\:
:doc:`../week4/week4`

