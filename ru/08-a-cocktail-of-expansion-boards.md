Попробуйте коктейль проектов
============================

Перед вами - первая статья из серии, призванной помочь пользователям Raspberry Pi собирать платы расширения и полностью использовать возможности ввода-вывода их RPi.

Уровень сложности: продвинутый

Lloyd Seaton

Применимость
------------
В этой статье описаны относительно сложные проекты, которые не рекомендуются к реализации новичкам. К ним нет готовых наборов деталей, наоборот, вам придётся покупать их самостоятельно. Впрочем, списки нужных компонентов и магазинов всегда прилагается.

Вместе дешевле
--------------

> Печатные платы можно купить дешевле, если вы объединитесь в группы по 2-3 и более человека.

Радиодетали обойдутся дешевле, если покупать их помногу. Вы можете порядочно сэкономить, если объединитесь с единомышленниками в школе или своём сообществе любителей Raspberry Pi.

О платах
--------
Многие избегают разработки собственных схем плат расширений, и поэтому я решил написать этот цикл статей. Если собрать "коктейль" из проектов с готовыми разработками, дело остаётся лишь за реализацией - так можно сэкономить время и деньги. Особенно если объединиться и сэкономить на деталях.

Стратегия производства печатных плат
------------------------------------
Автор статьи начал участвовать в разработке малых сборок печатных плат в конце 2001, когда он решил разрабатывать контроллер помп для хозяйств с питанием на солнечных батареях.

Вскоре стало очевидно, что стоимость производства плат нужно уменьшать, и было принято решение воспользоваться услугой Mini Board Pro от ExpressPCB, а ещё проектировать платы таким образом, чтобы на одном фабричном листе умещалось несколько (в начале 2-3) плат. После получения заказа лист разрезался, а затем производилась непосредственно сборка. Эта стратегий позволила снизить стоимость производства каждой платы до 15A$, это вполне приемлимая цена.

Недавно был произведён редезайн контроллера (с помощью Raspberry Pi), и теперь он работает на микроконтроллере ATtiny85 вместо чисто аналоговой схемы. Это позволило уместить 6 плат на одном листе и ещё больше снизить цену до 7.50$, это просто замечательно.

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
BatteryLoadManager+ или некоторых плат Arduino, без использования соединительных проводов.

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

Процедура заказа производства печатных плат
-------------------------------------------
Прежде чем заказать печатные платы, вам необходимо установить ExpressPCB CAD на компьютере с доступом в Интернет. Программа бесплатная и предназначена для Windows, но работает и в Linux под Wine. Вам нужно выбрать файл cocktail, скачать и открыть его в ExpressPCB. Дальнейшая процедура простая и хорошо описана в информационном блоге.

Информационный блог
-------------------
В Интернете открылся блог picocktails.blogspot.com, где вы можете почерпнуть актуальную информацию для конструкторов представленных проектов: ссылки на чертежи, файлы, фотоальбомы, тестовые программы и многое другое.
