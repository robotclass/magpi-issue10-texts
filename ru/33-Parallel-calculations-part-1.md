Параллельные вычисления, Часть I
================================

W. H. Bell, The MagPi

Уровень: сложный

* * *

В предыдущих примерах на Python мы показывали, как получить доступ к программе из веба. Тем не менее, существуют и другие типы клиент-серверных взаимодействий, которые также могут оказаться полезными. Статья этого месяца - первая часть демонстрации простого клиент-серверного приложения, которая показывает приемы программирования для других разработок.

К Raspberry Pi можно присоединить солнечные панели или батареи и подключить его к сети через WiFi адаптер. В этом виде он может использоваться для удаленного наблюдения или робототехники. Как вариант, можно использовать Raspberry Pi в качестве управляющего звена вычислительной системы. В первом примере Raspberry Pi может быть как клиентом, так и сервером. Во втором - скорее всего просто сервером.

Сервера - это процессы, которые ожидают подключения клиентов (слушают). Когда сервер получает запрос от клиента, обычно создается отдельный поток для обслуживания нового подключения. Поток - наименьшая последовательность запрограммированных инструкций, которая может быть исполнена операционной системной независимо от других. Часто прослушивающий процесс сервера выполняется отдельным потоком и выделяет клиентам потоки из некоторого ограниченного пула. Когда клиент завершает работу, назначенный ему поток должен быть освобожден для новых соединений. Если сервер будет создавать для каждого очередного клиента потоки, у него быстро закончится память.

Иногда для того, чтобы произвести вычисления достаточно быстро, требуется несколько компьютеров. Когда физическая или инженерная проблема описана уравнениями со множеством переменных, поиск глобального минимума на бумаге становится невозможным, а на одном компьютере занимает слишком много времени. Для решения этой проблемы, несколько компьютеров объединяются в сеть и вычисляют множество точек одновременно, тем самым находя численное решение задачи намного быстрее.

Raspberry Pi наделен не самым быстрым центральным процессором, но на нем можно показать сам принцип таких вычислений. Для клиент-серверного решения задачи понадобится два или более Raspberry Pi, или просто другой компьютер. Если вы обладатель нескольких компьютеров или же у вас есть возможность объединиться с друзьями, будет только лучше.


Классы и вычисление функций
---------------------------
Создайте файл с именем `FunctionCalculator.py` и добавьте в него следующий код:

    # Класс для вычисления значения функции, записанной в строке
    class FunctionCalculator:
      def evaluate(self, cmd):
        y = 0.
        print "exec \"%s\"" % cmd
        exec cmd
        print "y = %e" % y
        return y
    
    # Класс для пакетного рассчёта нескольких уравнений
    class SynchronousCalculator:
      def __init__(self):
        self.calculator = FunctionCalculator()
    
      def evaluate(self, cmds):
        results=[]
        for cmd in cmds:
          results.append(self.calculator.evaluate(cmd))
        return results

`FunctionCalculator` - простой класс с одним методом, который выполняет код, записанный в строке, как команду python. `SynchronousCalculator` включает в себя экземпляр класса `FunctionCalculator` для выполнения нескольких команд друг за другом. Из этого следует, что метод `evaluate` в `FunctionCalculator` получает одну строчку, а в такой же метод в `SynchronousCalculator` передаётся список строк.

Чтобы протестировать `FunctionCalculator.py` откройте оболочку python набрав `python`. Затем выполните:

    from FunctionCalculator import FunctionCalculator
    f = FunctionCalculator()
    f.evaluate("y=5*100")

Эти команды рассчитают результат вычисления функции. Несколько точек функции можно вычислить с помощью `SynchronousCalculator`. Например, десять точек кривой y=x^2 можно получить набрав:

    from FunctionCalculator import *
    import math
    f_sync = SynchronousCalculator()
    cmds = []
    for x in xrange(11):
      cmds.append("import math; y = math.pow(%f, 2)" % (x))
    f_sync.evaluate(cmds)

Обратите внимание, что в классе `FunctionCalculator`, используемом в `SynchronousCalculator` не нужно импортировать функции из `math` до тех пор пока они не потребуются при выполнии команды. Это особенно эффективно когда программа выполняется на многих компьютерах ивычисляемая функция может быть неизвестна до момента запуска программы.

