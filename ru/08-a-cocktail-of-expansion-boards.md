Попробуйте коктейль проектов
============================

Перед вами - первая статья из серии, призванной помочь пользователям Raspberry Pi собирать платы расширения и полностью использовать возможности ввода-вывода их RPi.

Уровень сложности: продвинутый

Lloyd Seaton

Применимость
------------
В этой статье описаны относительно сложные конструкторские проекты, которые не рекомендуются к изучению новичкам. К ним нет готовых наборов деталей, наоборот, вам придётся покупать их самим в любимых магазинах радиодеталей. Впрочем, список нужных компонентов и возможных магазинов всегда прилагается.

Сила колелктива
---------------

> Печатные платы можно купить дешевле, если вы объединитесь в группы по 2-3 и более человека.

Стоимость электронных компонентов во многом зависит от закупаемого количества. Для этих проектов, участники могут неплохо сэкономить, если объединятся для совместных покупок с единомышленниками в школе, Raspberry Jam или других сообществах.

Печатные платы
--------------
Многие конструкторы избегают разработки и производства печатных плат любой детализации или сложности, - это причина этого цикла статей. Если мы соберём "коктейль" из проектов со множеством дизайнов печатных плат, они могут быть произведены с меньшими затратами, особенно если конструкторы участники проектов объединятся, чтобы разделить расходы.


Стратегия производства печатных плат
------------------------------------
The author became involved in the design and
construction of small printed circuit assemblies
(PCAs) late in 2011 when he decided to develop
an electronic supervisory circuit to manage solar
powered pumps on farms and elsewhere. It soon
became apparent that the cost of PCBs needed
to reduce and a decision was made to use the
Mini Board Pro service of ExpressPCB and to
design the project PCBs such that multiple
(initially 2, later 3) project PCBs could fit on each
of the 3 manufactured PCBs per order. The
project PCBs are designed to be separated by
hacksaw prior to assembly. This strategy
achieved a unit price of about $15 Australian per
project PCB, seemingly a reasonable price.
More recently, the supervisory circuit has been
redesigned (with the help of a Raspberry Pi) to
employ an ATtiny85 microcontroller instead of
the previously pure analogue circuitry such that 6
project PCBs can now be fitted on each
manufactured PCB, halving the PCB unit price
again to about $7.50, a very satisfactory price.

Список проектов
---------------
В течении следующих месяцев планируется осветить следующие проекты на печатных платах:

1. Power I/O
2. Tiny I/O
3. MegaPower
4. Pi Bridge ICSP Interconnect
5. Battery Load Manager 85
6. BatteryLoadManager+

Краткое описание каждого из них приведено ниже.

Power I/O
---------
Включает в себя шестнадцатеричный круговой переключатель, 7 драйверов Дарлингтона со светодиодными индикаторами, и обеспечивать буферизованный доступ к GPIO портам Pi. Дополнительно можно включить импульсный регулятор напряжения, чтобы Pi могла работать от истоичника постоянного напряжения 7-20В.

Tiny I/O
--------
Эта плата может содержать микроконтроллер ATtiny84, 7 драйверов Дарлингтона со светодиодными индикаторами, и будет обеспечивать буферизованный доступ к GPIO.

MegaPower
---------
Эта плата подойдёт тем людям, которые предпочтут микроконтроллер ATmega328 с 4 индикаторными светодиодами, 7 драйверами Дарлингтона и питанием 5В (для совместимости по напряжению) вместо GPIO. Это устройство может работать как в тесной интеграции с Raspberry Pi, так и само по себе. Будучи сопряжённым с RaspberryPi, импусльный источник питания MegaPower может обеспечивать саму MegaPower, и Raspberry Pi от источника постоянного тока 7-20В. Если это не нужно, элементы источника питания можно опустить, тогда микроконтроллер MegaPower будет питаться от Raspbery Pi.

Pi Bridge ICSP Interconnect
---------------------------
Маленькая печатная плата (2x1.5 см) позволяет Raspberry Pi подключиться для программирования Battery Load Manager 85,
BatteryLoadManager+ или некоторых плат Arduino, без использования соединительных проводов..

Battery Load Manager 85
-----------------------
This PCB is for a stand-alone ATtiny85-based
design (1.25" x 1.25") that can be programmed
via the Pi Bridge ICSP Interconnect and a
Raspberry Pi set up with Gordon Henderson's
Arduino IDE procedure and extensions together
with ATtiny support. Circuitry is included for
monitoring of battery voltage (+ optionally
another voltage) and switching of a high current
load according to user policy programmed via an
Arduino sketch. There are 3 indicator LEDs. An
example Arduino sketch will be available for use
as a solar pump supervisor.

BatteryLoadManager+
-------------------
This PCB is for a stand-alone ATtiny84-based
design (1.9" x 1.25") that is otherwise similar to
the Battery Load Manager 85 but has additional
capabilities. Surplus ATtiny84 I/O pins are
brought out to a header for increased flexibility of
application.

The Cocktails - What's In The Mix?
----------------------------------
One size rarely fits all. To try to assist in the
most cost-effective purchase of PCBs it is
intended that there will be a choice of "cocktail
files" available so that each constructor or group
of constructors can choose to use the cocktail
files that most closely match their interest in the
various project PCBs. For example, if a
constructor has little interest in stand-alone
ATtiny PCBs, it would not be cost-effective to
choose a cocktail file that commits significant
space/cost to the Battery Load Manager projects.
Pictured below is an ExpressPCB Mini Board Pro
delivery. Each of the 3 manufactured PCBs is
2.5" x 3.8" and is allowed to have no more than
350 holes. These manufactured PCBs will each
yield a pair of different project PCBs which
happen to be equal in size; one for the Tiny I/O
project, the other for MegaPower. There are
guide lines to assist with the hacksawing.

Procedure For Ordering PCBs
---------------------------
Before ordering PCBs it is necessary that you
first install the ExpressPCB free CAD software on
a PC with Internet access. The
ExpressPCB.com software is a free download
from the Internet and is intended for Windows
machines but the author uses it with Wine on a
Linux PC quite satisfactorily. You then need to
choose your cocktail file, download the file and
open it with the ExpressPCB software. Placing
the order is then a straight-forward procedure
whose full description can be found on the
information blog.

Информационный блог
-------------------
В Интернете открылся блог picocktails.blogspot.com, где вы можете почерпнуть актуальную информацию для конструкторов представленных проектов: ссылки на чертежи, файлы, фотоальбомы, тестовые программы и многое другое.
