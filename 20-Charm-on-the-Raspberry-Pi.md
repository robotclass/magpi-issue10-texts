An Intoduction to
CHARM

Charm on the Raspberry Pi
=========================

Peter Nowosad
Guest Writer

DIFFICULTY : ADVANCED

This and follow on articles are intended to
promote interest in and understanding of the
Charm language on the Raspberry Pi. As the
author of the language, this is a goal I am keen to
encourage, particularly among the younger
generation of Raspberry Pi owners and users
who are looking to learn a little about the
mysterious world of programming.

The Charm tools are light on resource yet
powerful and highly suited to an agile
development environment. Applications of any
complexity can be rapidly developed in small
incremental changes without waiting around for
the code to build; for instance the whole of the
Charm distribution can be rebuilt in well under a minute!

Here I would like to cover the practical aspects
of using Charm. I hope this will encourage
people to take up writing Charm programs for
themselves and join the over 1,200 people from
around the world that have visited the Charm
web site so far.


Installation
------------

Charm version 2.6.1 is already bundled in the
Programming folder of the RC6 release of Risc
OS for the Raspberry Pi. I would however
recommend updating to the latest version
(currently 2.6.4) which is freely distributed under
the Gnu Public License as a simple zip file from
the Charm website charm.qu-bit.co.uk. There
you can read more, get in touch with me through
the Charm forum and view screenshots of the various
Charm demos, of which the most fun is Decapedes,
a shoot'em up somewhat akin to Pacman with
some nice sound and graphics.

You may optionally wish to utilise the vector floating point
(VFP) capabilities of the ARM 11 chip which is
off by default for people running Charm on
emulators or older variants of the ARM chip. This
will involve enabling the VFP option in the Charm
shell and re-building the distribution as described
on the web site. Doing this will for instance speed
up the included Mandelbrot explorer program by
an order of magnitude by replacing floating point
emulator (FPE) instuctions with native VFP
coprocessor instructions.


Tools
-----

The set of Charm tools for Risc OS contains the
following principal applications:

* **!Charm** - A desktop shell application for
running the tool set. The shell supports drag and
drop for files and folders, command logging and
error reporting.
* **edit** - A general purpose editor that is useful
forwriting and developing Charm source code
(drag files or folders to edit on to the Charm shell
icon with the shift key pressed).
* **armc** - A compiler that generates an object file
in binary form from a source code file written in
the Charm programming language.
* **arma** - An assembler that generates an object
file in binary form from a source code file written
in ARMassembly language.
* **arml** - A linker that combines Charm object
files into an executable Risc OS application or
module.


New Projects
------------

Creating new template projects in Charm is easy
using the !NewProject utility application. Simply
select New Project from the menu, name the
project as you wish (default is MyProject) and
drag the folder icon to the folder in which you
wish it to reside. You can then build the project
by dragging the project folder on to the Charm
icon. You should then see the following in the
Charm log:


Required project folders are created
automatically, namely:

* **src** - Charm language source files
* **obj** - object files created by the compiler or
assembler

In case you don't want to inline ARM assembler
inside Charm source you can create the
additional folder:

* **arm** - Arm assembler source files

Applications that live inside an application folder
are usually linked directly to the correct
!RunImage location from inside the project 'build
file using the program command.


Modular Programming
-------------------

The concept of modules is key to an
understanding of Charm (N.B. in this context
Charm modules are different from Risc OS
modules!). Each module is introduced with the
keyword module, and each source file that
Charm compiles contains a single module
definition. It may however import exported
declarations from any number of other modules
on which it is dependent.

References are usually to other modules in the
containing project and to run time library (RTL)
modules that provide essential functions such as
managing windows, files, the keyboard and
screen. The latter are further documented on the
Charm website. The order in which modules are
compiled is determined by the project 'build file
which also specifies the name and location of the
linked application or module.


Getting Started
---------------

So now for the first few snippets of the Charm
language. Each project must contain one and
only one module in which a ~start procedure with
one of two specific signatures is exported.

If no command line parameters are required, the
~start procedures is defined like this:

    module Main
    {
      export proc ~start() {...startup code }
    }

Hence, the classic hello world program in Charm
which uses the vdu stream of the Out run time
library module can be coded in file `src.hello` as:

    import lib.Out;
    module Hello {
      export proc ~start () {
        Out.vdu.str ("Hello World!\n");
      }
    }

though you will need the project 'build file to
contain:

    module hello
    program hello

in order to build the program before you can run
it (the linker will find the Out library automatically
for you).


A Practical Project
-------------------

If you are up for a challenge, I suggest replacing
the default !NewProject MyProject module with
the following code to output the first dozen
factorial numbers:

    import lib.Out;
    module MyProject
    {
      proc factorial (int n) int
      {
        if n <= 1 return 1;
        return n * factorial (n - 1);
      }
      export proc start (ref array ref array char argv)
      {
      for int i := 1 step inc (i) while i <= 12
        Out.vdu.num_fld (i, 2).str ("! = ").num(factorial (i )).nl ();
      }
    }

This code illustrates the use of recursion to
calculate each value from the previous value via
multiplication while utilising the axiom 1!=1.

Finally as an exercise, try changing the program
so that the first 20 factorials can be calculated
without running into the 32-bit limitation on
integer size (Hint: return a real from a factorial
and use .float instead of .num to output it).


Next Time
---------

Next time I intend to cover Charm data types,
variables, strings and scoping.