Следующим шагом в ускорении вычислений будет использование второго компьютера. Чтобы выполнить его, нужно сделать один Raspberry Pi сервером, а другой Raspberry Pi или компьютер - клиентом. Соедините оба Raspberry Pi или Raspberry Pi с другим компьютерм сетью. Затем найдите IP адреса обеих машин, для этого в Linux или OS X наберите `ifconfig` или `ipconfig` в Windows.

Чтобы избежать проблем с трансляцией сетевых адресов (NAT), убедитесь что два компьютера находятся в одной сети. Затем протестируйте сетевой маршрут от одной машины к другой с помощью ping. Например, так:

    ping -c 5 192.168.1.12

Если всё хорошо, утилита покажет время, потраченное на запросы, пять раз. Используйте ping на обеих машинах чтобы полностью удостовериться в надёжности сетевого соединения. После этого создайте файл `SimpleServer.py`, содержащий следующий код:

    class SimpleServer:
      def __init__(self, host, port):
        self.host = host
        self.port = port
        self.sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
        self.sock.settimeout(None)
        self.client_sockets = []

      def initialise(self):
        try:
          self.sock.bind((self.host, self.port))
        except socket.error:
          return

        self.sock.listen(5)
        self.server_thread = threading.Thread(target=self.serve_forever)
        self.server_thread.setDaemon(True)
        self.server_thread.start()
        print "Server running on %s and listening on %d" % (self.host, self.port)

      def serve_forever(self):
        try:
          request, client_address = self.sock.accept()
        except socket.error:
          return

        self.client_sockets.append(request)
        print "Received connection from " , client_address

Чтобы запустить наш простой сервер, откройте оболочку python и выполните:

    from SimpleServer import SimpleServer
    import socket
    server = SimpleServer("192.168.1.3" , 20000)
    server.initialise()

Затем откройте python на втором компьютере и наберите:

    import socket
    sock = socket. socket(socket. AF_INET, socket. SOCK_STREAM)
    sock.connect(("192.168.1.3" , 20000)) # The IP address of the server machine
    sock.close()

Вернитесь к серверному Raspberry Pi. Он должен показывать, чтол к нему подключился клиентский компьютер.

В примере выше при запуске сервер IP адрес указывается в явном виде, т.к. имя хоста для Raspberry Pi по DHCP мы получим `127.0.0.1`, что соответствет локальному адресу. Когда имя хоста ассоциировано с адресом в локальной сети, можно использовать команду `socket.getfqdn()` вместо `192.168.1.3`.

Класс `SimpleServer` срдержит несколько атрибутов, которые инииализируются в конструкторе `__init__`. Они содержат значения имени хоста, номера порта, сокет порт и клиентские подключения. Номер порта должен быть достаточно большим, чтобы его мог использовать пользователь без привелегий root. Каждый сервер привязывается к указанному номеру порта. Если порт занят сервером, то никто больше не может его использовать. Таким образом, сервер необходимо выключать, прежде чем он сможет подключиться с тем же портом. С помощью `socket.socket(socket.AF_INET, socket.SOCK_STREAM)` создаётся сокет. Константа `AF_INET` определяет семейство адресов, в нашем случае это адрес Internet Protocol. Существуют другие типы адресов, например Bluetooth. `SOCK_STREAM` - тип сокета, надёжное двухстороннее соединение с подключением, представленное потоком байтов. Имена семейств адресов и типы сокетов можно найти в стандартных библиотеках C для Linux. Переменная `client_sockets` - это массив для хранения входящих соединений.

Класс `SimpleServer` содержит два других метода, `initialise` и `serve_forever`. The initialise function tries to bind to the allocated socket. If this is successful, it configures the socket as a listening socket. Then a thread is created which executes the `serve_forever` member function. The thread is configured as a daemon and started. The serve_forever member function is the listening process which accepts connections from clients. The client socket associated with a connection is then added to the list of client sockets.

В следующей статье я расскажу как отправлять клиентам команды и получать ответы с использованием отдельных потоков для каждого соединения.

*Testedwith Python 2.7.3 (Raspbian) & 2.6.1 (OSX10.6.8)*
