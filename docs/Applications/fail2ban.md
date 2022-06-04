# Fail2Ban

## Revision 1 with docker

Switched to docker, building on crazymax fail2ban docker. But instead of sendmail I use mailx and instead of ssmtp I use msmtp. In alpine mailx is called as "mail" that is why

```conf
mta = mail
```

in the jail.config

Also please note that to refer to an action you always need to use

```conf
%(action_)%s[Parameter....]
```

Where [Parameters] is optional.

Unbans are done via

``` bash
docker container exec fail2ban fail2ban-client set MYJAIL unbanip IP
```

check which bans are currently in place with:

``` bash
iptables -n -L
```

## Original

Originally I used fail2ban on server directly. But this also has the downside of fail2ban being outdated.

Sources:

- <https://www.digitalocean.com/community/tutorials/how-fail2ban-works-to-protect-services-on-a-linux-server>
- <https://visei.com/2020/05/incremental-banning-with-fail2ban/>
- <https://github.com/crazy-max/docker-fail2ban>
- <https://stackoverflow.com/questions/60694033/mailx-not-found-in-alpine>
- <https://decatec.de/home-server/nextcloud-auf-ubuntu-server-18-04-lts-mit-nginx-mariadb-php-lets-encrypt-redis-und-fail2ban/#E-Mail-Versand_durch_Fail2ban>
- <https://github.com/fail2ban/fail2ban>
- <https://unix.stackexchange.com/questions/286119/delete-all-fail2ban-bans-in-ubuntu-linux>