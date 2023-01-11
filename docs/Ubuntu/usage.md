# Ubuntu Usage

## How to mount things?

Generally we differentiate between devices(/dev/), removable media(/media) and admin mountpoints(/mnt). Admin mountpoints are meant for temporary mounts that should not be visible in programs like Nautilus. /media are used by the system to e.g. auto mount external hard drives. /dev is used for internal devices like CD/DVD or internal hard drives.

Sources:

- <https://help.ubuntu.com/community/Mount>

## 4.1. DAVFS

The generic dav protocol via gfvs is very slow and also leads to problems with Cryptomator as it needs write access, which is stupid with mount via admin.

First davfs2 must be installed

``` bash
sudo aptitude install davfs2
```

The best way is to use the -o option to mount the drive with the user to be written as owner. This is less of a security risk than allowing the user to mount in general.

Is a fuse mount for DAV/WebDAV.

``` bash
sudo mount -t davfs -o uid={{ USER }}  {{ TARGET_IP }}:443 {{ MOUNTPOINT_PATH }}
sudo mount -t davfs -o uid={{ USER }}  {{ TARGET_URL }}:443 {{ MOUNTPOINT_PATH }}
```

## 4.2. SSHFS

SSHFS is a fuse mount for SSH. This allows us to mount a filesystem via ssh.

``` bash
sudo sshfs -o allow_other,default_permissions,IdentityFile={{ KEY_PATH }} -p 2222 {{ USER_NAME }}@{{ SERVER_IP_OR_URL }}:/ {{ SINK_FOLDER }}
```

Sources:

- <https://www.digitalocean.com/community/tutorials/how-to-use-sshfs-to-mount-remote-file-systems-over-ssh>

## Allow users to mount

Then edit fstab to allow mounting also for a normal user

``` bash
sudo nano /etc/fstab
```

insert:

```conf
http://{{ TARGET_IP }}:443 {{ MOUNTPOINT_PATH }} davfs user,noauto,rw 0 0
```

Then add the user to the appropriate group:

``` bash
sudo usermod -a -G davfs2 {{ USER }}
```

groups # if not yet current with

``` bash
su -l {{ USER }}
```

user can now mount by using:

``` bash
mount {{ MOUNTPOINT_PATH }}
```

Sources:

- <https://github.com/cryptomator/cryptomator/issues/696>
- <https://community.cryptomator.org/t/cannot-open-storage-entsperren-fehlgeschlagen/433>
- <https://www.systutorials.com/136297/control-filesystem-mounting-on-linux-by-playing-with-etcfstab/#allow-non-root-users-to-mount-and-unmount-filesystems>
- <https://askubuntu.com/questions/498492/mounting-a-webdav-share-for-all-users>
- <https://blog.sleeplessbeastie.eu/2017/09/04/how-to-mount-webdav-share/>
- <https://wiki.ubuntuusers.de/WebDAV/>
- <https://www.systutorials.com/docs/linux/man/8-mount.davfs/>

## Why use flatpaks?

Flatpaks give an alternative to the classic debs or package distribution systems. A big advantage is that all dependencies are already included in the flatpak. Especially for programs that I only use over and over again like GIMP or QGis a gain.

``` bash
aptitude install flatpak gnome-software-plugin-flatpak
flatpak remote-add --if-not-exists flathub https://flathub.org/repo/flathub.flatpakrepo
```

Sources:

- <https://flatpak.org/setup/Ubuntu/>

## How to set up thunderbolt devies

``` bash
boltctl list
boltctl forget {{ MYDEVICEID }}
boltctl enroll --policy auto {{ MYDEVICEID }}
```

Sources:

- <https://christian.kellner.me/2019/02/11/thunderbolt-preboot-access-control-list-support-in-bolt/>
- <https://blog.karssen.org/2021/01/05/using-the-lenovo-thunderbolt-3-essential-dock-with-linux/>

## Wie verwende ich systemd units richtig?

Sources:

- <https://www.digitalocean.com/community/tutorials/understanding-systemd-units-and-unit-files>

## Securely erase ssds

```bash
sudo hdparm -I /dev/sdX | grep -i erase
```

Soures:

- <https://askubuntu.com/questions/794612/how-to-securely-wipe-files-from-ssd-drive#795756>
- <https://www.thomas-krenn.com/en/wiki/Perform_a_SSD_Secure_Erase>
- <https://9to5tutorial.com/secure-erase-ssd-in-ubuntu>
