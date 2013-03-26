WebIOPi - Raspberry Pi REST framework

Remote Controlled
Robot Cam, Part II

Eric PTAK
Guest Writer

DIFFICULTY: ADVANCED

Building the interface
----------------------

Building your own interface is also easy, and is
based on a HTML file embedding some
Javascript. You only have to load the webiopi.js
file from your HTML file to use the WebIOPi
power. Create a new index.html file next to your
Python script:

    <html>
      <head>
      <title>CamBot</title>
      <script type="text/javascript" src="/webiopi.js"></script>
      <script type="text/javascript">
      // Javascript code goes here
      </script>
      </head>
      <body>
        <div id=”box" align="center">
        </div>
      </body>
    </html>

Take note of the starting slash when loading
webiopi.js, to ensure it will be searched in the
root of the server or it may be not found.
I added an empty script section; we will use the
WebIOPi JS library here. There is also a div box,
which will contain controls.

In the script section, we add an init function to
build the interface using WebIOPi library. It
contains many functions to ease creation of
buttons that control GPIO. Here we use a basic
button to call a different function on press and
release. Each function calls a different macro on
the server. Don’t forget to register the init
function to WebIOPi. It will be called when
everything is loaded and ready.

    function init() {
      var button =
      webiopi().createButton(
        "bt_up" , // id
        "/\\" , // label
        go_forward, // press
        stop); // release
        $("#box" ).append(button);
    }



[page05]

Be careful that webiopi() is a function and a
reserved word that need brackets to return the
WebIOPi object. You can use w() to short the
webiopi() call.

It’s now time to start the server and enjoy the
interface. Open a terminal in the folder you
created Python and HTML files and execute the
script:

    $ sudo python yourscript.py

