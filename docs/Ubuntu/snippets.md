# Ubuntu Snippets

## How do I remove desktop shortcuts from lets say appimages?

``` bash
ls ~/.local/share/applications/
```

Sources:

- <https://stackoverflow.com/questions/43680226/how-can-i-uninstall-an-appimage>

## Test .desktop files

just run:

``` bash
gtk-launch {{ PATH_TO_DESKTOP_FILE }}
```

Sources:

- <https://askubuntu.com/questions/5172/running-a-desktop-file-in-the-terminal>

## How to wait for mount before service is started?

Use

``` bash
ConditionPathIsMountPoint={{ MOUNTPOINT_PATH }}
```

To let a a service wait for a folder plus checking if it is a mountpoint.

Sources:

- <https://unix.stackexchange.com/questions/281650/systemd-unit-requiresmountsfor-vs-conditionpathisdirectory>

## How do I set up and flush the DNS?

``` bash
sudo systemd-resolve --flush-caches
sudo systemd-resolve --statistics
resolvectl flush-caches 
```

Sources:

- <https://askubuntu.com/questions/906476/how-can-i-flush-the-dns-on-ubuntu-17-04>
- <https://askubuntu.com/questions/1404586/how-to-clear-dns-cache-in-22-04>

## How to turn LEDs off in raspberry pi or change hadware functions

You can use the /boot/firmware/config.txt for that. It will allow you to alter the so called device tree which is a manual for the kernel on what to activate how to treat different parts of the hardware.

Example how to turn off wifi and bluetooth:

```conf
[all]
dtoverlay=disable-bt
dtoverlay=disable-wifi
```

or turn off lan and activity leds:

```conf
[all]
dtparam=eth_led0=4
dtparam=eth_led1=4
dtparam=act_led_trigger=none
```

Sources:

- <https://github.com/raspberrypi/firmware/blob/master/boot/overlays/README>
- <https://github.com/seamusdemora/PiFormulae/blob/master/LEDControlForRPi.md>
- <https://stackoverflow.com/questions/19863723/turn-off-leds-of-raspberry-pi>
- <https://raspberrypi.stackexchange.com/questions/70593/turning-off-leds-on-raspberry-pi-3>
