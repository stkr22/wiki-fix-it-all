# MSMTP

## Set up

I use MSMTP because SSMTP is outdated and lo longer maintained. First install msmtp and mailutils

``` bash
aptitude install msmtp msmtp-mta mailutils
```

After that we need to configure the relevant config file:

``` bash
cat >> /etc/msmtprc <<EOL
    defaults
    auth           on
    tls            on
    tls_trust_file /etc/ssl/certs/ca-certificates.crt
    syslog         on

    # service
    account        service
    host           {{ SMTP_HOST }}
    port           587
    from           {{ EMAIL_ADRESS }}
    user           {{ USERNAME }}
    password       {{ PASSWORD }}

    # Set a default account
    account default : service
    aliases        /etc/aliases
    EOL
```

When checking docs on this keep in mind that ports might differ if you take into consideration that mail is a "relay" mail program. The permissions should be

``` bash
chmod 600 /etc/msmtprc
```

If you plan on sending mails to users later

``` bash
echo -e "Subject: Do you love alpine?\nYes, I do!\n" | msmtp root
```

Than do not forget to set up aliases in /etc/aliases properly!

To activate msmtp we need to change mail in ubuntu like:

``` bash
vi /etc/mail.rc
```

```conf
set sendmail="/usr/bin/msmtp -t"
```

Sources:

- <https://decatec.de/linux/linux-einfach-e-mails-versenden-mit-msmtp/>
- <https://wiki.archlinux.org/index.php/SSMTP>
- <https://wiki.alpinelinux.org/wiki/Relay_email_to_gmail_(msmtp,_mailx,_sendmail>
