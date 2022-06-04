# PiHole

## How do I install PiHole ?

### Revision 1

Use docker pihole/pihole:latest

Exact:

> {{ URL }}
> {{ URL }}

Wildcard

> {{ URL_WITH_WILDCARD }}

Also important after the update do not forget the Gravity:

``` bash
docker container exec pihole_app pihole updateGravity
```

There may be problems with the LG App Store and EULA Agreements / signing if ngfts.lge.com is blocked. Then allow once and unblock again (after restarting the device).

For Windows Spy Protection in Admin Panel under settings > blocklists add:

- <https://raw.githubusercontent.com/crazy-max/WindowsSpyBlocker/master/data/hosts/spy.txt>

### Original

Load script

``` bash
curl -sSL https://install.pi-hole.net | bash
```

During the installation write down the IPs for the Fritzbox. LightPd must not be installed if later or earlier Nginx is used as a server is used. Specify no in the corresponding question! Do not accept the firewall rules! The script writes these directly into the IpTables and this will confuse ufw later. In the Fritzbox store the then displayed IPs and uncheck the checkbox always assign the same IP to this device under Home > Network Important regarding firewall ufw, port 53 must be open!

``` bash
ufw allow 53
```

to change DNS note point 4.3 at Kuketz Blog Link. Important, if this is selected, then the DNS must be adjusted separately for IPv6 and IPv4 at the Fritzbox as described in 4.3!

Sources:

- <https://www.kuketz-blog.de/pi-hole-schwarzes-loch-fuer-werbung-raspberry-pi-teil1/>
- <https://docs.pi-hole.net/guides/nginx-configuration/>
- <https://www.raspberrypi.org/forums/viewtopic.php?t=246376>
- <https://www.kuketz-blog.de/nacharbeiten-pi-hole-in-kombination-mit-pivpn/>
- <https://discourse.pi-hole.net/t/resolved-recent-ubuntu-update-causes-strange-dns-issue/20449>

Issues:

- <https://stackoverflow.com/questions/32100834/how-to-set-php-ini-settings-in-nginx-config-for-just-one-host>
- <https://coderwall.com/p/gt2g3q/setting-multiple-php_value-in-nginx-config>
- <https://stackoverflow.com/questions/3829403/how-to-increase-the-execution-timeout-in-php/3829437#3829437>

-------------------- Ende original

Sources:

- <https://github.com/crazy-max/WindowsSpyBlocker/>
- <https://www.reddit.com/r/pihole/comments/6qmpv6/blacklists_for_lg_webos_tvs/>

### Blocklist for pihole

Use:

- <https://raw.githubusercontent.com/crazy-max/WindowsSpyBlocker/master/data/hosts/spy.txt>
- <https://raw.githubusercontent.com/FadeMind/hosts.extras/master/add.Spam/hosts>
- <https://raw.githubusercontent.com/PolishFiltersTeam/KADhosts/master/KADhosts.txt>
- <https://v.firebog.net/hosts/Easyprivacy.txt>
- <https://v.firebog.net/hosts/AdguardDNS.txt>
- <https://adaway.org/hosts.txt>
- <https://www.github.developerdan.com/hosts/lists/ads-and-tracking-extended.txt>

Sources:

- <https://www.reddit.com/r/pihole/comments/hpfajt/best_blocklists/>
- <https://firebog.net/>