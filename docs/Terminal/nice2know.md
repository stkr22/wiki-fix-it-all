# Terminal Nice2Know

## How do I open an editor to input multiline commands?

use ++ctrl+x++ and ++ctrl+e++

Sources:

- <https://www.tecmint.com/linux-command-line-bash-shortcut-keys/>

## How to clear bash history completely?

However,

One annoying side-effect is that the history entries has a copy in the memory and it will flush back to the file when you log out.

To workaround this, use the following command (worked for me):

``` bash
cat /dev/null > ~/.bash_history && history -c && exit
```

Sources:

- <https://askubuntu.com/questions/191999/how-to-clear-bash-history-completely>
