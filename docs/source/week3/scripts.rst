Scripting
=========
:doc:`week3`

In this section there are a couple of main
concepts that we are going to discuss:

* What are shell scripts?
* Where can scripts and other programs be stored?
* Environment variables
* Login profiles
* Program exit status and control flow

What is a shell script?
^^^^^^^^^^^^^^^^^^^^^^^

A shell script is a text file of actions you want
the computer to take. The first line, called the
"shebang" defines what shell interpreter to be
used.

Bash (and other shells) often use whitespace to
delimit arguments to programs. Quotations keep
arguments with spaces together.

Double quotes and single quotes work differently
in bash: double quotes allow expansion of variables
and some other kinds of things, whereas single
quotes are verbatim.

Following is a simple script we will use as an
example in the following sections:

.. code-block::

   #!/bin/bash
   
   echo 'Ooh, so scary!'

Please use your command-line editor to copy this
into a file named `boo.sh`.

Notice the first line is the "shebang" that starts
with a `#!`, followed by the interpreter, wihch in
this case is the bash shell. We then have the `echo`
program, which just prints out the argument(s) to
the console.

A couple of helpful programs that you may want to
use:

.. list-table:: Helpful programs
   :widths: 20 50
   :header-rows: 1

   * - Program
     - Action
   * - `echo`
     - Print statements
   * - `hostname`
     - Name of the server you are logged into
   * - `date`
     - Current date and time (many format options)

There are two ways to execute shell scripts:

* Invoking it with the appropriate shell

.. code-block::

   $ bash boo.sh
   Ooh, so scary!

* Refer to it as a program directly by its path

.. code-block::

   $ ./boo.sh
   -bash: ./boo.sh: Permission denied

Quiz: Why did this happen? What can we do to
check the permissions?

.. admonition:: Permission Hint
   :collapsible: closed
   
   Use the command `ls -l boo.sh` to see the
   permissions::
      
      $ ls –l boo.sh
      -rw-r--r-- 1 username student 22 Oct 11 01:44 boo.sh

.. admonition:: Answer
   :collapsible: closed

   The file `boo.sh` doesn't have the execute
   bit set, so we can't execute it as a program.

   You can change file and directory permissions
   using the `chmod` program::

      $ chmod +x boo.sh
      $ ls –l boo.sh
      -rwxr-xr-x 1 username student 22 Oct 11 01:44 boo.sh
      $ ./boo.sh
      Ooh, so scary!

.. admonition:: `chmod` hint
   
   The `chmod` program takes both relative
   (**[ugo][-+][rwx]**) and absolute (e.g. 700)
   syntax.

Quiz: How would we remove read permissions on a
file for both the file *group* and *others*?

.. admonition:: Answer
   
   `chmod go-r boo.sh`

Now that we have an executable shell script,
we may want to move it to a directory that
contains all our executables, maybe something
named `bin`, like was mentioned earlier.
We also need to make the new directory discoverable
by the shell. We do this using the `PATH` variable,
which we will discuss in a minute. Essentially,
the `PATH` variable controls where the shell looks
for executables.

Also, remember that file extensions are optional
in UNIX.

.. code-block::

   $ mkdir ~/bin
   $ mv boo.sh ~/bin/boo
   $ export PATH=$PATH:~/bin
   $ boo
   Ooh, so scary!

Quiz: Now does it matter if we change directories?
What happens if we close the shell and reopen it?

.. admonition:: Answer
   :collapsible: closed

   It doesn't matter if we change directories, but
   the change to our PATH variable is not kept.

Environment variables
^^^^^^^^^^^^^^^^^^^^^

Environment variables are similar to variables in
other programming languages. But, they also have
some important distinctions that set them apart.

Variables can have a *local* scope (in a script)
or a *global* scope (with child processes).

There are three types of variables:

#. Simple
#. Magic
#. Special

You can also modify software (or shell) behavior
by environment variables.

**Simple variables**

They are like other programming languages.

Simple assignment:

.. code-block::

   x=1

There are no types (mostly everything is text)

