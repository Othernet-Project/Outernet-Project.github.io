A microSD greater than 4GB in size is required. The latest Armbian image for Dreamcatcher is found here:

https://archive.outernet.is/images/

Instructions:

1. Extract the image. it is compressed using "gzip." You can use gunzip on Linux or 7zip on Windows to extract it:

    gunzip image_name.img.gz

2. Write the image to the SD card using any normal writing process.

On Linux you can use

    sudo dd if=image_name.img of=/dev/device bs=4M status=progress ; sync ; sync; sync

device can be obtained by inserting SD card and then immediately checking the
output of "dmesg | tail". Or just use "usb image creator" on windows/linux etc. Detailed instructions can be found on RPI website:

Linux: https://www.raspberrypi.org/documentation/installation/installing-images/linux.md

Windows: https://www.raspberrypi.org/documentation/installation/installing-images/windows.md

Mac: https://www.raspberrypi.org/documentation/installation/installing-images/mac.md

3. Insert SD card into Dreamcatcher board slot marked "SD0_OS," next to LED array.
Make sure wifi dongle is also inserted already. Attach uart (optional, only needed if you have trouble booting). Boot dreamcatcher by powering it using microusb. It should start booting as soon as power is applied. If not, press the "power" button for a couple seconds.

4.

  a. On uart, you should arrive at regular "Dreamcatcher login:" prompt. If you see anything related to "panic", rebooting, etc, instead of the login prompt, and board keeps rebooting,
your sdcard is corrupt and you need to burn it again. Or use another card. Make sure you use atleast 4GB sdcard, and a fast, good quality one.

  b. The image also supports console on usb-serial. This is the easiest way to initially setup the board after burning a new sdcard. Insert the OS sdcard and connect the board via microusb to your Laptop/PC. Once the board finishes booting up (takes about a minute), you should be able to see ttyACM0 device (on linux). You can access it using any serial terminal programs like picocom, minicom, screen or putty (on windows, for example).

5. Use "root" as username and "1234" as password. On first login, it will ask for new password and change root password. It will also ask for a new username to create a regular user, and for user password and other info. You can just press "enter" for the other info (but not for the  new username or password). This user is what you should login as normally.
Recommend you use outernet and outernet as username/password for this, so you don't forget.
(unless you plan to expose the board to outside your Lan, in which case use more secure settings!)


6. To connect to wifi network, use command line:

    nmcli d wifi connect your_ssid password your_password

or using the text UI:

    nmtui


to check assigned ip use:

    /sbin/ifconfig

SSID/password, once configured, will be stored, and should be applied automatically at next boot.

7. To install librtlsdr and sdr utilities like rtl_fm etc, use:

    apt update; apt install rtl-sdr

(you will need to have already connected to network for this to work)

8. Note that unlike Skylark, the root filesystem here is modified and is writeable, so it is important you shut this down properly:

    sudo poweroff

Chances of corrupting your sdcard are much higher than in skylark. Use a good sdcard.

9. This is regular debian. apt update, apt upgrade, etc should all work as normal.


10. LEDs: The leds from the SMA connector down to Sdcard slot, in order:

  a. USB HUB Enable: green LED, on when the usb hub is on. On by default at boot.
to turn off:

    echo 0 | sudo tee /sys/class/leds/usbhuben/brightness

(this will also turn off the usb hub: no rtlsdr, no wifi!)

  b. Packet indicator: flashes when Outernet packets are received. Unused under armbian. Default off.

  c. Lock indicator: On when there is a signal lock. Unused under Armbian. Default off.

  d. SNR1, SNR2, SNR3, SNR4: SNR indicators, unused under Armbian. Default off.

  e. USR1: sdcard activity indicator. equivalent of the HDD activity light on laptops/desktops

  f. USR2: heartbeat: flashes when kernel is booted and working normally. The flash pattern is:

long off, N short flashes, long off, repeat.

    N = 2 * loadavg.

  g. Power: White LED, on when board is powered.

11. The default armbian image enables the "LNA bypass" RF input port, to allow usage as a normal, unfiltered RTLSDR.
to switch to the Outernet LNA (L Band) RF input:

    echo 0 | sudo tee /sys/class/leds/rfswitch1/brightness
    echo 1 | sudo tee /sys/class/leds/rfswitch2/brightness
