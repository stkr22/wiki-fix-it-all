# Terminal Advanced

## How do I retrieve the unique cpu signatur id?

Install hwinfo:

``` bash
sudo apt-get install hwinfo
```

Retrieve info and look for Unique ID:

``` bash
hwinfo --cpu
```

Sources:

- <https://linuxhandbook.com/check-cpu-info-linux/>

## How do I set a env variable for all users?

``` bash
echo "export BORG_PASSPHRASE={{ PASSPHRASE }}" >> /etc/profile
```

Sources:

- <https://stackoverflow.com/questions/1641477/how-to-set-environment-variable-for-everyone-under-my-linux-system>

## How do I grab a env variable from a remote machine with ssh?

ssh runs a non interactive shell and thus the number of env variables can be significantly smaller then in normal mode. I started to simply use:

``` bash
ssh oph "source /etc/profile; printenv | grep BORG | rev | cut -d '=' -f 1 | rev"
```

Sources:

- <https://superuser.com/questions/952084/why-is-ssh-not-invoking-bash-profile>
- <https://stackoverflow.com/questions/43775801/use-environment-variables-of-remote-server-in-ssh-command>
- <https://stackoverflow.com/questions/216202/why-does-an-ssh-remote-command-get-fewer-environment-variables-then-when-run-man>

## How to find which devices are connected to network in Linux?

``` bash
sudo aptitude install nmap
```

lese IP Range ab

``` bash
ip a
```

setze scan parameter nach CIDR notation

``` bash
sudo nmap -sn 192.168.178.0/24
```

Sources:

- <https://itsfoss.com/how-to-find-what-devices-are-connected-to-network-in-ubuntu/>

## How do I find connected and unmounted devices or devices ?

### Regular unencrypted

You can also use the lsblk command (list block devices) which lists all block devices attached to your system. To view each device attached to your system as well as its mount point, you can use the df command (checks Linux disk space utilization) as shown in the image below:

``` bash
lsblk
df -h
```

for mounting

``` bash
sudo mkdir {{ PATH_TO_MOUNTPOINT }}
sudo mount /dev/sdb1 {{ PATH_TO_MOUNTPOINT }}
```

Sources:

- <https://www.tecmint.com/find-usb-device-name-in-linux/>
- <https://askubuntu.com/questions/285539/detect-and-mount-devices>

### How to mount encrypted devices ?

First unlock device:

``` bash
sudo udisksctl unlock -b /dev/sdb1
```

now mount it using the unlock endpoint:

``` bash
ls -la /dev/mapper
sudo mount /dev/dm-0 {{ PATH_TO_MOUNTPOINT }}
```

Sources:

- <https://askubuntu.com/questions/63594/mount-encrypted-volumes-from-command-line>

### How to mount an encrypted LUKS device automatically?

First list all devices with luks

``` bash
lsblk --fs
```

List all available keys for the device and add a new one

``` bash
cryptsetup luksDump /dev/sdb1
cryptsetup luksAddKey /dev/sdb1
```

Simply enter the correct passphrase if asked for existing passphrase. Validate with

``` bash
cryptsetup luksDump /dev/sdb1
```

Create a lukskey than can be used to automount the device at boot. We will first create a new key with random content. Next we will fill it with the real key.

``` bash
dd if=/dev/random bs=32 count=1 of=/root/lukskey
chmod 0600 /root/lukskey
cryptsetup luksAddKey /dev/sdb1 /root/lukskey
cryptsetup luksDump /dev/sdb1
```

create new cryttab entry (watchout to choose correct mountpoint "backup" in /etc/fstab)

``` bash
cryptsetup luksDump /dev/sdb1 | grep "UUID"
echo "sdb1_crypt UUID={{ MY_UID }}   /root/lukskey" >> /etc/crypttab
```

create new fstab entry and mountpoint

``` bash
echo "/dev/mapper/sdb1_crypt      {{ PATH_TO_MOUNTPOINT }}   ext4    defaults    rw,user,exec     0    0" >> /etc/fstab
mkdir /mnt/backup
chmod 777 /mnt/backup
```

TO test:

``` bash
cryptdisks_start sdb1_crypt
mount {{ PATH_TO_MOUNTPOINT }}
```

Sources:

- <https://bbs.archlinux.org/viewtopic.php?id=164798>
- <https://www.golinuxcloud.com/mount-luks-encrypted-disk-partition-linux/>
- <https://blog.tinned-software.net/automount-a-luks-encrypted-volume-on-system-start/>

## How do I find a process and its consumption?

``` bash
ps aux | grep {{ PROCESS_YOU_LOOK_FOR }}
```

## How to check if env exist or set it to default if not exist?

Simply use

``` bash
"${MYVAR:-default_value}"
```

for check use:

``` bash
if ["${MYVAR:-default_value}" = "default_value"]
```

## How do I determine the number / size of all files in a folder?

Option a for all files and subfolders

``` bash
du -sh * | sort -hr
```

Size in the sense of quantity

``` bash
find . -type d | wc -l
```

for specific file types

``` bash
find {{ PATH_TO_FOLDER }} -mindepth 1 -type f -name "*.mp4" -printf x | wc -c
```

Important if

> The problem seemed to be is that du insists on reporting the disk usage of directories and files, and I wanted only the size of files.
> So, since creating a few directories will use more bytes on some filesystems, and less on others, you get a difference.

Sources:

- <https://stackoverflow.com/questions/1019116/using-ls-to-list-directories-and-their-total-sizes>
- <https://www.shellhacks.com/count-number-files-directory/>
- <https://askubuntu.com/questions/454564/count-total-number-of-files-in-particular-directory-with-specific-extension/454568>

## How to copy a file from a to b via SSH or SCP?

``` bash
scp -r {{ REMOTE }}:{{ SOURCE_FOLDER }} {{ SINK_FOLDER }}
```

The *r* option stands for recursive

## How to check listeners on ports?

``` bash
netstat -tunlp
```

oder neuere Variante

``` bash
sudo ss -tunlp
```

Sources:

- <https://linuxize.com/post/check-listening-ports-linux/#check-listening-ports-with-ss>
