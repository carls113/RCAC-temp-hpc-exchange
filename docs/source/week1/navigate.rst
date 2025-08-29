Navigate the file system
========================
:doc:`week1`

**Previous section:**
:doc:`anatomy`

Now that we have learned about the shell and the anatomy of a
command, let's dive into different helpful programs that you
typically encounter while working on UNIX systems.

.. _pwd:

pwd
^^^

`pwd` is a program that is occasionally helpful, but is a
good way to get your feet wet in trying out different UNIX
programs. All it does is print out what directory you are
currently in. Usually you will already know where you're at
from the prompt, but it can be helpful to know the full path.
`pwd` stands for *print working directory*::

   $ pwd
   /home/username

.. _ls:

ls
^^

`ls` is one of the most common UNIX programs that you may use
while on UNIX systems. By default, it lists the contents of
your current working directory::

   $ ls
   example-data		data.bak

There are many options that `ls` can use to modify its
behavior. In addition, you can specify a directory as an
argument to list contents of that directory.

Paths
^^^^^

All files in UNIX systems are organized into directories, and
those directories may have subdirectories, creating a tree
of files that span the entire operating system. Directory paths
in UNIX systems can be *absolute* or *relative*. Absolute paths
start at the root of the file system and thus always start with
a slash (\/). Relative paths are relative to your current working
directory and do not start with a slash.

Abolute path

.. code-block:: bash

   $ ls /home/username/example-data
   output_of_ls

Relative path

.. code-block:: bash

   $ ls example-data
   output_of_ls

File extensions
^^^^^^^^^^^^^^^

In UNIX systems, file extensions are technically meaningless,
however, convention is important. Executables don't need to
end in **.exe** and text files don't need to end in **.txt**.
It is helpful if they do, so that people (including yourself)
know what type of file it is.

.. _cd:

cd
^^

Another one of the most common programs to run on UNIX systems
is `cd`, which stands for *change directory*, but should be
thought of as *change working directory*. This will change
which directory you are currently working in.

.. code-block:: bash

   $ pwd
   /home/username
   $ cd example-data
   $ pwd
   /home/username/example-data

.. important::

   Paths as the argument of the `cd` program can be absolute
   or relative.

.. tip::

   Run **cd -** to navigate to the previous directory you were in.
   Run `cd` without any arguments to navigate back to your home
   directory.

Special paths
^^^^^^^^^^^^^

There are three special paths that are commonly used in navigating
UNIX file systems:

* \~
* \.
* \.\.

The tilde (~), as mentioned earlier refers to your home directory.
The single period (.) refers to the current working directory, which
can sometimes be helpful, but it is more rare. Lastly, the double dot
(..) refers to the parent directory of the directory you are in.

.. code-block:: bash

   $ pwd
   /home/username/example-data
   $ cd ..
   $ pwd
   /home/username

Permissions
^^^^^^^^^^^

Since everything in UNIX systems is a file, file permissions are
of supreme importance. To check the permissions of a file, run
the command `ls -l` which runs the `ls` program with the `l` option
which tells `ls` to print out more information about the files.

The following code block shows an example of what you might see from
the longer `ls` output:

.. code-block:: bash

   $ ls -a -l -h ~/.ssh
   total 6.0K
   drwxr-xr-x  2 username student    4 Jul 17 11:19 .
   drwx------ 14 username student   28 Jul 16 22:26 ..
   -rw-r--r--  1 username student 2.0K Oct 18  2021 authorized_keys
   -rw-r--r--  1 username student    0 Jul 17 11:19 config

The three program options used here are: `a`, which displays all files/folders,
even hidden ones; `l` which lists out more information about each listing;
and `h` which shows the size of items in a human-readable format. In the
first ten columns of the output are the permissions of that item,
details of which will be discussed in the next paragraph. The next number
is the number of hardlinks to the file, which for most use cases isn't
important. After that, we have the username of the owner of the file and
then the name of the group that owns access to the file. The number
after that is the size of the item. Then comes the date that the item
was most recently modified. Last of all is the name of the actual item.
The four items in the `.ssh` folder of this home directory are: the
current directory (.), a link to the parent directory (..), and two
files (`authorized_keys` and `config`).

Let's talk permissions. The first ten characters of each row
represent the permissions for each item listed. The first column
shows whether it's a directory (with a `d`) or not (with a dash).
Then the next nine columns are broken into three groups of three.
The first group of three is the permissions belonging to the
user that owns (`u`) the file. The next group of three is the permissions
for the group that owns the access to the file (`g`). And the last
three are the permissions for other users (`o`). Each group of
three is made up of the read (`r`), write (`w`), and execute (`x`)
bits. The read bit controls whether someone can look at the
data contained in the file. The write bit controls whether someone
can edit the data in the file. And the execute bit controls if
someone can run that file as a program. We'll talk more about the
execute bit in the :doc:`../week3/week3` section :doc:`../week3/scripts`.

For directories, the permission bits mean slightly different things.
The write bit is the same, it controls whether someone can modify
(e.g. delete) is. The execute bit controls whether someone can see
what's inside the directory. The read bit controls whether
someone can go into that folder.

.. _man:

man
^^^

Another helpful program is `man`, which stands for *manual*. You may
hear the term *man page* which is just short for *manual page*, or
running the program `man` with the argument being the program you
want more information about.

.. code-block::

   $ man ls

The `man` program pulls up a page that you can scroll up and down
with your arrow keys or the `j`/`k` keys and quit out of by
pressing the `q` key. It is up to each program to provide its
own `man` page, so not all programs have them, but when they do
it can be helpful.

As a quiz, who can find (using the `ls` `man` page) what are the
options necessary to list items by reverse chronological order
(older items listed first).

.. admonition:: Answer
   :collapsible: closed

   The command would be `$ ls -t -r`

.. _mkdir:

mkdir
^^^^^

The last program we'll go over in this section is the `mkdir`
or *make directory*. This does what it sounds like and will
create the directory noted in the argument if it doesn't already
exist.

.. code-block::

   $ mkdir ~/example-data

You can also put multiple arguments and `mkdir` will
create all of them. A helpful option to pass to the `mkdir`
program is `-p`, which will create parent directories as needed.

.. code-block::

   $ mkdir -p ~/another_one/test1

Which will create the `another_one` directory in your home
directory and the `test1` directory within the `another_one`
directory.


Next section\:
:doc:`edit`
