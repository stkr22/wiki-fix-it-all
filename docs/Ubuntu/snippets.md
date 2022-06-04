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
