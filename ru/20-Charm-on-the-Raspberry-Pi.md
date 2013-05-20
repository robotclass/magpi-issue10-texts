Введение в CHARM

Charm на Raspberry Pi
=====================

Peter Nowosad, Guest Writer

Уровень сложности: Продвинутый

Эта и последующие статьи призваны повысить заинтересованность и понимание языка Charm на Raspberry Pi. Как создатель этого языка, это цель которую я преследую, в частности, среди молодого поколения пользователей Raspberry Pi, а также пользователей, которые стремятся узнать немного больше о таинственном мире программирования.

Инструментарий Charm нетребователен к ресурсам, но в то же время мощный и предоставляет гибкую среду разработки. Приложения любой сложности могут быть разработаны малыми изменениями без ожидания компиляции кода, например, весь пакет Charm можно пересобрать менее чем за одну минуту!

Сейчас мне бы хотелось рассказать о частных аспектах использования Charm. Я надеюсь, что это поможет людям начать писать программы на Charm и присоединиться к сообществу более чем 1200 людей со всего мира, которые уже посетили веб-сайт Charm.


Установка
---------
Charm версии 2.6.1 уже поставляется в каталоге Programming выпуска RC6 операционной системы Risc OS для Raspberry Pi. Тем не менее, я бы рекомендовал вам обновиться до последней версии (на сегодня это 2.6.4), которая распространяется свободно по условиям лицензии Gnu Public License в виде простого файла zip через сайт charm.qu-bit.co.uk. На этом сайте вы можете получить более полную информацию, а также связаться со мной через форум Charm, посмотреть скриншоты различных демонстрационных программ на Charm, самая интересная из которых Decapedees, стрелялка похожая на Pacman с приятной графикой и музыкой.

Вам также может понравиться использовать векторные операции с плавающей точкой (VFP, vector floating point) ARM11, которые выключены по-умолчанию для тех, кто запускает Charm на эмуляторах и ранних версиях процессоров ARM. Вам нужно будет включить VFP в оболочке Charm и пересобрать дистрибутив, инструкции можно найти на сайте. После этого, вы получите, например, на порядок более высокую производительность в программе Mandelbrot explorer, т.к. эмулятор вычислений с плавающей точкой (FPE, floating point emulator) будет заменён родными инструкциями VFP сопроцессора.


Инструментарий
--------------
Набор инстурментов Charm для Risc OS содержит следующие основные приложения:

* **!Charm** - Оболочка для выполнения инструментария. Поддерживает перетаскивание мышью для файлов и папок, журналирование команд и отчёты об ошибках.
* **edit** - Редактор общего назначения, используется для написания и разработки кода Charm (перетащите файлы или папки, которые нужно редактировать, на иконку Charm с нажатой клавишей Shift)
* **armc** - Компилятор, создающий объектный файл в бинарном формате из исходного кода на языке Charm.
* **arma** - Ассемблер, создающий объектный файл в бинарном формате из исходного кода на языке ARMassembly.
* **arml** - Линковщик, собирающий объектные файлы Charm в исполнимый файл Risc OS или модуль.


Новые проекты
-------------
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


Модульное программирование
--------------------------

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


Начало работы
-------------

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


Практическая задача
-------------------
Если вы готовы к новому испытанию, я предлагаю заменить модуль по-умолчанию `!NewProject myProject` на следующий код, выводящий первую дюжину факториальных чисел:

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

Этот код показывает пример использования рекурсии для вычисления каждого нового значения через умножение, учитывая аксиому `1! = 1`.

Наконец, как упражнение, попробуйте изменить программу так, чтобы она вычисляла первые 20 чисел без ограничений на значение числа в 32-битном представлении (подсказка: возвращайте вещественное число из `factorial` и используйте `.float` вместо `.num` для его вывода).


Что дальше?
-----------
В следующий раз я расскажу про типы данных Charm, переменные, строки и области видимости.
