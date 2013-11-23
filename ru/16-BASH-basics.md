Основы BASH
===========

W. H. Bell
MagPi Writer

Сложность: для начинающих

Bash (Bourne Again Shell) вот уже на протяжении последних лет является оболочкой по умолчанию в Linux. Цель этой серии статей - дать обзор Bash, описания синтаксиса и встроенных функций. Bash - отличный инструмент для объединения нескольких программ с минимальными усилиями, - и поэтому цикл статей так и назыается, "Синяя изолента Bash".

По мере чтения статей вам может понадобиться обратиться к справке Bash, чтоб открыть её, наберите `man bash`. Команды поиска по справке доступны в самой программе `man`, другие можно найти в руководстве по `less`. Некоторые команды Bash уже рассматривались нами в серии Command Line Clinic 2-5 выпусков журнала.


Запуск Bash
-----------

Интерпретатор команд запускается при открытии окна терминала. Узнать оболочку по-умолчанию для текущего пользователя можно командой

    echo $SHELL

Она задаётся для каждого пользователя отдельно в файле `/etc/passwd` или через NIS или LDAP. Например:%

    grep pi/etc/passwd

напечатает

    pi:x:1000:1000:,,,:/home/pi:/bin/bash

Скрипты можно запускать набиря команды непосредственно в окне терминала, и с помощью сценария в текстовом файле. Такой сценарий можно выполнять двумя способами: включая скрипт

    source script.sh

это будет то же самое что и 

    . script.sh

или выполняя скрипт

    ./script.sh

Когда файл включается, всё работает так как если бы вы набирали строчки из скрипта вручную в терминале. Любая переменная, объявленная в скрипте, будет доступна после его завершения. Самому скрипту также доступны все переменные, объявленные до него. В противоположность этому, исполнение скрипта происходит в новой сессии, т.е. по завершении все локальные переменные очищаются.

Чтобы выполнить скрипт, необходимо указать путь к интерпретатору Bash в начале файа:

    #!/bin/bash

Этот файл должен иметь атрибут исполнимости:

    chmod u+x script.sh

Теперь можно набрать `./script.sh` чтобы запустить скрипт.

Используйте `nano` (описан в выпуске 3 C cave article), чтобы создать файл `hello.sh`:

    #!/bin/bash
    # A simple script to print a string.
    echo "In the beginning.."

Затем сделайте его исполнимым и запустите. Команда `echo` выводит строку на стандартное устройство вывода. Строки, сценария, начинающиеся с `#` являются комментариями. Комментарии можно писать с новой строки, или в конце строки с командой.


(Канальный оператор)!?!?!?!?
------------------

A series of commands can be chained together using the
pipe "|" operator. A pipe has the effect of passing the
standard out from one command to the standard in of
another command. This is especially useful when handling
strings,


    # Print "Hello Joe" , replace Joe with Fred.
    echo "Hello Joe" | sed 's/Joe/Fred/g'
    # Replace Hello with Goodbye too.
    echo "Hello Joe" | sed 's/Joe/Fred/g' | sed
    's/Hello/Goodbye/g'

In this example the sed command is used to replace a part
of the string. The sed command (stream editor) is a
program in its own right and has a separate manual page.


Redirection
-----------

The standard output from a program can be directed to a
file or a device,

    # Print a string to a fi le
    echo "This is a file" > file.txt
    # Print the contents of the file on the screen
    cat file.txt

The operator ">" truncates the file and then appends the
standard output to the file. To append to a file without
truncation, the ">>" operator should be used.

If a command produces a lot of output which is not needed,
the output can be sent to `/dev/null` instead:

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


| Syntax           | Meaning    
|------------------|-------------
| -z *string*      | True if length of *string* is zero
| -n *string*      | True if the length of *string* is non-zero
| *var1* == *var2* | True if equal
| *var1* != *var2* | True if not equal


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

fills the variable `dir_list` with the text returned by the `ls`
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
