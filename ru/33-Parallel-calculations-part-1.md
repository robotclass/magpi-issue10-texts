Parallel calculations part 1
============================

W. H. Bell, The MagPi

Difficulty: Advanced

* * *

Previous Python examples have discussed web server access to programs. However, sometimes other client server relationships can be useful. This month's article is the first part of a demonstration of a basic client server application, which shows how other applications might be written.

The Raspberry Pi can be deployed with solar panels or batteries and connected to a network using a Wifi dongle. In this manner, it can be used for remote monitoring or robotics. Alternatively, the Raspberry Pi could be used as the control centre of a computational system. In the first example, the Raspberry Pi could be a client or a server. In the section example, the Raspberry Pi is likely to be a server.

Servers are processes which listen for clients to connect to them. When a server receives a request from a client, it is common for a thread to be allocated to the client connection. A thread is the smallest sequence of programmed instructions which can be managed independently by an operating system. Often the server listening process runs as one thread and gives the clients each a thread from a limited pool. When the client is finished, the thread in the server should be released for a new connection. If the server created a new thread for each client connection it would quickly run out of memory. Sometimes calculations require more than one computer to find a result quickly. When a physics or engineering problem is described by an equation with many variables, finding a global minimum for the equation can be impossible on paper and take too long with one computer. To solve this problem, a network of computers can be connected together to calculate many points and numerically solve the equation much more quickly. While the Raspberry Pi does not have the fastest CPU, it can be used to demonstrate this principle. For the client-server parts of this problem another Raspberry Pi or another computer will be needed. If you have many other computers to play with or can invite many friends around, all the better.


Classes and function evaluation
-------------------------------
Create a file called `FunctionCalculator.py` and add to it,

    # A class to calculate the value of a function string
    class FunctionCalculator:
      def evaluate(self, cmd):
        y = 0.
        print "exec \"%s\"" % cmd
        exec cmd
        print "y = %e" % y
        return y
    
    # A class to calculate many the result of many equations at once.
      class SynchronousCalculator:
        def __init__(self):
          self.calculator = FunctionCalculator()
        
        def evaluate(self, cmds):
          results=[]
          for cmd in cmds:
            results.append(self.calculator.evaluate(cmd))
          return results

The `FunctionCalculator` is a simple class which has one member function that executes the value of a string as a python command. The `SynchronousCalculator` includes an instance of the `FunctionCalculator` class to evaluate many commands one after another. In the case of the FunctionCalculator, the evaluate method takes one string whereas the `SynchronousCalculator` function takes a list of strings.

To test `FunctionCalculator.py` open a python shell by typing `python`. Then type:

    from FunctionCalculator import FunctionCalculator
    f = FunctionCalculator()
    f.evaluate("y=5*100")

This evaluate function prints the command and the result of evaluating the command. Many points of a mathematical function can be evaluated using the SynchronousCalculator. For example, ten points on the curve y=x^2 can be calculated by typing,

    from FunctionCalculator import *
    import math
    f_sync = SynchronousCalculator()
    cmds = []
    for x in xrange(11):
      cmds.append("import math; y = math. pow(%f, 2)" % (x))
    f_sync.evaluate(cmds)

Notice that the `FunctionCalculator` class, which is used by the `SynchronousCalculator` class, does not need to import the `math` functions until they are needed during the command evaluation. This becomes very powerful when used with many computers, where the function to be evaluated remotely might not be known before runtime.

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