.. code-block::

   y=foo

Variables are conventionally uppercase, but
it's not necessary.

.. code-block::

   NAME="some data"

Variables are just dumb text, unlike other
programming languages. While there are some
execptions, there are no complex data
structures.

.. code-block::

   MYDATA=1,2,3

And since it's all just text, there's limited
syntax.

.. code-block::

   OTHER=1+2.3

There is also a weird thing where 0 is true
and 1 is false, which we will discuss later.

.. code-block::

   COND=1

Variables also have a scope. If you define
a variable, it is only visible in local
scope (current script) by default.

.. code-block::

   X=true

To propagate the variable down to child
processes, you need to `export` it.

.. code-block::

   export X

You can also declare the variable and export
it on the same line.

.. code-block::

   export THING=0

You can also declare multiple variables on
one line.

.. code-block::

   export THING=0 DATASET=foo.in

**Magic variables**

There are a couple of "magic" variables which
are not like other programming languages.

.. code-block::
   
   echo $RANDOM

Will always give you a random value, even if
you set it to be something else.

**Special variables**

There are many different special variables
you can use.

.. list-table:: Special variables
   :widths: 15 60
   :header-rows: 1

   * - Variable
     - Meaning
   * - `$0` ... `$99`
     - x-th argument of script/function
   * - `$_`
     - Last argument of script/function
   * - `$@`
     - All arguments of script/function (whitespace)
   * - `$#`
     - Count of arguments of script/function
   * - `$?`
     - Exit status of last process (more on this later)
   * - `$!`
     - Process ID of last process (more on this later)

There are also special software/shell environment
variables that change the behavior of different
things.

.. list-table:: Meaningful variables
   :widths: 20 50
   :header-rows: 1

   * - Variable
     - Meaning
   * - `PATH`
     - Directories containing programs
   * - `MANPATH`
     - Directories containing manual pages
   * - `LD_LIBRARY_PATH`
     - Directories containing shared libraries
   * - `PKG_CONFIG_PATH`
     - Directories containing package configuration

Login profile
^^^^^^^^^^^^^

As we discussed earlier, the `PATH` variable is reset
every time you log into the cluster, or open a new
terminal. What if we wanted to have it be modified
every time we started a new session? There's a
solution for that! It's called a **login profile**
Typically, on Linux you would use the `~/.bashrc`
file, but on the UNIX system we have on the clusters
it's contained in the `~/.bash_profile` hidden file.

There are many things you can put in the login
profile to configure your personal session, but
three that we are going to talk about are:

#. Aliases
#. Variables
#. Modules (bad)

An alias is a verbatim command substitution that
happens on the command line when invoked like a program.
Here's one example:

.. code-block::

   alias rm="rm -i"

Which would make sure that every time you run the
`rm` program, it's run in interactive mode. However,
other programs will not recognize aliases as
commands.

You can also add variables (like `PATH`) to your
login profile.

.. code-block::

   export PATH=$PATH:$HOME/bin

This will add the newly created `bin` folder in your
home directory to the `PATH` variable every time
you create a new session. Note that we have `$PATH`
at the beginning of the assignment, that's so we 
don't only have `$HOME/bin` as our entire `PATH`.
Because that would mean the only place that the
computer looks for programs would be in your
home directory. Which would be bad.

Something that you should **NOT** do is load
modules (especially conda modules) in your
login profile. This can mess up the rest of the
start up process and can cause weird errors.

Exit status
^^^^^^^^^^^

Successful programs should exit with a zero (0).
And any non-zero exit statis should be considered
an error condition. Often, programs will document
the meaning of their different exit status values
in thier manual page.

.. code-block::

   $ boo
   Ooh, so scary!

   $ echo $?
   0

Control flow (if-else conditional statements) in
shell scripts often hinge on the success or
failure of commands. Because of this, 0 means
true and 1 (non-zero) means false in the UNIX
shell. This is opposite of almost everywhere
else.

.. code-block::

   $ true
   $ echo $?
   0

   $ false
   $ echo $?
   1

Next section\:
:doc:`processes`

