Managing output with pipes
==========================
:doc:`week3`

Previous section\:
:doc:`processes`

When dealing with processes, the output
of those processes is important. In this
section, we will discuss how to manage
the output of processes in different ways.

The main concepts that we will go over are:

* File descriptors and standard input/output
* Input/output redirection
* Special files
* Command pipelining
* Useful commands for processing output

Programs often write output to the console,
this is called *standard output*. The shell
defines `stdin`, `stdout`, and `stderr` for
every program.

File descriptors are integers that are used
by processes to attach to open files.

.. list-table:: Standard file descriptors
   :widths: 10 20 50
   :header-rows: 1

   * - \#
     - Common name
     - Description
   * - `0`
     - `stdin`
     - Attached to programs for input data
   * - `1`
     - `stdout`
     - Attached to programs for output data
   * - `2`
     - `stderr`
     - Attached as a secondary output for errors

You can also redirect these file descriptors
to attach to files other than the console.

.. list-table:: Redirection notation
   :widths: 20 50
   :header-rows: 1

   * - Notation
     - Meaning
   * - `<   FILE`
     - Read `stdin` from `FILE`
   * - `>   FILE`
     - Write `stdout` to `FILE`
   * - `>>  FILE`
     - Append `stdout` to `FILE`
   * - `2>  FILE`
     - Write `stderr` to `FILE`
   * - `2>> FILE`
     - Append `stderr` to `FILE`
   * - `|   PROGRAM`
     - Join `stdout` to `stdin` of `PROGRAM`

The following example redirects "Ooh, so scary!"
from the console into the file `message.txt`.

.. code-block::

   $ boo > message.txt
   
You can concatenate (print) the contents of
files with the `cat` program

.. code-block::
   
   $ cat message.txt
   Ooh, so scary!

To reduce output, you can count the number of
words, lines, chars, etc. with the `wc` program.

.. code-block::

   $ cat message.txt | wc --chars
   15

.. list-table:: Useful programs
   :widths: 15 50
   :header-rows: 1

   * - Program
     - Meaning
   * - `cat`
     - Concatenate (or slice) one or more files
   * - `head/tail`
     - Limit output to first/last `N` lines (default 10)
   * - `less/more`
     - Page output interactively (for ease of viewing)
   * - `cut`
     - Slice columns from input by delimiter
   * - `sort/uniq`
     - Reorder and filter input
   * - `grep/sed`
     - Filter/modify input based on regular expression
   * - `awk`
     - Streaming programming language (superpowers)
   * - `wc`
     - Reduce output by counting (words, lines, etc)
   * - `xargs`
     - Transpose output as positional arguments to next program

Next section\:
:doc:`managing_files`

