# Ubuntu Configuration

## How do I set the timezone?

``` bash
timedatectl
timedatectl set-timezone Europe/Berlin
```

Sources:

- <https://linuxize.com/post/how-to-set-or-change-timezone-on-ubuntu-18-04>

## How can I permanently disable wifi?

The best way is via the package rfkill

``` bash
aptitude install -y rfkill
```

It works like a switch and disables wifi on boot

``` bash
rfkill block wifi
```

## How to deactivate bluetooth at start?

We enter a softblock for the corresponding function in the file rc.local:

``` bash
vi /etc/rc.local
```

Add following line

``` bash
rfkill block bluetooth
```

Rfkill is a command to query or change the status of switches in the system. If there is no file then:

``` bash
sudo install -b -m 755 /dev/stdin /etc/rc.local < EOF
  #!/bin/sh
  rfkill block bluetooth
  exit 0
  EOF
```

Sources:

- <https://itsfoss.com/turn-off-bluetooth-by-default-in-ubuntu-14-04/>
- <https://askubuntu.com/questions/67758/how-can-i-deactivate-bluetooth-on-system-startup>

## SSH

### Config

Neuerdings verwende ich keepassxc SSH-Agent für die Verwaltung. Damit das funktioniert muss(!) Keepass aus dem ppa installiert werden. Außerdem muss unter "Startup Applications" Der Gnome Keyring SSH - Agent abgestellt werden. Sonst sind die keys nicht da.

nicht vergessen die Hosts einmal mittels "ssh key" zu aktivieren bzw. in known_host zu hinterlegen, sonst funktionieren die Scripts später nicht!

```conf
Host raspi
  HostName {{ HOST_IP }}
  port 22
  user {{ USER }}
  IdentityFile {{ SSH_KEY_PATH }}
```

## Network

### How do I connect to WLAN on Ubuntu?

Do

``` bash
vi /etc/netplan/50-cloud-init.yaml
```

insert:

``` yaml
wifis:
  wlan0:
    dhcp4: true
    optional: true
    access-points:
      "{{ SSID_NAME }}":
          password: "{{ WIFI_PASSWORD }}"
```

Generate the netplan below

``` bash
netplan generate
```

and apply

``` bash
netplan apply
```

If errors occur here, a missing wpa supplicant service may be the problem

``` bash
systemctl start wpa_supplicant
```

then apply again and if that does not work:

``` bash
systemctl reboot
```

Sources:

- <https://itsfoss.com/connect-wifi-terminal-ubuntu/>

### How do I set a static IP?

Set Static IP via netplan:

``` bash
vi /etc/netplan/01-netcfg.yaml
```

insert:

``` yaml
network:
 version: 2
 renderer: networkd
 ethernets:
  eth0:
    dhcp4: no
    addresses:
      - {{ FIXED_IP }}/24
    gateway4: {{ GATEWAY_IP_ADDRESS}}
    nameservers:
      addresses: [{{ DNS_IP_ADDRESS }}]
```

Generate the netplan below

``` bash
netplan generate
```

and apply

``` bash
netplan apply
```

Sources:

- <https://www.techrepublic.com/article/how-to-configure-a-static-ip-address-in-ubuntu-server-18-04/>
- <https://www.snel.com/support/how-to-configure-ipv6-with-netplan-on-ubuntu-18-04/>
- <https://help.ubuntu.com/lts/serverguide/network-configuration.html#ip-addressing>

## User control

### Create a user and give it sudo

Always create non root user and change password of root

``` bash
passwd root
adduser {{ USER }}
```

Add the new user to sudoer group

``` bash
usermod -aG sudo {{ USER }}
```

### set up passwordless sudo

Activate passwordless sudo

``` bash
echo "{{ USER }} ALL=(ALL:ALL) NOPASSWD:ALL" >> /etc/sudoers
```

## Security

## Network hardening

Use a firewall like ufw or firewalld. Turn in on and block all ports.

### SSH hardnening

Once you have authenticated the new key, deactivate non key authentication(use extended Regex -E option):

``` bash
sed -i -E -e "s:#?PasswordAuthentication.*:PasswordAuthentication no:" -e "s:#?ChallengeResponseAuthentication.*:ChallengeResponseAuthentication no:" /etc/ssh/sshd_config
```

Restart Service:

``` bash
systemctl restart sshd
```

## Backup

### How to use Rsync

rsync has many different options. A useful one is -h (human readable)

``` bash
rsync -h
```

This streamlines the file paths so that only the currently transferred file is included and not the whole path. It also converts the amount of data transferred instead of outputting bytes.

Important learning on my side. The z option makes no sense in most cases without rceiving rsync. Because there is a rsync protocol in HIdrive: Probably useful, but not at all for e.g. local or on USB.

``` bash
rsync -z
```

if granular logging with timestamps is desired, the following notation can be chosen.

``` bash
rsync --out-format="%t %f %b"
```

