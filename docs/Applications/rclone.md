# RClone

## How to use Rclone?

Use rclone to sync a folder to a remote

``` bash
rclone sync --transfers 4 --checkers 10 --delete-after --progress --stats 10s {{ SOURCE_FOLDER }} {{ REMOTE }}:{{ TARGET_FOLDER }}
```

Mount a remote folder

``` bash
rclone mount --allow-other {{ SOURCE_FOLDER }} {{ REMOTE }}:{{ TARGET_FOLDER }}
```

You can also use rclone to encrypt on the fly. Sync is then directed towards to crypt *remote* which points to the *real* remote.

Source:

- <https://rclone.org/crypt/>
- <https://www.andyibanez.com/posts/rclone-basics-encryption/>