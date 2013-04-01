1 - BASH basics
===============

W. H. Bell
MagPi Writer

DIFFICULTY: BEGINNER



Bash (Bourne Again Shell) has been the default LINUX
shell for several years. The aim of this series is to give an
overview of the Bash shell, providing a description of the
syntax and built in functions. Bash is great for lashing
together several different programs with minimal overhead.
For this reason, this of series of articles is called "Bash
Gaffer Tape".

When reading these articles, it may prove useful to consult
the Bash manual page by typing man bash. The search
commands available within man and other commands can
be found in the less manual page. Some Bash commands
have already been discussed in the Command Line Clinic
series in Issues 2 to 5.


Running Bash
------------

When a terminal window is opened a shell interpreter is
started. The default shell for the current user can be
printed by typing,

    echo $SHELL

The default shell is set for each user within /etc/passwd or
via NIS or LDAP. For example,

    grep pi/etc/passwd

returns

    pi:x:1000:1000:,,,:/home/pi:/bin/bash

Scripts can be run by typing the commands directly into a
terminal window or by using a shell script text file. A shell
script file can be run in two ways: by sourcing the script

    source script.sh

which is equivalent to

    . script.sh

or by executing the script,

    ./script.sh

When a file is sourced, it is as if the file was typed into the
current shell. Any variables which are declared in the
script remain set when the script finishes. The script also
has access to all of the variables declared in the current
shell. In contrast, when a script is executed a new bash
interpreter session is started. At the end of the bash
session any local variables are cleaned up.

To execute a script, the path to the Bash interpreter should
be given at the top of the file:

    #!/bin/bash

Then the file should be made executable

    chmod u+x script.sh

Finally, it is possible to type ./script.sh to execute the script.

Use `nano` (documented in the issue 3 C cave article) to
create a hello.sh file containing:

    #!/bin/bash
    # A simple script to print a string.
    echo "In the beginning.."

Then make the file executable and execute the script. The
echo command prints the string on the screen using the
standard out. Strings starting with "#" are comments.
Comments can be added on a separate line or at the end
of a line.


Pipe operator
-------------

A series of commands can be chained together using the
pipe "|" operator. A pipe has the effect of passing the
standard out from one command to the standard in of
another command. This is especially useful when handling
strings,


    # Print "Hello Joe" , replace Joe with Fred.
    echo "Hello Joe" | sed ' s/Joe/Fred/g'
    # Replace Hello with Goodbye too.
    echo "Hello Joe" | sed ' s/Joe/Fred/g' | sed
    ' s/Hello/Goodbye/g'

In this example the sed command is used to replace a part
of the string. The sed command (stream editor) is a
program in its own right and has a separate manual page.


Redirection
-----------

The standard output from a program can be directed to a
file or a device,

    # Print a string to a fi le
    echo "This is a fi le" > file.txt
    # Print the contents of the file on the screen
    cat file.txt

The operator ">" truncates the file and then appends the
standard output to the file. To append to a file without
truncation, the ">>" operator should be used.

If a command produces a lot of output which is not needed,
the output can be sent to /dev/null instead:

    # Run a command, but throw away the output
    rm /tmp &> /dev/null # This command will fail.

A file can be used as the standard input of a command by
using "<". This will be discussed later in the context of
loops.


Variables
---------

A variable is defined by assigning it a value,

    myName="JohnDoe"


Bash is very sensitive to the use of white spaces. For the
declaration to be interpreted correctly there must not be
any spaces between the variable name and the equals
sign or the equals sign and the value.

Once a variable has been defined, it is used by prepending
the name with a dollar sign,

    echo $myName

Variables which are defined in one shell are not available in
a sub-shell unless they are exported,

    export myName="JohnDoe"

where the variable can be exported when it is declared or
afterwards.


if-else conditions
------------------

Logic conditions are inclosed in "[[]]" parentheses. The
status of a variable can be tested using a logic condition,

    #!/bin/bash
    if [[ -z $myName ]]; then
      echo "myName is not defined"
    else
      echo "myName is defined as \"$myName\""
    fi

In this case the first condition is true if the variable is not
set. At least one white space must separate the pieces of
the logic condition. Save this script, change its
permissions and execute it. Then try

    export myName=$HOSTNAME

and run the program again. Then type

    unset myName

and run the program again. The unset command removes
the variable myName. Bash also provides else-if
statements:

    if [[ $var == 1 ]]; then
      cat /proc/cpu # Check the CPU type
    elif [[ $var == 2 ]]; then
      cat /proc/meminfo # Memory information
    else
      date -I # The current date
    fi

A summary table of logic tests which can be applied to a
variable are given below,

Syntax Meaning
-z string True if length of *string* is zero
-n string True if the length of *string* is non-zero
var1 == var2 True if equal
var1 != var2 True if not equal
(ilya: table todo)


The logic comparisons of equal and not equal can also be
used with wildcard syntax, to check if a sub-string is found
in another string.

    #!/bin/bash
    str1="Raspberry"
    str2="berry"
    if [[ $str1 == "$str2"* ]]; then
      echo "$str1 begins with $str2"
    fi
    if [[ $str1 == *"$str2" ]]; then
      echo "$str1 ends with $str2"
    fi
    if [[ $str1 == *"$str2"* ]]; then
      echo "$str1 contains $str2"
    fi


The 'for' loop
--------------

Bash provides many familiar loop structures. The for loop
is most commonly used with input files or variables,

    #! /bin/bash
    # A string with values and spaces
    list="a b c"
    # print each character in the 'list' variable.
    for l in $list; do
      echo $l
    done

This can also be written on one line as

    list="a b c"; for l in $list; do echo $l; done

where several of the newline characters in the script file
are replaced with semi-colons. The variable list can be
replaced with an input file,

    #!/bin/bash
    # Fill a file with some strings
    echo "Apple Orange" > list # Truncate, append
    echo "Pear" >> list # Append
    # print each word in the input file.
    for l in $(<list); do
      echo $l
    done

This example uses a redirection from an input file to read
each word. Bash separates the words using the space or
the new line character.

For loops also support C-like iteration,

    #! /bin/bash
    # Print all numbers from 1 to 10
    for (( i=1; i<=10; i++ )); do
      echo $i
    done

Notice that the variable i is not prefixed by a dollar sign
within the (()) parentheses of the for loop. This is an
exception for this type of for loop.


Evaluating commands
-------------------

A command can be evaluated by writing it within $(). For
example,

    dir_list=$(ls )

fills the variable dir_list with the text returned by the ls
command. The syntax $() can be directly used as a
variable. This can be useful within a for loop,

    #!/bin/bash
    for file in $(ls *. txt); do
      gzip $file
    done

where this example gzips all of the text files in the present
working directory.

Command evaluations can be nested,

    touch /tmp/t1 # Create empty
    $(basename $(ls /tmp/t1)) # File name only

and can include variables,

    file=/tmp/t1
    $(basename $(ls $file))

Each of the commands can include pipe operations,

    files_to_gzip=$(ls * | grep -v .gz)

where this command excludes file names which include
".gz" from the variable. The pipe operator "|" passes the
standard output from one command to the standard input
of another command.


Challenge problem
-----------------

Write a program to gzip all of the files in the present
working directory. The program should not gzip files which
have the .gz ending. The solution to the problem will be
given in the next tutorial.
