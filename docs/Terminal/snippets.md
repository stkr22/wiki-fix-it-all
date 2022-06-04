# Terminal Snippets

## How do I create numbered folders with a loop?

``` bash
for i in $(seq 1 13);
  do mkdir session_$i; 
done
```

## My file names are too long, how can I fix that?

``` bash
find . -type f -exec rename 's/(.*\/)(.{20}).*(\.pdf)$/$1$2$3/' {} \;
```

## My filenames have special character, how to fix that?

``` bash
for file in ./*; do cd "$file"; rename 's/\W/_/g' *; rename 's/_+/_/g' *; cd {{ PATH_TO_FILES }}; done
find . -type f -exec rename 's/_mp3/.mp3/' {} \;
```

Sources:

- <https://unix.stackexchange.com/questions/216659/how-to-rename-all-files-with-special-characters-and-spaces-in-a-directory>

## How many files are in a folder?

``` bash
find . -type f | wc -l
```

Sources:

- <https://superuser.com/questions/198817/recursively-count-all-the-files-in-a-directory>

## What do I do if I accidentially deleted /etc/fstab?

``` bash
sudo blkid
```

choose mapping and :

``` bash
sudo mount -t ext4 -o rw,remount /dev/sdb2 /
```

Sources:

- <https://askubuntu.com/a/435966>

## How do I access the error logs of a particular service?

like:

``` bash
journalctl -u {{ NAME_OF_SERVICE }}
```

Sources:

- <https://www.loggly.com/ultimate-guide/using-journalctl/>

## How do I scan a Server/IP for open ports?

``` bash
sudo nmap -sT {{ SERVER_IP }}
```

Sources:

- <https://linuxize.com/post/check-open-ports-linux/#check-open-ports-with-nmap>

## How do I force ssh to use password authentication?

``` bash
ssh -o PreferredAuthentications=password
```

Sources:

- <https://unix.stackexchange.com/questions/15138/how-to-force-ssh-client-to-use-only-password-auth>

## How to watch the output of a command over time?

``` bash
watch -n 1 "kubectl top pod --namespace {{ NAMESPACE_NAME }} {{ POD_NAME }}"
```

Sources:

- <https://github.com/kubernetes/kubernetes/issues/52963#issuecomment-331781491>
