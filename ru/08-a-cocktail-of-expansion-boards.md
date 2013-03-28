TRY A COCKTAIL OF PROJECTS
==========================

This is the first of a series of articles intended to assistRaspberry
Pi users to construct and use expanded I/O capabilities of theirPi.

DIFFICULTY : ADVANCED

Lloyd Seaton

Suitability
-----------
These are relatively advanced constructional
projects and are not recommended for beginners.
There are no kits of parts being offered. Instead,
participants will need to purchase their own
components from their preferred electronic
component suppliers. However, lists of
components and suggested suppliers will be
provided.

> PCBs can be purchased more economically...
> if participants combine into
> groups of 2 or 3 (or more)

The Power Of Groups
-------------------
The economics of component purchasing are
greatly influenced by the quantities involved. For
these projects, participants should be able to
achieve substantial savings if they can band
together with like-minded individuals through
school, Raspberry Jam or other organisations to
combine their purchases.

Printed Circuit Boards (PCBs)
-----------------------------
Most constructors shrink from the challenge of
designing and producing a circuit board with any
fineness of detail or complexity. That is the
reason for this series of articles. By grouping
together a "cocktail" of projects with a variety of
PCB designs, the PCBs can be purchased more
economically, especially if participants combine
into groups of 2 or 3 (or more) to arrange their
purchases.

PCB Manufacturing Strategy
--------------------------
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

The Projects
------------
During the coming months it is intended that the
following PCB projects will be covered:
1. Power I/O
2. Tiny I/O
3. MegaPower
4. Pi Bridge ICSP Interconnect
5. Battery Load Manager 85
6. BatteryLoadManager+
Brief descriptions of each project follow.

Power I/O
---------
This PCB can include a hexadecimal rotary
switch, 7 Darlington drivers with indicator LEDs
and buffered access to the Pi's GPIO ports. A
switching regulator circuit can optionally be
included to allow the Pi to operate from a DC
source of 7 to 20V.

Tiny I/O
--------
This PCB can include an ATtiny84
microcontroller, 7 Darlington drivers with
indicator LEDs and buffered GPIO access.

MegaPower
---------
This PCB is for people who are not interested in
using the Raspberry Pi's GPIO, preferring
instead to have an ATmega328 microcontroller
with 4 indicator LEDs, 7 Darlington drivers and
operating at 5V for broad interfacing capability.
The board can serve as a close companion to a
Raspberry Pi or operate on its own. When
coupled with a Raspberry Pi, the MegaPower's
on-board switching regulator circuit can power
itself and the Pi from a 7 ~ 20V DC source. If not
required, the switching power supply
components can be omitted, leaving the
ATmega328 to rely on the Pi's regulated power
supplies.

Pi Bridge ICSP Interconnect
---------------------------
This little PCB (0.8" x 0.6") provides a convenient
way to connect the Raspberry Pi for
programming of the Battery Load Manager 85,
BatteryLoadManager+ or some Arduino boards
without the use of jumper wires.

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

Information Blog
----------------
An Internet blog has been established at
picocktails.blogspot.com to provide an ongoing
information resource for constructors of
these projects. Links to the design
documentation, cocktail files, photo albums, test
programs etc will be readily accessible from the
blog.
