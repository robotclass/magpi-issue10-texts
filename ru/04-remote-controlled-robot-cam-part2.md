WebIOPi - Raspberry Pi REST framework

Роботизированная камера
с удалённым управлением, Часть II

Eric PTAK
Guest Writer

УРОВЕНЬ СЛОЖНОСТИ: ПРОДВИНУТЫЙ

Построение интерфейса
----------------------

Написать интерфейс - тоже простая задача, и решается HTML файлом со встроенным Javascript. 
Чтобы использовать возможности WebIOPi, необходимо всего лишь включить файл webiopi.js в документ HTML.
Создайте новый файл index.html рядом со сценарием Python:

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
Я добавил пустую секцию сценария, мы запишем в ней вызовы к библиотеке WebIOPi.
Также есть контейнер div, который будет содержать элементы управления.

In the script section, we add an init function to
build the interface using WebIOPi library. It
contains many functions to ease creation of
buttons that control GPIO. Here we use a basic
button to call a different function on press and
release. Each function calls a different macro on
the server. Don�t forget to register the init
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

It�s now time to start the server and enjoy the
interface. Open a terminal in the folder you
created Python and HTML files and execute the
script:

    $ sudo python yourscript.py

