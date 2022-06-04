# Firewall

## UFW

### Basics

using ufw

``` bash
sudo ufw enable 
sudo ufw allow 8080
```

some apps offer presets for ufw like

``` bash
sudo ufw app list
sudo ufw allow calibre
```

Sources:

- <https://wiki.ubuntu.com/BasicSecurity>
- <https://help.ubuntu.com/lts/serverguide/firewall.html>

### Wie aktiviere ich logging für ufw?

einfach

``` bash
ufw logging on
```

Logs sind dann in /var/log/ufw

Sources:

- <https://serverfault.com/questions/516838/where-are-the-logs-for-ufw-located-on-ubuntu-server>

## Network Security with Firewalld

``` bash
firewall-cmd --list-all --zone=public
firewall-cmd --get-zone-of-interface=eth0
firewall-cmd --permanent --zone=external --change-interface=eth0
firewall-cmd --remove-interface=eth0 --zone=external
firewall-cmd --list-services --zone=external
firewall-cmd --reload
```

Sources:

- <https://www.redhat.com/sysadmin/firewalld-zones-and-rules>
- <https://unix.stackexchange.com/questions/559980/cant-remove-interface-from-zone-with-networkmanager-enabled-firewalld-cent>
