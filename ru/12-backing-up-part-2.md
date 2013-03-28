Backing up - part 2
===================

Norman Dunbar

DIFFICULTY : MEDIUM

In part 1, I demonstrated how you could make a
backup of your Raspberry Pi's SD card. In part 2, I
shall demonstrate how you may check that the
backup worked and also, I shall show you some of
the "fun" that can be had using the backup image as
a disc drive. You will see how to modify files within
the backup image itself. When you subsequently
write the image to an SD card, all your changes will
be present.
Again, this is simple to do on a Linux computer,
however, for Windows users it's not easy at all.
Windows users should consider using Linux Live CD
or running Linux in a VirtualBox VM (or similar) if you
want to follow along.

Checking the Backup File
----------------------------
Making a backup is simple, normally, but how do you
know that the backup has worked? It is not fun when
you have a trashed Raspberry Pi SD card and you
discover that your backup is not able to be restored.
You have to begin again from scratch by building a
new default distribution and reinstalling and
configuring all your software. Of course, none your
own files will be able to be restored. So you should
always check your backups work.
The simplest method of checking is to write the image
to a spare SD card and plug that in to the Rasberrp Pi
and reboot it. This is also about the quickest method,
and the part 1covered this in some detail.

This article looks at a method whereby you can take
the backup image and pretend that it is a real device,
an SD card if you like, and use it accordingly as if it
was a real SD card.
Please note, all of the following assumes that the
backup image is uncompressed and resides on a
local hard disc. If you have a compressed image,
then you may wish to uncompress it if you are
following along.
The first step, as root – as ever – is to determine
where the partitions begin. Because we have an
image copy of the SD card, we can look at the file
itself and see the partition table within. If you wish to
run all the following commands as the Pi user, the
please prefix each one with sudo. Otherwise, make
life simple and type:
    $ sudo sh
This command will start a root shell for you, and there
will be no need to prefix the commands with sudo.
First list the backup image to see where the partitions
begin and the sizes of the sectors. Fdisk reports sizes
in sectors, but handily displays the sector size at the
top.
The command to list the partition details is:
    $ fdisk Rpi_8gb_backup. img

    Welcome to fdisk (uti l-linux 2. 21. 2).
    . . .
    Command (m for help): p
    Disk Rpi_8gb_backup. img: 7948 MB, 7948206080 bytes
    255 heads, 63 sectors/track, 966 cylinders, total 15523840 sectors
    Units = sectors of 1 * 512 = 512 bytes
    Sector size (logical/physical): 512 bytes / 512 bytes
    I/O size (minimum/optimal): 512 bytes / 512 bytes
    Disk identifier: 0x000dbfc6
    Device Boot Start End Blocks Id System
    /BU/Rpi_8gb_backup. img1 8192 122879 57344 c W95 FAT32 (LBA)
    /BU/Rpi_8gb_backup. img2 122880 15523839 7700480 83 Linux

The output from this command is shown at the top of
the following page.
When the utility has loaded, we use the 'p' command
to print the current partition table. This is the
command that shows details of the current partitions,
where they are located and how big they are.
We can see from the line starting with ‘Units’ or
‘Sector size’ that a sector is 512 bytes. We can also
see that our two partitions start at sectors 8,192 and
122,880. Multiplying these start sectors by 512 gives
the start position in bytes. This works out at
4,194,304 and 62,914,560 bytes respectively,
however, we don't really need these latter figures as
Linux will calculate it for us.
On your Raspberry Pi, the first partition is normally
mounted at /boot while the second is the root (/)
mount point.
To check these, without needing access to the Pi, we
need to create a pair of mount points, as follows:

    $ mkdir /mnt/root /mnt/boot
    $ chmod a=rwx /mnt/root /mnt/boot

The two commands above only requires to be
executed once, the very first time we attempt this
exercise. Next, and on any subsequent occasion
when we do this, the following two commands should
be typed - on one line each - to mount the backup
image's two partitions as pseudo disc drives:

    $ mount -t vfat -o loop, offset=$((8192 * 512))
    /BU/Rpi_8gb_backup. img /mnt/boot
    $ mount -t ext4 -o loop, offset=$((122880 *
    512)) /BU/Rpi_8gb_backup. img /mnt/root

The above creates a couple of mount points
(directories) and then mounts the first partition within
the image file as a vfat file system on /mnt/boot and
then mounts the second partition as an ext4 file
system on /mnt/root.
The first two commands to create the mount points
and set the permissions on them are only required
once, the first time you carry out this exercise.
You can now see the files by opening a file manager
and looking at the /mnt/boot and /mnt/root directories
– you should see your various files as if you were
looking on your Raspberry Pi.
At least you are now sure that your image file could
be restored to an SD card, and that it is at least
mountable - so it should be ok for future use if you
ever require to restore a corrupted card. However,
with the card image currently mounted as a pseudo
drive on your Linux laptop, you can treat it exactly as
if it was a real drive, and edit files, create new ones,
delete ones you no longer require, and so on.

Edit a file
--------------
If, for example, you were about to restore this backup
image to a second SD card, but for use in a Pi
connected to a TV that has VGA input rather than
HDMI, you could edit the config.txt file, in the /boot
partition, inside the image before you write it to the
new SD card.
As you have the image's first partition, the one
normally mounted at /boot, mounted on your laptop
as /mnt/boot, then all you have to do is edit
/mnt/boot/config.txt using your favourite editor, and
set hdmi_group and hdmi_mode to 1 and save the
file.
When you subsequently write this backup image to a
new card, it will be ready to run on a Pi connected to
your VGA TV.
Why would you want to do this? Well, You might have
a couple of Pis running identical setups, but one is in
the main computer room attached to an HDMI TV or
monitor, and another in the Kids bedroom, connected
to a VGA TV or monitor. This method will save you
having to backup one of the devices, write the SD
card for the other, boot it up attached to the "wrong"
display, change the config and then boot it on the
correct display.
Obviously, if you have configured both of the devices
to have a static IP address when connected to your
network, you will have to edit /etv/network/interfaces
to suit the device which is having the SD card reimaged,
otherwise you will end up with two devices
runing the same IP address, which can only result in
problems.

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
