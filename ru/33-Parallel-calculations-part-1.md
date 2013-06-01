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

The next step needed to improve the speed of calculations is to use more than one computer. To do this, one Raspberry Pi will be needed as a server and another Raspberry Pi or other computer will be needed as a client. Connect both Raspberry Pis or the Raspberry Pi and other computer to the network. Then find the IP addresses for the two machines. On LINUX or
OSX type `ifconfig`, or on Windows type `ipconfig`.

To avoid any difficulties with network address translation (NAT), make sure that the two computers are on the same
network. Then test the network path using ping from one machine to the other. For example,

    ping -c 5 192.168.1.12

If this is successful, it will return the time the ping took five times. Use ping from both machines to be absolutely sure the network path is as hoped. Now create a `SimpleServer.py` file containing

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

To start up the simple server, open a python shell and type:

    from SimpleServer import SimpleServer
    import socket
    server = SimpleServer("192.168.1.3" , 20000)
    server.initialise()

Then go to the other Raspberry Pi and open a python shell and type:

    import socket
    sock = socket. socket(socket. AF_INET, socket. SOCK_STREAM)
    sock.connect(("192.168.1.3" , 20000)) # The IP address of the server machine
    sock.close()

Now look on the server machine. The server machine reports that the client machine has connected to it.

In the example program, the server address is explicitly used in the server startup, since the host name of a Raspberry Pi using DHCP resolves to the local address `127.0.0.1`. If the server's host name is associated with an address on the local network, then the command `socket.getfqdn()` can be used instead of `192.168.1.3`.

The `SimpleServer` class contains several member variables which are initialised in the constructor `__init__`. These
member variables contain the values for the host name, port number, socket and client connections. The port number has to be high enough to be accessible to a non-root user. Each server binds to a given port number. Once the port number is in use, it cannot be used by another server. Therefore, the server has to be shutdown before it can re-bind to the same port. The instantiation `socket.socket(socket.AF_INET, socket.SOCK_STREAM)` creates a socket. The `AF_INET`
refers to the address family, which in this case is an Internet Protocol address. There other types of socket for bluetooth. The `SOCK_STREAM` is the socket type, which is a reliable two-way connection-based byte stream. The available address family names and the socket types are also found within standard LINUX C libraries. The `client_sockets` variable is a list for holding each client connection.

The `SimpleServer` class contains two other member functions `initialise` and `serve_forever`. The initialise function tries to bind to the allocated socket. If this is successful, it configures the socket as a listening socket. Then a thread is created which executes the `serve_forever` member function. The thread is configured as a daemon and started. The serve_forever member function is the listening process which accepts connections from clients. The client socket associated with a connection is then added to the list of client sockets.

The next article will show how to send and receive commands from clients, using threads for each associated connection.

*Testedwith Python 2.7.3 (Raspbian) & 2.6.1 (OSX10.6.8)*
