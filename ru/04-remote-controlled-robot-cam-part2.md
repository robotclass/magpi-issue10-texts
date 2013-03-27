WebIOPi - Фреймворк REST для Raspberry Pi

Роботизированная камера с удалённым управлением, Часть II

Eric PTAK
Guest Writer

УРОВЕНЬ СЛОЖНОСТИ: ПРОДВИНУТЫЙ

Построение интерфейса
---------------------

Написать интерфейс - тоже простая задача, и решается HTML файлом со встроенным Javascript. 
Чтобы использовать возможности WebIOPi, необходимо всего лишь включить файл `webiopi.js` в документ HTML.
Создайте новый файл `index.html` рядом со сценарием Python:

    <html>
      <head>
      <title>CamBot</title>
      <script type="text/javascript" src="/webiopi.js"></script>
      <script type="text/javascript">
      // Код Javascript будет здесь
      </script>
      </head>
      <body>
        <div id="box" align="center">
        </div>
      </body>
    </html>

Обратите внимание на начальный слэш при подключении webiopi.js, он указывает на то,
что файл нужно искать в корне сервера - иначе он может и не подгрузиться.
Я добавил пустую секцию `script`, в ней мы запишем вызовы к библиотеке WebIOPi.
Также есть контейнер `div`, который будет содержать элементы управления.

В секции `script` мы записываем интерфейсную функцию `init()`.
WebIOPi содержит множество функций для упрощения создания кнопок управления GPIO.
Здесь мы используем простой вызов `createButton`, чтобы различать нажатие и отпускание кнопки.
Каждая функция вызывает свою макропоследовательность на сервере.
Не забудьте зарегистрировать функцию `init()` в WebIOPi.
Она будет выполнена как только всё загрузится и будет готово.

    function init() {
      var button =
      webiopi().createButton(
        "bt_up" ,    // идентификатор
        "/\\" ,      // надпись
        go_forward,  // обработчик нажатия
        stop);       // обработчик отпускания
        $("#box" ).append(button);
    }

[page05]

Будьте внимательны: `webiopi()` является функцией и зарезервированным словом. 
Скобки нужно записывать обязаательно, чтобы она могла вернуть объект WebIOPi.
Для сокращения можно использовать просто `w()`.

Самое время запустить сервер и полюбоваться на интерфйс.
Откройте терминал в том каталоге, где вы создали сценария Python и HTML файл, и выполните команду:

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