the exclude option is always relative to the source path (don't always choose the a option, often t(timestamps preserve) r(recursive) is enough). Preserving permissions can also quickly lead to errors).

``` bash
rsync --delete -azvhr --exclude '{{ FOLDER_TO_EXCLUDE }}' {{ SOURCE_FOLDER }} {{ SOURCE_FOLDER_2 }} {{ REMOTE_NAME }}:{{ SINK_FOLDER }}
```

Very useful is also the stats option together with h

``` bash
rsync --delete -azvhr --stats --exclude '{{ FOLDER_TO_EXCLUDE }}' {{ SOURCE_FOLDER }} {{ SOURCE_FOLDER_2 }} {{ REMOTE_NAME }}:{{ SINK_FOLDER }}
```

Cron can help with scheduling.

### Use borgbackup

For repairing borgbackup repository in case of power loss:

``` bash
sudo borg -p -v check --repair --bypass-lock {{ BORGBACKUP_FOLDER }}
```

to remove a lock

``` bash
borg break-lock {{ BORGBACKUP_FOLDER }}
```

Sources:

- <https://borgbackup.readthedocs.io/en/stable/usage/check.html>
- <https://borgbackup.readthedocs.io/en/stable/usage/lock.html>

## Cron

``` bash
crontab -e
```

Sources:

- <https://askubuntu.com/questions/173924/how-to-run-a-cron-job-using-the-sudo-command>
- <https://www.linode.com/docs/tools-reference/tools/schedule-tasks-with-cron/>
- <https://crontab.guru>

## 3.1. How to log Cronjob output?

How to write output to a file:

```conf
00 10 ** * rsync --delete -azvr {{ SOURCE_FOLDER }} {{ SOURCE_FOLDER_2 }} {{ REMOTE_NAME }}:{{ SINK_FOLDER }} >> {{ LOG_PATH }} 2>&1
```

use brackets for multiple commands

``` bash
(rsync -azv test test && rsync -azvr test test) >> {{ LOG_PATH }} 2>&1
```

Sources:

- <https://stackoverflow.com/questions/4811738/how-to-log-cron-jobs>
- <https://stackoverflow.com/questions/818255/in-the-shell-what-does-21-mean>
- <https://unix.stackexchange.com/questions/52330/how-to-redirect-output-to-a-file-from-within-cron>
- <https://askubuntu.com/questions/936710/write-to-file-output-of-two-commands-running-together-in-one-terminal>

### How to log Cron to Postfix/SSMTP respectively email address?

See msmtp instructions

### How do I use locks and avoid duplicates of the same cron job??

```conf
* * * * * flock -n {{ PATH_TO_LOCKFILE }} {{ COMMAND }}
```

or if shell chains are to be used

```conf
* * * * * flock -n {{ PATH_TO_LOCKFILE }} -c {{ COMMAND }} && {{ COMMAND }}
```

Sources:

- <https://serverfault.com/questions/82857/prevent-duplicate-cron-jobs-running>

## Firmware, Kernel and GRUB

### How does the GRUB menu work?

By default, GRUB will show the menu if there is a second operating system installed. If only Ubuntu is installed, then GRUB will generally load Ubuntu without showing the menu(unless u press shift/esc etc.). To reconfigure GRUB to always show a menu:

``` bash
vi /etc/default/grub
```

```conf
Set GRUB_HIDDEN_TIMEOUT= # (no value after the = sign).
Set GRUB_TIMEOUT=n # to show the menu for n seconds.
```

``` bash
update-grub 
```

to regenerate /boot/grub/grub.cfg based on the /etc/default/grub settings.

Sources:

- <https://askubuntu.com/questions/16042/how-to-get-to-the-grub-menu-at-boot-time>

### How do I set an old kernel as default in GRUB?

The best solution for me was to set (in /etc/default/grub):

```conf
GRUB_DEFAULT=saved
GRUB_SAVEDEFAULT=true
```

``` bash
sudo update-grub
```

Sources:

- <https://www.gnu.org/software/grub/manual/grub/grub.html#Configuration>
- <https://askubuntu.com/a/1000735>

## How do I change the default the location of user folders ?

``` bash
vi ~/.config/user-dirs.dirs
```

Each row is a user folder definition (music, video...), edit as you want. For example, I did not want the videos folder in home, but in a separate disk, and changed the XDG_VIDEOS_DIR parameter this way

Bear in mind in particular that, if at startup it is found that the XDG_DOCUMENTS_DIR path actually doesn't lead to a valid directory, the setting for XDG_DOCUMENTS_DIR will be overwritten in user-dir.dirs, replacing it, in principle, with $HOME/Documents

restart nautilus!
in simpler solution could be to replace the document folder with a symlink.

``` bash
ln -s ~/target/folder ~/Link_name 
```

last for creation in current directory
the *s* stands for symbolic  

Sources:

- <https://askubuntu.com/questions/67044/change-default-user-folders-path>
- <https://askubuntu.com/questions/56339/how-to-create-a-soft-or-symbolic-link>

## How do I perform a firmware update of the WiFi and how do I restart the driver?

check Firmware under:

``` bash
sudo ethtool -i {{ WIFI_NAME }}
```

Easily restart Wifi without restart computer

``` bash
sudo lshw -C network
sudo modprobe -r iwlwifi && sudo modprobe iwlwifi
```

Sources:

- <https://askubuntu.com/questions/26054/how-to-restart-wifi-interface-without-rebooting-it-drops-connection>
- <https://wireless.wiki.kernel.org/en/users/drivers/ath10k/debug#firmware_version>


## How to deal with packages have been kept back in apt?

Sources:

- <https://askubuntu.com/questions/601/the-following-packages-have-been-kept-back-why-and-how-do-i-solve-it>
