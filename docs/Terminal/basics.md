# Terminal Basics

## How do I get a root shell?

The correct way is to use sudo -s (shell with your current environment) or sudo -i (login shell)

Sources:

- <https://askubuntu.com/questions/978300/how-do-i-set-environment-variable-for-all-users-even-when-doing-sudo-su>

## How do I use if in bash?

``` bash
if [ "$(iwgetid -r)" == "{{ WIFI_NAME }}" ]; then echo "true"; fi
```

um das eigentliche Problem mit dings zu lösen

``` bash
if [ -d "{{ MY_FOLDER }}" ]; then echo "true"; fi
```

ganz wichtig sind die **spaces nach und vor den eckigen Klammern**
Dies lässt sich auch auf remote systems anwenden:

``` bash
if [ "$(ssh {{ USER }}@{{ HOST }} ls -l {{ FOLDER_PATH }})" == "{{ SOME_FILE }}" ]; then ...
```

or even simpler use(bash):

``` bash
if ssh {{ REMOTE_NAME }} test -e {{ PATH_TO_TEST }} ; then echo "true"; fi
```

Achtung es gibt Unterschied zwischen sh und bash bei if z.B. akzeptiert bash "==" sh aber nur "=" zudem funktioniert `#!bash "$(iwgetid -r)" = "VF"` wunderbar in bash aber nur als `#!bash if [ $(iwgetid -r) = "{{ WIFI_NAME }}" ]` in sh. Das gilt insbesondere für das ausführen von commands, diese können in bash in "" gesetzt werden, in sh müssen sie innerhalb eines `#!bash $()` gesetzten werden.

Scheinbar dürfen commands die Booleans zurückgeben auch nicht in [...] gefasst werden.

sh:

``` bash
if [ $(iwgetid -r) = "{{ WIFI_NAME }}" ]
then 
if [ $(ssh {{ REMOTE_NAME }} "test -e {{ PATH_TO_TEST }}") ]
```

Sources:

- <https://www.cyberciti.biz/faq/howto-check-if-a-directory-exists-in-a-bash-shellscript/>
- <https://askubuntu.com/questions/117065/how-do-i-find-out-the-name-of-the-ssid-im-connected-to-from-the-command-line>
- <https://askubuntu.com/questions/612100/what-is-the-syntax-for-if-else-in-bash>
- <http://tldp.org/LDP/abs/html/comparison-ops.html>
- <https://askubuntu.com/questions/707785/command-to-compare-two-strings>
- <https://stackoverflow.com/questions/5725296/difference-between-sh-and-bash>
- <https://serverfault.com/questions/242176/is-there-a-way-to-do-a-remote-ls-much-like-scp-does-a-remote-copy>
- <https://stackoverflow.com/questions/12845206/check-if-file-exists-on-remote-host-with-ssh>

## Find

"-iname" for i flag bei find "-exec" führt alle commands nach dem ersten aus und übergibt parameter? Beendet mit "\;"

Sources:

- <https://www.cyberciti.biz/faq/linux-unix-how-to-find-and-remove-files/>

## How do I compress tar multiple folders and exclude some?

with the following everything fits

``` bash
tar -cvzf {{ TARGET_ARCHIVE }} --exclude='__pycache__'  {{ source_file}} {{ SOURCE_FILE_2 }}
```

the -z specifies here gzip as target compression
extract the archive with

``` bash
tar -xzvf {{ SOURCE_ARCHIVE }} -C {{ PATH_TO_TARGET_FOLDER }}
```

-C specifies the target directory here.

Sources:

- <https://stackoverflow.com/questions/984204/shell-command-to-tar-directory-excluding-certain-files-folders>
- <https://www.howtogeek.com/248780/how-to-compress-and-extract-files-using-the-tar-command-on-linux/>

## How do I read permission and change it with chmod?

``` bash
chown {{ USER }}:{{ USER_GROUP }} {{ PATH_TO_FILE }}
chmod +x {{ PATH_TO_FILE }}
```

for recursive use -R

``` bash
chmod -R 755 {{ PATH_TO_FOLDER }}
chown -R {{ USER }}:{{ USER_GROUP }} {{ PATH_TO_FOLDER }}
```

In general, however, it is better to combine the whole thing with find, because then files do not get an x (execute).

``` bash
find {{ PATH_TO_FOLDER }} -type d -exec chmod 0750 {} \;
find {{ PATH_TO_FOLDER }} -type f -exec chmod 0640 {} \;
```

Sources:

- <https://www.linode.com/docs/tools-reference/tools/modify-file-permissions-with-chmod/>
- <https://help.ubuntu.com/community/FilePermissions>

## How can I find out which candidate versions of which packages are available from which sources?

Simply

``` bash
apt-cache policy {{ PACKAGE_NAME }}
```

## How to grab/stream stdin/stdout to a command?

Simply use the "-" to indicate where to stream the output of the previous command.

``` bash
cp -
```

## Create an SSH key

Then create your ssh key

``` bash
ssh-keygen -o -a 100 -t ed25519 -f ~/.ssh/{{ KEY_NAME }} -N ""
ssh-copy-id -o PreferredAuthentications=password -i ~/.ssh/{{ KEY_NAME }}.pub {{ USER }}@{{ HOST_IP }}
rm ~/.ssh/{{ KEY_NAME }}.pub
echo "Host {{ HOST_NAME }}
    Hostname SERVER
    Port 22
    user {{ USER_NAME }}
    IdentityFile ~/.ssh/{{ KEY_NAME }}
" >> ~/.ssh/config
```

Sources:

- <https://medium.com/risan/upgrade-your-ssh-key-to-ed25519-c6e8d60d3c54>
