Резервное копирование - Часть II
================================

Norman Dunbar

Уровень сложности: Средний.

* * *

В первой части я показал, как сделать резервную копию SD карты вашей Raspberry Pi. Сейчас же мы рассмотрим, как можно проверить правильность резервной копии, а также я покажу несколько трюков, которые можно делать с резервной копией как с диском. Вы узнаете, как изменять файлы в резервной копии напрямую, - так, чтобы все изменения отразились при записи образа на SD карточку.

Напомню, что все действия легко выполняются в Linux, для пользователей Windows всё будет гораздо сложнее. В таком случае следует подумать о Live CD с Linux или установке Linux в виртуальную машину VirtualBox (или любую другую).

Проверка файла резервной копии
------------------------------
Обычно, создание резервной копии - несложая задача, но как убедиться в том, что она правильная? Ситуация, когда SD карточка полетела, а резервная копия оказывается нерабочей, совсем не весёлая - придётся начинать всё сначала, устанавливать дистрибутив, программы, с нуля настраивать конфигурацию. И конечно же, ни один из ваших файлов не будет подлежать восстановлению. Что в итоге? Всегда нужно проверять правильность резервной копии.

Самый простой метод - записать образ на другую SD карточку, а потом перезагрузить Raspberry Pi с ней. Это также самый быстрый способ, и в первой части статьи мы осветили этот вопрос.

В этой статье мы рассмотрим метод, который позволит вам работать с образом диска, как будто он был настоящей SD картой.

Пожалуйста, обратите внимание, что всё нижеизложенное предполагает, что образ диска несжатый и находится на локальном жёстком диске. Если у вас сжатый образ, вам может понадобиться распаковать его.

Первым делом под пользователем root (как и всегда) необходимо определить место начала разделов. Так как у нас есть копия образа SD карты, мы можем посмотреть на сам файл и узнать, где начинается таблица разделов. Если вы хотите выполнять все команды как обыеновенный пользователь Pi, дописывайте в начало команд `sudo`. Можно немного упростить себе жизнь и набрать:

    $ sudo sh

Эта команда запустит консоль root, и не нужно будет добавлять `sudo` к началу команд.

В первую очередь, просмотрите образ диска, чтобы узнать, где начинаются разделы и какого размера секторы. Fdisk отображает размеры в количестве секторов, но размер сектора удобно посмотреть в начале вывода.

Команда для отображения структуры разделов выглядит так:

    $ fdisk Rpi_8gb_backup.img

    Welcome to fdisk (util-linux 2.21.2).
    ...
    Command (m for help): p
    Disk Rpi_8gb_backup.img: 7948 MB, 7948206080 bytes
    255 heads, 63 sectors/track, 966 cylinders, total 15523840 sectors
    Units = sectors of 1 * 512 = 512 bytes
    Sector size (logical/physical): 512 bytes / 512 bytes
    I/O size (minimum/optimal): 512 bytes / 512 bytes
    Disk identifier: 0x000dbfc6
    Device Boot Start End Blocks Id System
    /BU/Rpi_8gb_backup. img1 8192 122879 57344 c W95 FAT32 (LBA)
    /BU/Rpi_8gb_backup. img2 122880 15523839 7700480 83 Linux

Вывод этой команды представлен в начале следующей страницы.

Когда программа загружена, мы используем команду 'p' чтобы напечатать текущую таблицу разделов. Эта команда показывает сведения о разделах, их расположении и размере.

Также из строчки, начинающейся с 'Units' или 'Sector size' мы узнаём размер сектора - 512 байт. Мы также можем увидеть, что два наших раздела начинаются с секторов 8192 и 122880. Умножая эти номера на 512, получим начало каждого раздела в байтах, будет 4194304 и 62914560 байт соотвественно. На самом деле нам не нужно считать смещения в байтах, так как Linux сделает это за нас.

На Raspberry Pi первый раздел обычно монтируется как `/boot`, а второй - как корневой раздел `/`.

Чтобы проверить это без Pi, нам нужно сделать две точки монтирования:

    $ mkdir /mnt/root /mnt/boot
    $ chmod a=rwx /mnt/root /mnt/boot

Эти две команды нужно выполнить один раз. В дальнейшем, чтобы примонтировать два раздела образ диска в псевдо-диск, нужно будет выполнить:

    $ mount -t vfat -o loop, offset=$((8192 * 512)) /BU/Rpi_8gb_backup. img /mnt/boot
    $ mount -t ext4 -o loop, offset=$((122880 * 512)) /BU/Rpi_8gb_backup. img /mnt/root

