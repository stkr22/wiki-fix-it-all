# Cloudflare

## Wie richte ich einen proxy DNS für DNS Over HTTPS(DOH) ein?

### Revision 2

Sources:

- <https://developers.cloudflare.com/1.1.1.1/dns-over-https/cloudflared-proxy#dnscrypt-proxy>
- <https://github.com/DNSCrypt/dnscrypt-proxy>
- <https://github.com/pi-hole/pi-hole/wiki/DNSCrypt-2.0>

### Revision 1

Zunächst die vorkompilierte binary runterladen (hier für arm64):

``` bash
wget https://github.com/cloudflare/cloudflared/releases/download/2020.11.11/cloudflared-linux-arm64 -O /usr/local/bin/cloudflared
```

Dann die Berechtigungen setzen:

``` bash
useradd -r -M -s /usr/sbin/nologin -c "Cloudflared user" cloudflared
chown cloudflared:cloudflared /usr/local/bin/cloudflared
chmod +x /usr/local/bin/cloudflared
```

und den Service erzeugen(/etc/systemd/system/cloudflared_dns.service):

```conf
[Unit]
Description=cloudflared DNS over HTTPS proxy
After=syslog.target network-online.target

[Service]
Type=simple
User=cloudflared
ExecStart=/usr/local/bin/cloudflared proxy-dns --port 5053 --upstream https://1.1.1.1/dns-query --upstream https://1.0.0.1/dns-query
Restart=on-failure
RestartSec=10
KillMode=process

[Install]
WantedBy=multi-user.target
```

Check mit:

<https://1.1.1.1/help>

Sources:

- <https://docs.pi-hole.net/guides/dns-over-https/>
- <https://www.cyberciti.biz/faq/configure-ubuntu-pi-hole-for-cloudflare-dns-over-https/>
- <https://developers.cloudflare.com/argo-tunnel/downloads/>
- <https://github.com/cloudflare/cloudflared/issues/115>
- <https://github.com/golang/go/wiki/Ubuntu>
- <http://manpages.ubuntu.com/manpages/trusty/pt/man5/host.5.html>

### Original

Erst einmal laden wir das aktuelle Interface von Cloudflare runter: dann
müssen wir es kompilieren auf dem normalen Gerät.

Dafür zunächst Golang installieren, wir brauchen dafür ein separates ppa
(machen wir natürlich auf unserem eigenem Rechner):

``` bash
snap install --classic go
```

Danach kompilieren:
Get the source

``` bash
go get github.com/cloudflare/cloudflared/cmd/cloudflared
```

Build for arm64

``` bash
GOOS=linux GOARCH=arm64 go build -x github.com/cloudflare/cloudflared/cmd/cloudflared
```
