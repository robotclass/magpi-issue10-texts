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


Add a webcam stream
-------------------

There are many possibilities to stream a
webcam, which may depend on the model you
have. In my case, I have a recent webcam which
outputs both RAW and MJPEG formats up to
1280x720@30fps.

First, check your webcam is installed with a
terminal:

    $ lsusb
    [...]
    Bus 001 Device 005: ID 046d: 0825
    Logitech, Inc. Webcam C270
    $ ls /dev/video*
    /dev/video0

Then, to check it’s working, you can install
uvccapture using apt-get or aptitude and take a
single snapshot:

    $ uvccapture -v
    Using videodevice: /dev/video0
    Saving images to: snap.jpg
    Image size: 320x240
    Taking snapshot every 0 seconds
    Taking images using mmap
    Resetting camera settings
    Camera brightness level is 0
    Camera contrast level is 255
    Camera saturation level is 255
    Camera gain level is 255
    Saving image to: snap.jpg

If uvccapture returns without error, we can
continue to stream the webcam.

I use MJPG-STREAMER, which is really easy to
use. It gives me a 320x240@25fps pass-through
MJPEG stream over HTTP. I tried FFMPEG but
it takes the RAW output of the webcam to
encode it in MJPEG with a framerate under 5fps.

You can download MJPG-STREAMER at
http://sourceforge.net/projects/mjpg-streamer/

You will also need libjpeg8-dev you can install
using aptitude/apt-get.

Uncompress and build MJPG-STREAMER using
make command. Then execute it:

    $ ./mjpg_streamer -i " ./input_uvc.so
    –r 320x240 –f 25" -o " ./output_http.so
    –n –p 8001" &

[Илья: по кавычкам надо уточнить, чувствуется что не очень корректно]

Back to HTML file, add a img tag with src set to
http://raspberrypi:8001/?action=stream replacing
raspberrypi by your Pi’s IP. You can also directly
try the URL in your browser.

    ...
    <img
    src="http://raspberrypi:8001/?action=stream">
    </body>
    </html>

Conclusion
----------

With this article, you learned how to install
WebIOPi and how to use it in your own Python
scripts to write macros you can call from the web.

The code is incomplete as it only allows to go
forward and to stop. Just add
left/right_backward, turn_left/right and
go_backward functions to move the robot in all
directions.

You can download the complete code at
http://files.trouch.com/webiopi/cambot.zip. You
will find more information on the project wiki and
in the examples folder of WebIOPi archive.

Eric PTAK, creator of WebIOPi
http://trouch.com
http://code.google.com/p/webiopi/

NOTE:
Part I of this tutorial appeared in the last
issue of the MagPi. Please read Part I
before attempting what is shown here.
You can download Issue 9 at:
www.themagpi.com