В результате будет создано несколько точек монтирования (директорий), а затем первый раздел с файловой системой `vfat` будет подмонтирован в `/mnt/boot`, а второй раздел ext4 - в `/mnt/root`.

Первые две команды для создания директорий нужно выполнить только один раз в самом начале.

Теперь можно открыть файловый менеджер и посмотреть содержимое `/mnt/boot` и `/mnt/root` - вы должны увидеть множество файлов, точно как если бы видели их из Raspberry Pi.

В итоге, мы по меньшей мере удостоверились, что файл образа может быть восстановлен на SD карту и он может подмонтироваться - в дальнейшем в случае порчи SD карточки образ можно использовать и всё будет хорошо.

Таким образом, теперь подмонтированный образ карточки можно воспринимать как настойщий диск, редактировать файлы, создавать новые, удалять ненужные - и так далее.

Редактирование файлов
---------------------
Предположим, вам нужно залить образ на SD карту, но вместо RCA будете использовать HDMI подключение. Но прежде, вам нужно отредактировать файл `config.txt` в каталоге `/boot` внутри образа.

Так как первый раздел образа, тот что в Raspberry Pi значится как `/boot`, примонтирован на компьютере как `/mnt/boot`, то всё что остаётся сделать, это поправить `/mnt/boot/config.txt` вашим любимым текстовым редактором:

    hdmi_group = 1
    hdmi_mode  = 1

После того как вы запишете образ на SD карту, RPi будет работать с HDMI.

Зачем это может пригодиться? Ну, например если у вас несколько RPi с одинаковыми настройками, но один подключен к HDMI TV, а другой - к VGA. Такое редактирование файлов позволит вам хранить только копию карты с одного устройства, а для другого останется просто "поправить дисплей".

Естественно, не забудьте отредактировать `/etc/metwork/interfaces` если ваши RPi настроены со статическим IP адресом.


Restore a single file
-----------------------------
This method of mounting a disc image as a device is
useful for times when you manage to delete a file,
accidentally of course, on your Pi, but you know that
you have a backup. If the Pi is on your network, you
can simply mount the backup image as above, locate
the file and check that it is the one you want, then use
scp or sftp to copy the file to your Pi, as demonstrated
below:

    $ mount -t ext4 -o loop, offset=$((122880 *
    512)) /BU/Rpi_8gb_backup. img /mnt/root
    $ cd /mnt/root/home/pi
    $ scp Lost_fi le. txt pi@raspberrypi :

You will be prompted for pi's password, and then the
file will be copied over to the Pi user's home directory
on your Raspberry Pi.
So there you have it, how to mount your backup file
on the backup computer to check that it is ok, and as
a bonus, how to extract a file (or files) for an
individual recover. What else can we do?

Restore a full backup
--------------------------------
This is as simple as initialising the SD card for the
first time. You can restore a backup file to your SD
card provided that the SD card is bigger or the same
size as the image file. As ever, the following
command must be executed as root.
$ dd if=Rpi_8gb_backup. img of=/dev/mmcblk0
bs=2M
The command should be all on one line.
It takes a while, but it will restore in the end. All you
have to do is wait for the copy to finish and then boot
the Pi with the restored SD card in the slot. See part 1
for details on restoring compressed and/or split
backup images.

Restore to a larger card
-----------------------------
We can also use the smaller backup files to initialize
a larger SD card, if we were perhaps upgrading. This
is what I had to do when I restored a 4Gb backup to
my 8Gb card. Once the restore completed I had a
4Gb image on an 8Gb SD card. In order to reclaim
the missing 4Gb all I did was to boot the Pi with the
restored 8Gb card in place, and login as the pi user
as normal.
Once logged in, I executed the sudo raspi-config
command, selected the option to "Expand root
partition to fill SD card" and the system happily
extended the 4Gb partition to fill up the remaining free
space on the card. The actual resizing is carried out
as part of the next reboot of the Pi - it doesn't happen
immediately.
You can see that it worked by executing the df
command, which does not need to be executed as
root!

    $ df -h /
    Fi lesystem Size Used Avai l Use% Mounted on
    /dev/root 7. 3G 1. 6G 5. 4G 23% /

The output above shows my root file system,
mounted on /, is 7.3 Gb in size so I know it cannot
possibly be the same size as it was when I restored
from the 4Gb backup image.
