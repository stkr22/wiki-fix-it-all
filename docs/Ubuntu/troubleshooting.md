# Ubuntu Troubleshooting

## Raspi ubuntu server - usb boot

Don't use Ubuntu server > 21.04 for usb boot

Sources:

- <https://medium.com/xster-tech/move-your-existing-raspberry-pi-4-ubuntu-install-from-sd-card-to-usb-ssd-52e99723f07b>
- <https://www.raspberrypi.org/software/>
- <https://ubuntu.com/tutorials/how-to-install-ubuntu-on-your-raspberry-pi#2-prepare-the-sd-card>

## How to fix the locale issue

First run locale to list what locales currently defined for the current user account:

``` bash
locale
```

Then generate the missing locale and reconfigure locales to take notice:

important select with spacebar in the generate locale
probably the problem is that it sets the locale to de but there is none

``` bash
sudo locale-gen 'en_US.UTF-8'
export LANGUAGE=en_US.UTF-8
export LC_ALL=en_US.UTF-8
export LANG=en_US.UTF-8
```

or try to generate them, i.e. de_DE.UTF-8

``` bash
sudo dpkg-reconfigure locales
```

Sources:

- <http://www.iasptk.com/ubuntu-fix-locale-issue/>

## Inotify limit exceeded - wie finde ich raus, wer die aufbraucht und erhöhe das limit?

In letzter Zeit gab es öfters das Problem, das die Inotify Limits exceeded wurden. Inotify ist ein Dienst von Linux, der die Überwachung von Dateien auf der Festplatte anbietet und das Programm benachrichtigt, wenn eine Änderung vorliegt.

Dieses Skript erlaubt es alle Verbraucher von intotify anzuzeigen

``` bash
#!/bin/sh
lines=$(
  find /proc/*/fd \
  -lname anon_inode:inotify \
  -printf '%hinfo/%f\n' 2>/dev/null \
  \
  | xargs grep -c '^inotify'  \
  | sort -n -t: -k2 -r \
)
printf "\n%10s\n" "INOTIFY"
printf "%10s\n" "WATCHER"
printf "%10s  %5s     %s\n" " COUNT " "PID" "CMD"
printf -- "----------------------------------------\n"
for line in $lines; do
  watcher_count=$(echo $line | sed -e 's/.*://')
  pid=$(echo $line | sed -e 's/\/proc\/\([0-9]*\)\/.*/\1/')
  cmdline=$(ps --columns 120 -o command -h -p $pid) 
  printf "%8d  %7d  %s\n" "$watcher_count" "$pid" "$cmdline"
done
```

Sources:

- <https://github.com/fatso83/dotfiles/blob/master/utils/scripts/inotify-consumers>
- <https://unix.stackexchange.com/questions/498393/how-to-get-the-number-of-inotify-watches-in-use>
- <https://stackoverflow.com/questions/13758877/how-do-i-find-out-what-inotify-watches-have-been-registered>

## How to fix DNS resolver issues? (Ubuntu 20.04)

I had a lot of problems with Ubuntu not resolving websites and packages. The issue seems to be that it uses the local domain (fritz.box) for some addresses which leads to NXDOMAIN. No clue why.

It seem to suffice to

``` bash
vi /etc/systemd/resolved.conf
```

change line

```conf
#DNSStubListener=yes
```

to

```conf
> DNSStubListener=no
```

Restart service

``` bash
systemctl enable systemd-resolved.service && systemctl start systemd-resolved.service
```

Currently trying to disable systemd-resolver all together. So far works

``` bash
systemctl disable systemd-resolved.service && systemctl stop systemd-resolved.service
vi /etc/NetworkManager/NetworkManager.conf
```

add new line in

```conf
[main]
dns=default
```

``` bash
rm /etc/resolv.conf
systemctl restart network-manager
```

Recreate with:

``` bash
vi /etc/NetworkManager/NetworkManager.conf
systemctl enable systemd-resolved.service && systemctl start systemd-resolved.service
rm /etc/resolv.conf
sudo ln -s /run/resolvconf/resolv.conf /etc/resolv.conf
systemctl restart network-manager
```

Sources:

- <https://gist.github.com/zoilomora/f7d264cefbb589f3f1b1fc2cea2c844c>
- <https://askubuntu.com/a/1195074>

## How to fix missing bluetooth microphone?

This works

``` bash
pulseaudio --kill && pulseaudio --start
```

Deactivate

``` bash
{{ PATH_TO_MODEM_BINARY }} /phonesim
pulseaudio --kill && pulseaudio --start
pulseeffects --quit && pulseeffects &
```

Sources:

- <https://askubuntu.com/questions/952342/cant-change-profile-to-hsp-for-bluetooth-headset?noredirect=1&lq=1>
- <https://askubuntu.com/a/1242489>
- <https://askubuntu.com/a/1301127>
- <https://askubuntu.com/a/1236379>

## Wie löse ich Error starting userland proxy: listen tcp 0.0.0.0:53: bind: address already in use?

Might happen because of systemd-resolv, see bash on how to check ports.  

Sources:

- <https://www.linuxuprising.com/2020/07/ubuntu-how-to-free-up-port-53-used-by.html>

## Fixing the upower constantly showing notification about low power devices (Ubuntu 20.04)

Sources:

- <https://wrgms.com/disable-mouse-battery-low-spam-notification/>
- <https://askubuntu.com/questions/985963/disable-mouse-battery-low-spam-notification>

## Find printer with hplip

for finding the printer I had to disable ufw once and enable it again

Sources:

- <https://developers.hp.com/hp-linux-imaging-and-printing/>
